package stepDefinitions; // Assuming this package based on your project structure

import io.cucumber.java.After;
import io.cucumber.java.Before;
import io.cucumber.java.Scenario;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;
import utils.BrowserUtility; // Import your BrowserUtility

public class Hooks {

    // Use ThreadLocal to ensure each thread gets its own WebDriver instance
    private static ThreadLocal<WebDriver> driverThread = new ThreadLocal<>();

    public static WebDriver getDriver() {
        return driverThread.get();
    }

    @Before
    public void setup(Scenario scenario) {
        // You can get the browser type from system properties or a config file
        // For now, let's hardcode for simplicity or use System.getProperty for flexibility
        String browser = System.getProperty("browser", "edge"); // Default to edge
        // In a real scenario, you might parse tags from the scenario to determine browser

        // Get a new WebDriver instance from BrowserUtility for THIS thread/scenario
        WebDriver driver = BrowserUtility.launchBrowser(browser); // Call the static factory method
        driverThread.set(driver);
        driver.manage().window().maximize();
        System.out.println("Starting scenario: " + scenario.getName() + " with browser: " + browser);
    }

    @After
    public void tearDown(Scenario scenario) {
        WebDriver driver = driverThread.get();
        if (driver != null) {
            if (scenario.isFailed()) {
                try {
                    // Take screenshot for failed scenarios
                    byte[] screenshot = ((TakesScreenshot) driver).getScreenshotAs(OutputType.BYTES);
                    scenario.attach(screenshot, "image/png", "Failed Screenshot");
                } catch (Exception e) {
                    System.err.println("Failed to take screenshot: " + e.getMessage());
                }
            }
            driver.quit(); // Quit the WebDriver instance for this thread
        }
        driverThread.remove(); // Clean up ThreadLocal
        System.out.println("Finished scenario: " + scenario.getName());
    }
}
