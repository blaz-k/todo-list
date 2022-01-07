<template>
  <div class="text-center">
    <button class="fas fa-sync-alt" @click="contractAllTasks"></button>
    <i class="fas fa-sync-alt" @click="refreshTasks"></i>
    <p>REFRESH TASKS: {{ newTask }}</p>
    <p>{{ isActivated }}</p>
  </div>
  <hr />
  <div class="wrapper" v-if="isActivated">
    <div id="myDIV" class="header">
      <h2 style="margin: 5px" class="mb-4">My Todo List</h2>
      <input
        class="obroba"
        type="text"
        id="myInput"
        placeholder="To do..."
        v-model="newTask"
      />

      <span class="addBtn" @click="addNewTask" :disabled="spinner">Add</span>

      <button class="btn btn-primary" type="button" disabled v-if="spinner">
        <span
          class="spinner-border spinner-border-sm"
          role="status"
          aria-hidden="true"
        ></span>
        <span class="">Adding new task please wait...</span>
      </button>
    </div>
    <ul id="myUL">
      <div v-for="task in allTasks" :key="task">
        <Tasks :allTasksNames="task" />

        <li v-if="newStatus">
          <!-- <span
            class="btn btn-outline-success"
            v-if="!toggleStatus"
            @click="changeStatus"
            >Mark as done</span
          > -->
        </li>

        <li v-if="!newStatus" class="">
          {{ task }} toggle: {{ toggleStatus }} TASK DONE
        </li>
      </div>
    </ul>
  </div>
</template>

<script lang="ts">
import { ethers } from "ethers";
import { useEthers, displayEther } from "vue-dapp";
import TodoListAbi from "../abi/TodoListAbi.json";
import Tasks from "../components/Task.vue";
export default {
  name: "Home",
  components: { Tasks },

  created() {
    if (this.signer) {
      this.createContract();
      this.contractAllTasks();
    }
    // this.contractAddNewTask();

    // this.contractTaskByIndex();
    // this.contractTaskStatus();
    // this.contractToggleTaskStatus();
  },

  data() {
    return {
      addTask: "",
      dappName: "",
      allTasks: "",
      byIndex: null,
      taskStatus: false,
      toggleStatus: false,
      newTask: null,
      spinner: false,
      newStatus: true,
      contract: null,
    };
  },

  methods: {
    //SHOW

    async contractAllTasks() {
      this.allTasks = await this.contract.getAllTasks();
    },

    async contractTaskByIndex() {
      this.byIndex = await this.contract.getTaskByIndex();
    },

    // async contractTaskStatus() {
    //   this.taskStatus = await this.contract.getTaskStatus(this.allTasks);
    //   console.log("contract status= " + this.taskStatus);
    // },

    // async contractToggleTaskStatus() {
    //   this.toggleStatus = await this.contract.toggleTaskStatus();
    // },

    // async contractTasksLength() {
    //   this.tasksLength = await this.contract.getTasksLength();
    // },

    // async contractAddNewTask() {
    //   this.addTask = await this.contract.addNewTask();
    // },

    // WRITE

    //  ADD NEW TASK IN TODO LIST
    async addNewTask() {
      this.spinner = true;
      const tx = await this.contract.addNewTask(this.newTask);
      const receipt = await tx.wait();
      if (receipt.status === 1) {
        console.log("Transaction succeeded");
        console.log(receipt);
        this.contractAddNewTask();
        this.contractAllTasks();
      } else {
        console.log("Transaction failed...");
      }
      this.spinner = false;
    },

    //CHANGE STATUS OF TASK (FALSE --> TRUE)

    refreshTasks() {
      this.contractAllTasks();
      console.log("Contract all tasks");
    },

    createContract() {
      const contractInterface = new ethers.utils.Interface(TodoListAbi);
      const contractAddress = "0x5269d63d6d2c25Ba95AE2CB9fd5b46f1e48635a8";
      this.contract = new ethers.Contract(
        contractAddress,
        contractInterface,
        this.signer
      );
    },
  },
  watch: {
    //newValue is new value already defined, and oldValue is an old one
    signer(newValue, oldValue) {
      this.createContract();
      this.contractAllTasks();
    },
  },
  setup() {
    const { address, balance, chainId, isActivated, signer } = useEthers();
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
      signer,
      contract,
    };
  },
};

// watcher

// watcher contract izven setup-a v methods v eni funkciji
</script>
<style scoped>
.obroba {
  border-radius: 12px 86px;
}
.background {
  color: chartreuse;
}
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
  max-width: 600px;
  min-width: 350px;
  width: 100%;
  margin: 45px auto;
  padding: 25px;
  border-radius: 25px;
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
  border-radius: 12px 86px;
}
.addBtn:hover {
  background-color: rgb(187, 187, 187);
}
</style>
