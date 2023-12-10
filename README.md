# TaskMaster-Pro

Description: TaskMaster Pro is an advanced ToDo List application that allows users to manage tasks with features such as task name, category, priority levels, and the ability to mark tasks as completed. The application utilizes HTML, CSS, Bootstrap for styling, and JavaScript for dynamic functionality. Tasks are stored in the browser's localStorage for persistence.

Code Explanation:
HTML (index.html):
The HTML file contains the structure of the webpage, including a form to add tasks and a list to display tasks.

html
Copy code
<!-- Form to add tasks -->
<form id="taskForm">
    <!-- Task Name Input -->
    <div class="form-group">
        <label for="taskName">Task Name</label>
        <input type="text" class="form-control" id="taskName" required>
    </div>
    
    <!-- Category Input -->
    <div class="form-group">
        <label for="category">Category</label>
        <input type="text" class="form-control" id="category">
    </div>
    
    <!-- Priority Select -->
    <div class="form-group">
        <label for="priority">Priority</label>
        <select class="form-control" id="priority">
            <option value="low">Low</option>
            <option value="medium">Medium</option>
            <option value="high">High</option>
        </select>
    </div>
    
    <!-- Submit Button -->
    <button type="submit" class="btn btn-primary">Add Task</button>
</form>

<!-- Task List -->
<div class="row mt-4">
    <div class="col-md-6">
        <!-- Task items will be dynamically added here -->
        <ul id="taskList" class="list-group"></ul>
    </div>
</div>
CSS (styles/style.css):
The CSS file provides styling for a cleaner and more visually appealing interface.

css
Copy code
body {
    background-color: #f8f9fa;
}

.container {
    background-color: #ffffff;
    padding: 30px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    margin-top: 50px;
}

h1 {
    color: #007bff;
}

form {
    margin-bottom: 20px;
}

#taskList {
    list-style: none;
    padding: 0;
}

.task-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    margin-bottom: 10px;
    background-color: #ffffff;
    border: 1px solid #dee2e6;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.task-item:hover {
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.task-item span {
    flex-grow: 1;
    margin-right: 10px;
}

.task-item button {
    margin-left: 10px;
}
JavaScript (scripts/script.js):
JavaScript adds dynamic functionality to the ToDo List, handling form submissions, task additions, removals, and storage.

javascript
Copy code
document.addEventListener('DOMContentLoaded', function () {
    // Selecting DOM elements
    const taskForm = document.getElementById('taskForm');
    const taskNameInput = document.getElementById('taskName');
    const categoryInput = document.getElementById('category');
    const priorityInput = document.getElementById('priority');
    const taskList = document.getElementById('taskList');

    // Event listener for form submission
    taskForm.addEventListener('submit', function (event) {
        event.preventDefault();

        // Get values from form inputs
        const taskName = taskNameInput.value.trim();
        const category = categoryInput.value.trim();
        const priority = priorityInput.value;

        // Validate task name
        if (taskName === '') {
            alert('Please enter a task name.');
            return;
        }

        // Add task to the list
        addTask(taskName, category, priority);
        taskForm.reset();
    });

    // Function to add task dynamically
    function addTask(taskName, category, priority) {
        const taskItem = document.createElement('li');
        taskItem.classList.add('list-group-item', 'task-item');

        const taskContent = `
            <span>
                <strong>${taskName}</strong>
                ${category ? ` - ${category}` : ''}
            </span>
            <span class="badge badge-${getPriorityBadgeClass(priority)}">${priority}</span>
            <button class="btn btn-danger btn-sm" onclick="removeTask(this)">Remove</button>
        `;

        taskItem.innerHTML = taskContent;
        taskList.appendChild(taskItem);

        // Save tasks to localStorage
        saveTasksToStorage();
    }

    // Function to remove task
    function removeTask(button) {
        const taskItem = button.closest('.task-item');
        taskItem.remove();

        // Save tasks to localStorage
        saveTasksToStorage();
    }

    // Function to save tasks to localStorage
    function saveTasksToStorage() {
        const tasks = [];
        const taskItems = document.querySelectorAll('.task-item');

        taskItems.forEach(taskItem => {
            const task = {
                name: taskItem.querySelector('strong').textContent,
                category: taskItem.querySelector('span:nth-child(1)').textContent.trim().split(' - ')[1] || '',
                priority: taskItem.querySelector('.badge').textContent,
            };

            tasks.push(task);
        });

        localStorage.setItem('tasks', JSON.stringify(tasks));
    }

    // Function to get priority badge class
    function getPriorityBadgeClass(priority) {
        switch (priority.toLowerCase()) {
            case 'low':
                return 'success';
            case 'medium':
                return 'warning';
            case 'high':
                return 'danger';
            default:
                return 'secondary';
        }
    }

    // Function to load tasks from localStorage
    function loadTasksFromStorage() {
        const storedTasks = localStorage.getItem('tasks');

        if (storedTasks) {
            const tasks = JSON.parse(storedTasks);

            tasks.forEach(task => {
                addTask(task.name, task.category, task.priority);
            });
        }
    }

    // Load tasks from localStorage when the page is loaded
    loadTasksFromStorage();
});
