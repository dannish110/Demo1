Perfect —
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

