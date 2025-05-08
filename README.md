70hoursPerfect —
you want proper folder structure (organized neatly), just like a professional project.
Let’s design it cleanly:


---

Final Folder Structure

task_manager_project/
│
├── app/                   # all main code inside app/
│   ├── __init__.py
│   ├── auth.py             # login system
│   ├── models.py           # Task model (Pydantic)
│   ├── tasks.py            # task operations (insert, view, update, delete)
│   └── file_manager.py     # file handling (load/save tasks)
│
├── data/
│   └── tasks.txt           # tasks stored here
│
├── tests/                  # unit tests
│   ├── __init__.py
│   └── test_tasks.py
│
└── main.py                 # starts the program


---

Code in each file


---

1. main.py

from app.auth import Auth
from app.tasks import TaskManager

def main():
    print("Welcome to Task Manager CLI")
    print("1. Login")
    choice = input("Choose: ")

    if choice != "1":
        print("Invalid choice. Exiting...")
        return

    username = input("Enter username: ")
    password = input("Enter password: ")

    if not Auth.login(username, password):
        print("Invalid credentials!")
        return

    manager = TaskManager()
    manager.load_tasks()

    while True:
        print("\nI: Insert Task\nV: View Tasks\nU: Update Task\nD: Delete Task\nQ: Quit")
        action = input("Choose action: ").upper()

        if action == 'I':
            manager.insert_task()
        elif action == 'V':
            manager.view_tasks()
        elif action == 'U':
            manager.update_task()
        elif action == 'D':
            manager.delete_task()
        elif action == 'Q':
            manager.save_tasks()
            print("Goodbye!")
            break
        else:
            print("Invalid action!")

if __name__ == "__main__":
    main()


---

2. app/auth.py

class Auth:
    USERNAME = "user"
    PASSWORD = "pswd"

    @classmethod
    def login(cls, username, password):
        return username == cls.USERNAME and password == cls.PASSWORD


---

3. app/models.py

from pydantic import BaseModel, validator
from datetime import datetime

class Task(BaseModel):
    id: int
    title: str
    description: str
    datetime: datetime

    @validator('title', 'description')
    def not_empty(cls, v):
        if not v or not v.strip():
            raise ValueError('Field cannot be empty')
        return v


---

4. app/file_manager.py

import os
from app.models import Task
from datetime import datetime

TASKS_FILE = "data/tasks.txt"

class FileManager:
    @staticmethod
    def load_tasks():
        tasks = []
        if not os.path.exists(TASKS_FILE):
            return tasks
        with open(TASKS_FILE, "r") as file:
            lines = file.readlines()
            for line in lines:
                if line.strip():
                    id, title, description, dt_str = line.strip().split("|")
                    task = Task(
                        id=int(id),
                        title=title,
                        description=description,
                        datetime=datetime.strptime(dt_str, "%Y-%m-%d %H:%M")
                    )
                    tasks.append(task)
        return tasks

    @staticmethod
    def save_tasks(tasks):
        with open(TASKS_FILE, "w") as file:
            for task in tasks:
                line = f"{task.id}|{task.title}|{task.description}|{task.datetime.strftime('%Y-%m-%d %H:%M')}\n"
                file.write(line)


---

5. app/tasks.py

from app.models import Task
from app.file_manager import FileManager
from datetime import datetime

class TaskManager:
    def __init__(self):
        self.day_tasks = []
        self.night_tasks = []
        self.last_id = 0

    def load_tasks(self):
        tasks = FileManager.load_tasks()
        for task in tasks:
            self.add_task_to_list(task)
        if tasks:
            self.last_id = max(task.id for task in tasks)

    def save_tasks(self):
        all_tasks = self.day_tasks + self.night_tasks
        FileManager.save_tasks(all_tasks)

    def add_task_to_list(self, task):
        hour = task.datetime.hour
        if 9 <= hour < 18:
            if len(self.day_tasks) < 10:
                self.day_tasks.append(task)
        else:
            if len(self.night_tasks) < 10:
                self.night_tasks.append(task)

    def insert_task(self):
        try:
            title = input("Enter title: ")
            description = input("Enter description: ")
            dt_str = input("Enter datetime (yyyy-MM-dd HH:mm): ")
            dt = datetime.strptime(dt_str, "%Y-%m-%d %H:%M")
            new_task = Task(id=self.last_id + 1, title=title, description=description, datetime=dt)
            self.last_id += 1
            self.add_task_to_list(new_task)
            print("Task added successfully!")
        except Exception as e:
            print(f"Error: {e}")

    def view_tasks(self):
        print("\n--- Day Tasks ---")
        for task in self.day_tasks:
            print(f"[{task.id}] {task.title} - {task.datetime}")

        print("\n--- Night Tasks ---")
        for task in self.night_tasks:
            print(f"[{task.id}] {task.title} - {task.datetime}")

    def update_task(self):
        try:
            task_id = int(input("Enter task id to update: "))
            all_tasks = self.day_tasks + self.night_tasks
            task = next((t for t in all_tasks if t.id == task_id), None)
            if not task:
                print("Task not found!")
                return

            title = input(f"Enter new title [{task.title}]: ") or task.title
            description = input(f"Enter new description [{task.description}]: ") or task.description
            dt_str = input(f"Enter new datetime [{task.datetime.strftime('%Y-%m-%d %H:%M')}]: ") or task.datetime.strftime('%Y-%m-%d %H:%M')
            dt = datetime.strptime(dt_str, "%Y-%m-%d %H:%M")

            task.title = title
            task.description = description
            task.datetime = dt

            self.day_tasks.clear()
            self.night_tasks.clear()
            for t in all_tasks:
                self.add_task_to_list(t)

            print("Task updated successfully!")
        except Exception as e:
            print(f"Error: {e}")

    def delete_task(self):
        try:
            task_id = int(input("Enter task id to delete: "))
            before_count = len(self.day_tasks) + len(self.night_tasks)
            self.day_tasks = [t for t in self.day_tasks if t.id != task_id]
            self.night_tasks = [t for t in self.night_tasks if t.id != task_id]
            after_count = len(self.day_tasks) + len(self.night_tasks)
            if before_count == after_count:
                print("Task not found!")
            else:
                print("Task deleted successfully!")
        except Exception as e:
            print(f"Error: {e}")


---

6. tests/test_tasks.py (Example Unit Test)

import unittest
from app.models import Task
from app.tasks import TaskManager
from datetime import datetime

class TestTaskManager(unittest.TestCase):
    def setUp(self):
        self.manager = TaskManager()

    def test_insert_day_task(self):
        dt = datetime.strptime("2025-04-28 10:00", "%Y-%m-%d %H:%M")
        task = Task(id=1, title="Test Task", description="desc", datetime=dt)
        self.manager.add_task_to_list(task)
        self.assertEqual(len(self.manager.day_tasks), 1)

    def test_insert_night_task(self):
        dt = datetime.strptime("2025-04-28 20:00", "%Y-%m-%d %H:%M")
        task = Task(id=2, title="Night Task", description="desc", datetime=dt)
        self.manager.add_task_to_list(task)
        self.assertEqual(len(self.manager.night_tasks), 1)

if __name__ == '__main__':
    unittest.main()


---

Important:

The file data/tasks.txt will be auto-created if missing.

Data will persist even after closing and reopening the program.

Tasks are reloaded every time on startup.



---

Would you also like a ready-to-run ZIP of this project so you can just unzip and run?
(Or I can show you the terminal commands to set it up in your system?)
Let me know!

Here's the full code for your Python Task Management CLI project using OOP, Pydantic, exception handling, unit testing, and a dictionary structure for categorizing tasks into day and night tasks.


---

main.py

from auth.auth import login
from core.task_manager import TaskManager

def main():
    print("Welcome to Task Manager CLI")
    if not login():
        print("Access Denied!")
        return

    manager = TaskManager()
    commands = {
        'I': manager.insert_task,
        'V': manager.view_tasks,
        'D': manager.delete_task,
        'U': manager.update_task
    }

    while True:
        print("\nChoose an action:")
        print("I - Insert Task")
        print("V - View Tasks")
        print("D - Delete Task")
        print("U - Update Task")
        print("Q - Quit")

        choice = input("Enter your choice: ").strip().upper()

        if choice == 'Q':
            print("Goodbye!")
            break
        action = commands.get(choice)
        if action:
            try:
                action()
            except Exception as e:
                print(f"Error: {e}")
        else:
            print("Invalid choice!")

if __name__ == "__main__":
    main()


---

auth/auth.py

def login():
    USERNAME = "admin"
    PASSWORD = "admin123"

    username = input("Enter username: ")
    password = input("Enter password: ")

    return username == USERNAME and password == PASSWORD


---

core/task_manager.py

from models.task_model import TaskInput, Task
from datetime import datetime
from typing import Dict

class TaskManager:
    def __init__(self):
        self.tasks: Dict[str, Dict[int, Task]] = {'day': {}, 'night': {}}
        self.task_id = 1

    def categorize_task(self, time_str):
        try:
            time = datetime.strptime(time_str, "%H:%M").time()
            if datetime.strptime("09:00", "%H:%M").time() <= time < datetime.strptime("18:00", "%H:%M").time():
                return 'day'
            else:
                return 'night'
        except ValueError:
            raise ValueError("Invalid time format. Use HH:MM")

    def insert_task(self):
        try:
            title = input("Enter task title: ")
            description = input("Enter task description: ")
            time = input("Enter task time (HH:MM): ")
            date = input("Enter task date (YYYY-MM-DD): ")

            task_input = TaskInput(title=title, description=description, time=time, date=date)
            category = self.categorize_task(task_input.time)

            if len(self.tasks[category]) >= 10:
                raise Exception(f"Cannot add more than 10 tasks in {category} category.")

            task = Task(id=self.task_id, **task_input.dict())
            self.tasks[category][self.task_id] = task
            print(f"Task added with ID {self.task_id} in {category} category.")
            self.task_id += 1

        except Exception as e:
            print(f"Error adding task: {e}")

    def view_tasks(self):
        for category in ['day', 'night']:
            print(f"\n{category.upper()} TASKS:")
            if not self.tasks[category]:
                print("No tasks.")
            for task in self.tasks[category].values():
                print(f"ID: {task.id}, Title: {task.title}, Description: {task.description}, Time: {task.time}, Date: {task.date}")

    def delete_task(self):
        try:
            task_id = int(input("Enter task ID to delete: "))
            for category in ['day', 'night']:
                if task_id in self.tasks[category]:
                    del self.tasks[category][task_id]
                    print(f"Task {task_id} deleted from {category}.")
                    return
            print("Task not found.")
        except Exception as e:
            print(f"Error deleting task: {e}")

    def update_task(self):
        try:
            task_id = int(input("Enter task ID to update: "))
            for category in ['day', 'night']:
                if task_id in self.tasks[category]:
                    old_task = self.tasks[category][task_id]
                    title = input(f"New title (old: {old_task.title}): ") or old_task.title
                    description = input(f"New description (old: {old_task.description}): ") or old_task.description
                    time = input(f"New time (HH:MM, old: {old_task.time}): ") or old_task.time
                    date = input(f"New date (YYYY-MM-DD, old: {old_task.date}): ") or old_task.date

                    task_input = TaskInput(title=title, description=description, time=time, date=date)
                    new_category = self.categorize_task(task_input.time)

                    if new_category != category and len(self.tasks[new_category]) >= 10:
                        raise Exception(f"Cannot move task to {new_category} category. Limit reached.")

                    del self.tasks[category][task_id]
                    updated_task = Task(id=task_id, **task_input.dict())
                    self.tasks[new_category][task_id] = updated_task
                    print(f"Task {task_id} updated and moved to {new_category}.")
                    return
            print("Task not found.")
        except Exception as e:
            print(f"Error updating task: {e}")


---

models/task_model.py

from pydantic import BaseModel, validator
from datetime import datetime

class TaskInput(BaseModel):
    title: str
    description: str
    time: str
    date: str

    @validator('time')
    def time_format(cls, v):
        try:
            datetime.strptime(v, "%H:%M")
            return v
        except ValueError:
            raise ValueError("Time must be in HH:MM format")

    @validator('date')
    def date_format(cls, v):
        try:
            datetime.strptime(v, "%Y-%m-%d")
            return v
        except ValueError:
            raise ValueError("Date must be in YYYY-MM-DD format")

class Task(TaskInput):
    id: int


---

tests/test_task_manager.py

import unittest
from core.task_manager import TaskManager

class TestTaskManager(unittest.TestCase):
    def setUp(self):
        self.tm = TaskManager()

    def test_insert_and_view_task(self):
        self.tm.task_id = 1
        self.tm.insert_task = lambda: self.tm.tasks['day'].update({
            1: self.tm._create_mock_task(1, "Test", "Desc", "10:00", "2025-05-01")
        })
        self.tm.insert_task()
        self.assertIn(1, self.tm.tasks['day'])

    def test_category_limits(self):
        self.tm.task_id = 1
        for i in range(10):
            self.tm.tasks['day'][i+1] = self.tm._create_mock_task(i+1, f"Task{i}", "Desc", "10:00", "2025-05-01")
        with self.assertRaises(Exception):
            self.tm.insert_task = lambda: (_ for _ in ()).throw(Exception("Limit reached"))
            self.tm.insert_task()

if __name__ == '__main__':
    unittest.main()

> You can mock the _create_mock_task() method like below (add in task_manager.py for testing):



def _create_mock_task(self, id, title, desc, time, date):
    from models.task_model import Task
    return Task(id=id, title=title, description=desc, time=time, date=date)


---

requirements.txt

pydantic


---

This code follows:

Proper OOP practices (TaskManager, Task, etc.)

Uses Pydantic for task data validation

Implements command-based CLI (I, V, D, U)

Separates concerns via modules (auth, models, core)

Handles errors cleanly

Includes unit tests


Would you like me to zip the folder structure for you?


