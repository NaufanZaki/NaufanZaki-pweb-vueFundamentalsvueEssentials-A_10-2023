<template>
  <div class="todo-app">
    <h1>To-Do List</h1>
    
    <div class="input-row">
        <div class="input-container">
          <input v-model="task" @keyup.enter="addTask" placeholder="Add a task" />
          <input v-model="category" placeholder="Category" />
        </div>
    </div>
    
    <div class="button-container">
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
      </li>
    </ul>

    <!-- Edit Modal -->
    <EditModal
      v-if="editingIndex !== null"
      :taskText="editingTask"
      @save="updateTask"
      @close="closeEditModal"
    />
  </div>
</template>

<script>
import EditModal from "../components/EditModal.vue";

export default {
  components: {
    EditModal,
  },
  data() {
  return {
    task: "",
    tasks: [],
    editingIndex: null,
    editingTask: "",
    categories: ["All"],
    selectedCategory: "All",
    category: "", // Initialize an empty category field
    selectedCategoryFilter: "All", // Initialize with "All" as the default category filter
  };
},

  computed: {
  filteredTasks() {
    if (this.selectedCategoryFilter === "All") {
      return this.tasks;
    } else {
      return this.tasks.filter(task => task.category === this.selectedCategoryFilter);
    }
  }
},


  
  methods: {
    addTask() {
      if (this.task.trim() !== "") {
        const newCategory = this.category.trim() || "Uncategorized"; // Use "Uncategorized" if no category is provided
        this.tasks.push({ text: this.task, completed: false, category: newCategory });
        this.task = "";
        this.category = ""; // Clear the category input
        this.updateCategories();
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
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
      }
    },
    updateCategories() {
      const uniqueCategories = Array.from(new Set(this.tasks.map(task => task.category)));
      this.categories = ["All", ...uniqueCategories];
    },
  },
 

};
</script>
<style scoped>
.todo-app {
  scale: 100%;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px;
  border-radius: 5px;
  box-sizing: border-box;
  align-self: center;
}

h1 {
  margin-bottom: 20px;
  text-align: center;
}

.input-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 20px;
  padding: 10px;
  width: 100%;
}

input[type="text"] {
  flex: 1;
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 3px;
}

.button-container {
  display: flex;
  justify-content: center;
  margin-bottom: 15px;
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

.filter-container {
  margin-bottom: 20px;
}

.filter-container label {
  margin-right: 10px;
}

</style>