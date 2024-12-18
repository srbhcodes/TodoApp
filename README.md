# Full-Stack To-Do Application with React, Node.js, and MongoDB
---

## **Steps and Explanations**

### **1. Setup WSL Ubuntu Environment**

#### **Why?**
To use a Linux environment for development, as it’s widely used for backend applications and provides flexibility for installing and managing Node.js, MongoDB, and other tools.

#### **Steps:**
1. Ensure WSL is installed and set to use Ubuntu.
   ```bash
   wsl --install -d Ubuntu
   ```

2. Open Ubuntu and update the package list:
   ```bash
   sudo apt update && sudo apt upgrade
   ```

---

### **2. Install Necessary Software**

#### **Why?**
To build and run the full-stack application, the following tools are required:
- **Node.js**: For running JavaScript code on the backend.
- **MongoDB**: To store and retrieve tasks.
- **npm**: To manage dependencies for both backend and frontend.

#### **Steps:**
1. Install Node.js and npm:
   ```bash
   sudo apt install nodejs npm
   ```
   Verify installation:
   ```bash
   node -v
   npm -v
   ```

2. Install MongoDB:
   ```bash
   sudo apt install -y mongodb
   ```
   Start MongoDB:
   ```bash
   sudo service mongod start
   ```

---

### **3. Create Project Directory Structure**

#### **Why?**
Organizing the project into client (frontend) and server (backend) directories ensures a clear separation of concerns.

#### **Steps:**
1. Create a project folder:
   ```bash
   mkdir ToDoApp && cd ToDoApp
   ```
2. Create `server` and `client` subdirectories:
   ```bash
   mkdir server client
   ```

---

### **4. Build the Backend**

#### **Why?**
To handle requests from the frontend, interact with the database, and manage APIs for tasks.

#### **Steps:**
1. Navigate to the `server` folder:
   ```bash
   cd server
   ```

2. Initialize a new Node.js project:
   ```bash
   npm init -y
   ```

3. Install required dependencies:
   ```bash
   npm install express mongoose cors
   ```

4. Create a `server.js` file:
   ```bash
   touch server.js
   ```

5. Add the following code to `server.js`:
   ```javascript
   const express = require('express');
   const mongoose = require('mongoose');
   const cors = require('cors');

   const app = express();

   app.use(cors());
   app.use(express.json());

   mongoose.connect('mongodb://localhost/todolist', {
       useNewUrlParser: true,
       useUnifiedTopology: true,
   });

   const taskSchema = new mongoose.Schema({
       task: String,
       completed: Boolean,
   });

   const Task = mongoose.model('Task', taskSchema);

   app.get('/tasks', async (req, res) => {
       const tasks = await Task.find();
       res.json(tasks);
   });

   app.post('/tasks', async (req, res) => {
       const newTask = new Task(req.body);
       await newTask.save();
       res.json(newTask);
   });

   app.delete('/tasks/:id', async (req, res) => {
       await Task.findByIdAndDelete(req.params.id);
       res.send('Task deleted');
   });

   app.listen(5000, () => {
       console.log('Server running on http://localhost:5000');
   });
   ```

6. Start the backend server:
   ```bash
   node server.js
   ```

---

### **5. Build the Frontend**

#### **Why?**
To provide a user interface for adding, viewing, and deleting tasks.

#### **Steps:**
1. Navigate to the `client` folder:
   ```bash
   cd ../client
   ```

2. Create a React app:
   ```bash
   npx create-react-app .
   ```

3. Install Axios for API requests:
   ```bash
   npm install axios
   ```

4. Replace the default `App.js` with the following code:
   ```javascript
   import React, { useState, useEffect } from 'react';
   import axios from 'axios';

   const App = () => {
       const [tasks, setTasks] = useState([]);
       const [task, setTask] = useState('');

       useEffect(() => {
           axios.get('http://localhost:5000/tasks')
               .then(res => setTasks(res.data))
               .catch(err => console.log(err));
       }, []);

       const addTask = () => {
           axios.post('http://localhost:5000/tasks', { task, completed: false })
               .then(res => setTasks([...tasks, res.data]))
               .catch(err => console.log(err));
           setTask('');
       };

       const deleteTask = (id) => {
           axios.delete(`http://localhost:5000/tasks/${id}`)
               .then(() => setTasks(tasks.filter(task => task._id !== id)))
               .catch(err => console.log(err));
       };

       return (
           <div>
               <h1>To-Do List</h1>
               <input
                   type="text"
                   value={task}
                   onChange={(e) => setTask(e.target.value)}
                   placeholder="Add a new task"
               />
               <button onClick={addTask}>Add Task</button>
               <ul>
                   {tasks.map(task => (
                       <li key={task._id}>
                           {task.task}
                           <button onClick={() => deleteTask(task._id)}>Delete</button>
                       </li>
                   ))}
               </ul>
           </div>
       );
   };

   export default App;
   ```

5. Start the React app:
   ```bash
   npm start
   ```

---

### **6. Verify Data in MongoDB**

#### **Why?**
To confirm tasks are being stored correctly in the database.

#### **Steps:**
1. Start the MongoDB service:
   ```bash
   sudo service mongod start
   ```

2. Access the MongoDB shell:
   ```bash
   mongosh
   ```

3. Switch to the `todolist` database:
   ```bash
   use todolist
   ```

4. View tasks in the `tasks` collection:
   ```bash
   db.tasks.find().pretty()
   ```

---

### **7. Track Changes Using Git**

#### **Why?**
To maintain version control and ensure progress can be tracked or reverted if necessary.

#### **Steps:**
1. Initialize Git in the project:
   ```bash
   git init
   ```

2. Add and commit files:
   ```bash
   git add .
   git commit -m "Initial commit: Built To-Do application with React, Node.js, and MongoDB"
   ```

---

### **Final Output**
- **Backend**: API running at `http://localhost:5000`.
- **Frontend**: React app accessible at `http://localhost:3000`.
- **Database**: Tasks stored and verified in MongoDB.

