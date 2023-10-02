<template>
  <div class="todo-app">
    <h1>To-Do List</h1>
  
    <div class="input-container">
      <input v-model="task" @keyup.enter="addTask" placeholder="Add a task" />
      <input v-model="category" placeholder="Category" />
      <button @click="addTask">Add</button>
    </div>

    <div class="filter-container">
      <label for="categoryFilter">Filter by Category:</label>
      <select v-model="selectedCategoryFilter" id="categoryFilter">
        <option value="All">All</option>
        <option v-for="category in categories" :value="category">{{ category }}</option>
      </select>
    </div>

    <ul>
      <li v-for="(item, index) in filteredTasks" :key="index" class="task">
        <input type="checkbox" v-model="item.completed" />
        <span :class="{ completed: item.completed }">{{ item.text }}</span>
        <button @click="removeTask(index)" class="delete-button">Delete</button>
        <button @click="openEditModal(index)" class="edit-button">Edit</button>
        <button @click="openTaskDetail(index)" class="detail-button">Detail</button>
      </li>
    </ul>

    <!-- TaskDetailPopup -->
    <TaskDetailPopup
      :task="selectedTask"
      ref="taskDetailPopup"
      @close="closeTaskDetailPopup"
      v-if="selectedTask !== null"
    />
    
    <!-- Edit Modal -->
    <EditModal
      v-if="editingIndex !== null"
      :taskText="editingTask"
      :tasks="tasks" 
      @save="updateTask"
      @close="closeEditModal"
    />
  </div>
</template>

<script>
import EditModal from "../components/EditModal.vue";
import TaskDetailPopup from "../components/TaskDetailPopup.vue";

export default {
  components: {
    EditModal,
    TaskDetailPopup,
  },
  data() {
    return {
      task: "",
      tasks: [],
      editingIndex: null,
      editingTask: "",
      categories: ["All"],
      selectedCategoryFilter: "All",
      selectedTask: null,
    };
  },
  computed: {
    filteredTasks() {
      if (this.selectedCategoryFilter === "All") {
        return this.tasks;
      } else {
        return this.tasks.filter(task => task.category === this.selectedCategoryFilter);
      }
    },
  },
  methods: {
    addTask() {
      if (this.task.trim() !== "") {
        const newCategory = this.category.trim() || "Uncategorized";
        const newId = this.tasks.length + 1;
        this.tasks.push({ id: newId, text: this.task, completed: false, category: newCategory });
        this.saveTasksToLocalStorage();
        this.task = "";
        this.category = "";
        this.updateCategories();
        this.selectedTask = null;
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasksToLocalStorage();
    },
    openEditModal(index) {
      this.editingIndex = index;
      this.editingTask = this.tasks[index].text;
    },
    closeEditModal() {
      this.editingIndex = null;
      this.editingTask = "";
    },
    updateTask(editedText) {
      if (this.editingIndex !== null) {
        this.tasks[this.editingIndex].text = editedText;
        this.closeEditModal();
        this.saveTasksToLocalStorage();
      }
    },
    openTaskDetail(index) {
      this.selectedTask = this.tasks[index];
    },
    closeTaskDetailPopup() {
      this.selectedTask = null;
    },
    updateCategories() {
      const uniqueCategories = Array.from(new Set(this.tasks.map(task => task.category)));
      this.categories = ["All", ...uniqueCategories];
    },
    saveTasksToLocalStorage() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
  },
};
</script>

<style scoped>
.todo-app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding: 0px;
  border-radius: 5px;
  box-sizing: border-box;
  align-self: center;
}

h1 {
  font-size: 24px;
  margin-bottom: 20px;
  text-align: center;
}

.input-container {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  padding: 100px;
}

input[type="text"] {
  flex: 1;
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 3px;
  margin-right: 10px;
}

button {
  background-color: #007BFF;
  color: #fff;
  border: none;
  border-radius: 3px;
  padding: 8px 16px;
  font-size: 16px;
  cursor: pointer;
}

ul {
  list-style: none;
  padding: 0;
}

.task {
  display: flex;
  
  align-items: center;
  margin-bottom: 10px;
}

input[type="checkbox"] {
  margin-right: 10px;
}

span {
  flex: 1;
  font-size: 16px;
  /* Add margin or padding for spacing between text and buttons */
  margin-right: 10px; /* Adjust this value as needed */
}

.delete-button {
  background-color: #ff3333;
  color: #fff;
  border: none;
  border-radius: 3px;
  padding: 5px 10px;
  font-size: 12px;
}

.edit-button {
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 3px;
  padding: 5px 10px;
  font-size: 12px;
  cursor: pointer;
}

.detail-button {
  background-color: #007BFF;
  color: #fff;
  border: none;
  border-radius: 3px;
  padding: 5px 10px;
  font-size: 12px;
}

.filter-container {
  margin-bottom: 20px;
}

.filter-container label {
  margin-right: 10px;
}

</style>