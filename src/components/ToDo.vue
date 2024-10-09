<template>
  <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" rel="stylesheet">
  <h1 class="title" >TODOS</h1>
        <div class="input_todo">
            <input type="text" v-model="inputValue" @keyup.enter="addItem" class="input-text" placeholder="Example: Learning English...">
            <button class="btn" v-on:click="addItem" >Add</button>
        </div>
        <div class="list_todo">
            <h3>Todo List</h3>
            <div class="sub-list">
                <div class="btns-filter">
                    <span>Filter:</span>
                    <button  class="btn-filter" :class="{ active: filterStatus === 'completed' }"  v-on:click="filterTaskComplete" >Task completed</button>
                    <button  class="btn-filter" :class="{ active: filterStatus === 'notCompleted' }"  v-on:click="filterTaskNotComplete">Task not complete</button>
                    <button  class="btn-filter" v-on:click="resetFilter">Reset</button>
                </div>
                <span class="success">Success: {{completedTasksCount}}/{{totalTask}}</span>

            </div>

            <ul class="list">
                <li class="item" v-for="(item, index) in listItems" :key="item.text" > 
                    <div class="item-content">
                        <input type="checkbox" v-model="item.checked" v-on:click="toggleChecked(item)">
                        <span :class="{checked: item.checked}">
                            {{item.text}}
                        </span>&nbsp;
                        <span v-if="item.checked" class="complete-text">(Completed)</span>
                        
                    </div>
                    <div class="button">
                        <!-- <span v-on:click="editItem(index)" >Sửa</span> -->
                        <span v-on:click="removeItem(index)">
                            <i class="fa-solid fa-xmark"></i>
                        </span>
                    </div>
                </li>
            </ul>

        </div>

        <span class="clear-task" :class="{disabled: !hasItemCompleted}" v-on:click="removeTaskCompleted()" >Clear all task completed</span>
</template>

<script>
import '../assets/style.css'; // Import CSS ở đây
import { ref, computed } from "vue";

export default {
  name: "ToDos",
  setup() {
    const inputValue = ref("");
    const originalItems = ref(JSON.parse(localStorage.getItem("listItems")) || []);
    const filterStatus = ref("all"); // Trạng thái lọc

    // Computed
    const listItems = computed(() => {
      if (filterStatus.value === "completed") {
        return originalItems.value.filter((item) => item.checked);
      } else if (filterStatus.value === "notCompleted") {
        return originalItems.value.filter((item) => !item.checked);
      }
      return originalItems.value; // Trả về tất cả các item nếu không có bộ lọc
    });

    const hasItemCompleted = computed(() => {
      return originalItems.value.some((item) => item.checked);
    });

    const completedTasksCount = computed(() => {
      return originalItems.value.filter((item) => item.checked).length;
    });

    const totalTask = computed(() => {
      return originalItems.value.length;
    });

    // Methods
    const addItem = () => {
      if (inputValue.value.trim() !== "") {
        originalItems.value.push({ text: inputValue.value, checked: false });
        inputValue.value = "";
        saveTodos();
      }
    };

    const removeItem = (index) => {
      originalItems.value.splice(index, 1);
      saveTodos();
    };

    const removeTaskCompleted = () => {
      originalItems.value = originalItems.value.filter((item) => !item.checked);
      saveTodos();
    };

    const filterTaskNotComplete = () => {
      filterStatus.value = "notCompleted";
    };

    const filterTaskComplete = () => {
      filterStatus.value = "completed";
    };

    const resetFilter = () => {
      filterStatus.value = "all";
    };

    const toggleChecked = (item) => {
      item.checked = !item.checked; // Đảo trạng thái checked của item đã nhấn
      saveTodos();
    };

    const saveTodos = () => {
      localStorage.setItem("listItems", JSON.stringify(originalItems.value));
    };

    return {
      //Biến trả về
      inputValue,
      originalItems,
      filterStatus,
      listItems,
      hasItemCompleted,
      completedTasksCount,
      totalTask,
      //phương thức trả về
      addItem,
      removeItem,
      removeTaskCompleted,
      filterTaskNotComplete,
      filterTaskComplete,
      resetFilter,
      toggleChecked,
    };
  },
};
</script>