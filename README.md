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

package utils;

import io.github.bonigarcia.wdm.WebDriverManager;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.edge.EdgeOptions; // Import EdgeOptions
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions; // Import ChromeOptions
import java.io.IOException;

public class BrowserUtility {

    // This class should NOT have a WebDriver field like 'public WebDriver driver;'
    // It should just provide a static method to create a driver.

    // Remove the constructor `public BrowserUtility(WebDriver driver)` as it's no longer needed in this pattern

    // Your existing utility methods will need to get the WebDriver from the Hooks class
    // For example, in your step definitions, instead of this.driver, you'd use Hooks.getDriver()

    public static WebDriver launchBrowser(String browser) {
        WebDriver driver = null; // Initialize to null
        // Removed manual System.setProperty for driver paths, as WebDriverManager handles this.
        // If you absolutely need to use specific driver paths, consider passing them as parameters
        // or reading from a config file, but WebDriverManager is highly recommended.

        switch (browser.toLowerCase()) {
            case "chrome":
                WebDriverManager.chromedriver().setup();
                ChromeOptions chromeOptions = new ChromeOptions();
                // Add default options for headless if needed, will be refined for headless later
                // chromeOptions.addArguments("--headless=new"); // Example for headless
                // chromeOptions.addArguments("--window-size=1920,1080");
                driver = new ChromeDriver(chromeOptions);
                break;
            case "edge":
                WebDriverManager.edgedriver().setup();
                EdgeOptions edgeOptions = new EdgeOptions();
                // Add default options for headless if needed, will be refined for headless later
                // edgeOptions.addArguments("--headless=new"); // Example for headless
                // edgeOptions.addArguments("--window-size=1920,1080");
                driver = new EdgeDriver(edgeOptions);
                break;
            // Add other browsers as needed
            default:
                throw new IllegalArgumentException("Browser " + browser + " is not supported.");
        }

        // Your URL loading and maximizing should happen in Hooks, not here.
        // This method should *only* return a WebDriver instance.
        return driver;
    }

    // Your utility methods (clickMenu, gettext, enterValueInTextbox, etc.)
    // will now need to access the WebDriver from the Hooks class.
    // Example:
    /*
    public void clickMenu(By locator) {
        WebDriver driver = Hooks.getDriver(); // Get the WebDriver for the current thread
        if (driver != null) {
            WebElement menu = new WebDriverWait(driver, 10).until(ExpectedConditions.elementToBeClickable(locator));
            menu.click();
        } else {
            throw new IllegalStateException("WebDriver is not initialized for the current thread.");
        }
    }
    // Apply this pattern to all your utility methods.
    */

    // This method is now likely obsolete if you manage URL in hooks.
    // protected String getProperties(String url) { ... }
    // If you still need to load properties, make it a separate static method.
}
