<template>
  <div class="text-center">
    <h1>Homepage</h1>
    <p>addTask: {{ addTask }}</p>
    <p>dappName{{ dappName }}</p>
    <div v-for="task in allTasks" :key="task">
      <p>all Tasks: {{ task }}</p>
    </div>
    <p>all Tasks: {{ allTasks }}</p>

    <p>INdex {{ byIndex }}</p>
    <p>Status{{ taskStatus }}</p>
    <p>taskslenght:{{ tasksLength }}</p>
    <p>ToggleStatus {{ toggleStatus }}</p>
  </div>

  <div class="wrapper">
    <div id="myDIV" class="header">
      <h2 style="margin: 5px">To Do List</h2>
      <input type="text" id="myInput" placeholder="My Work..." />
      <span onclick="newElement()" class="addBtn">Add</span>
    </div>
    <ul id="myUL">
      <div v-for="task in allTasks" :key="task">
        <li>{{ task }}</li>
      </div>
    </ul>
  </div>
</template>

<script lang="ts">
// creeate method addTask()

import { ethers } from "ethers";
import { useEthers, displayEther } from "vue-dapp";
import TodoListAbi from "../abi/TodoListAbi.json";
export default {
  name: "Home",
  created() {
    this.contractAddNewTask();
    this.contractDappName();
    this.contractAllTasks();
    this.contractTaskByIndex();
    this.contractTaskStatus();
    this.contractTasksLength();
    this.contractToggleTaskStatus();
  },
  data() {
    return {
      addTask: "unknown",
      dappName: "newTaskName",
      allTasks: "show",
      byIndex: "2",
      taskStatus: "made",
      tasksLength: "15",
      toggleStatus: true,
    };
  },
  methods: {
    async contractDappName() {
      this.dappName = await this.contract.dappName();
    },
    async contractTasksLength() {
      this.tasksLength = await this.contract.getTasksLength();
    },
    async contractAllTasks() {
      this.allTasks = await this.contract.getAllTasks();
    },
    async contractTaskByIndex() {
      this.byIndex = await this.contract.getTaskByIndex();
    },
    async contractTaskStatus() {
      this.taskStatus = await this.contract.getTaskStatus();
    },
    //write
    async contractToggleTaskStatus() {
      this.toggleStatus = await this.contract.toggleTaskStatus();
    },
    async contractAddNewTask() {
      this.addTask = await this.contract.addNewTask();
    },
  },

  setup() {
    const { address, balance, chainId, isActivated, signer } = useEthers();
    // create contract helper object
    const contractInterface = new ethers.utils.Interface(TodoListAbi);
    const contractAddress = "0x5269d63d6d2c25Ba95AE2CB9fd5b46f1e48635a8";
    const contract = new ethers.Contract(
      contractAddress,
      contractInterface,
      signer.value
    );
    return {
      address,
      balance,
      chainId,
      isActivated,
      displayEther,
      contract,
    };
  },
};
</script>
<style scoped>
* {
  box-sizing: border-box;
}
body {
  margin: 0;
  min-width: 250px;
  background-image: linear-gradient(to right bottom, #0093e9, #80d0c7);
  height: 100%;
}
.wrapper {
  background: #fff;
  max-width: 400px;
  width: 100%;
  margin: 45px auto;
  padding: 25px;
  border-radius: 15px;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}
ul {
  margin: 0;
  padding: 0;
}
ul li {
  cursor: pointer;
  position: relative;
  padding: 12px 8px 12px 40px;
  list-style-type: none;
  background: #eee;
  font-size: 18px;
  transition: 0.2s;
  border-radius: 10px;
}
li {
  margin-top: 10px;
}
ul li:nth-child(odd) {
  background: #f9f9f9;
}
ul li:hover {
  background: #ddd;
}
ul li.checked {
  background: #888;
  color: #fff;
  text-decoration: line-through;
}
ul li.checked::before {
  content: "";
  position: absolute;
  border-color: #fff;
  border-style: solid;
  border-width: 0 2px 2px 0;
  top: 10px;
  left: 16px;
  transform: rotate(45deg);
  height: 15px;
  width: 7px;
}
.close {
  position: absolute;
  right: 0;
  top: 0;
  padding: 12px 16px 12px 16px;
}
.close:hover {
  background-color: #f44336;
  color: white;
  border-top-right-radius: 10px;
  border-bottom-right-radius: 10px;
}
.header {
  background: linear-gradient(to bottom right, #21d4fd, #b721ff);
  padding: 30px 40px;
  color: white;
  text-align: center;
  border-radius: 15px;
}
.header:after {
  content: "";
  display: table;
  clear: both;
}
input {
  margin: 0;
  border: none;
  border-radius: 0;
  width: 75%;
  padding: 10px;
  float: left;
  font-size: 16px;
}
.addBtn {
  padding: 10px;
  width: 25%;
  background: #d9d9d9;
  color: #555;
  float: left;
  text-align: center;
  font-size: 16px;
  cursor: pointer;
  transition: 0.3s;
  border-radius: 0;
}
.addBtn:hover {
  background-color: #bbb;
}
.credit {
  text-align: center;
  color: #000;
  font-family: "Trebuchet MS", "Lucida Sans Unicode", "Lucida Grande",
    "Lucida Sans", Arial, sans-serif;
  width: 100%;
}
.credit a {
  text-decoration: none;
  color: #072f5f;
  font-weight: bold;
}
</style>
