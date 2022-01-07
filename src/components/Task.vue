<template>
  <div id="task">
    <div v-if="taskStatus">
      <ul>
        <li>
          <h2 class="done">{{ task }}</h2>

          <span class="btn btn-outline-danger" @click="changeStatus"
            >Mark Undone</span
          >
        </li>
      </ul>
    </div>

    <div v-if="!taskStatus" class="undone">
      <ul>
        <li>
          <h2>{{ task }}</h2>
          <div class="gumb">
            <span class="btn btn-outline-success" @click="changeStatus"
              >Mark Done</span
            >
          </div>
        </li>
      </ul>
    </div>
  </div>

  <!-- <li v-if="newStatus"></li>

  <li v-if="!newStatus" class="">
    {{ task }} toggle: {{ toggleStatus }} TASK DONE
  </li> -->
</template>

<script lang="ts">
import { useBoard, useEthers, displayEther, shortenAddress } from "vue-dapp";
import TodoListAbi from "../abi/TodoListAbi.json";

import { ethers } from "ethers";

export default {
  name: "Task",
  props: ["task"],
  created() {
    this.contractTaskStatus();
    // this.contractToggleTaskStatus();
    // this.contractAllTasks();
  },
  data() {
    return {
      taskStatus: false,
    };
  },

  methods: {
    async contractTaskStatus() {
      this.taskStatus = await this.contract.getTaskStatus(this.task);
    },

    // WRITE
    async changeStatus() {
      const tx = await this.contract.toggleTaskStatus(String(this.task));

      const receipt = await tx.wait();
      if (receipt.status === 1) {
        console.log("Transaction succeeded");
        console.log(receipt);
        this.contractTaskStatus();
      } else {
        console.log("Transaction failed...");
      }
    },
  },

  setup() {
    const { open } = useBoard();
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
      open,
      displayEther,
      shortenAddress,
      contract,
    };
  },
};
</script>

<style>
#task ul {
  margin: 0;
  padding: 0;
}
#task ul li {
  display: flex;
  justify-content: space-between;
  cursor: pointer;
  position: relative;
  padding: 12px 8px 12px 40px;
  list-style-type: none;
  background: #eee;
  font-size: 18px;
  transition: 0.2s;
  border-radius: 10px;
}
#task li {
  margin-top: 10px;
}
#task ul li:nth-child(odd) {
  background: #f9f9f9;
}
#task ul li:hover {
  background: #ddd;
}
/*  */
#task h2 {
  font-size: 1.5rem;
  text-transform: capitalize;
}
/* .undone {
} */

#task .done {
  text-decoration: line-through;
}

#task span {
  font-size: 24px;
}
</style>
