<template>
  <h2>{{ allTasksNames }}</h2>
  <p>TASK STATUS: {{ this.taskStatus }},</p>
  <span
    class="btn btn-outline-success"
    v-if="!toggleStatus"
    @click="changeStatus"
    >Done</span
  >
</template>

<script lang="ts">
import { useBoard, useEthers, displayEther, shortenAddress } from "vue-dapp";
import TodoListAbi from "../abi/TodoListAbi.json";

import { ethers } from "ethers";

export default {
  name: "Navbar",
  props: ["allTasksNames"],
  created() {
    this.contractTaskStatus();
    this.contractToggleTaskStatus();
    this.contractAllTasks();
  },
  data() {
    return {
      taskName: "",
      taskStatus: false,
      newTaskStatus: true,
      toggleStatus: false,
      allTasks: "",
    };
  },

  methods: {
    async contractTaskStatus() {
      this.taskName = await this.contract.getTaskStatus(this.allTasksNames);
    },
    async contractToggleTaskStatus() {
      this.toggleStatus = await this.contract.toggleTaskStatus();
    },
    async contractAllTasks() {
      this.allTasks = await this.contract.getAllTasks();
    },

    // WRITE
    async changeStatus() {
      const tx = await this.contract.toggleTaskStatus(this.newTaskStatus);

      const receipt = await tx.wait();
      if (receipt.status === 1) {
        console.log("Transaction succeeded");
        console.log(receipt);
        this.taskStatus = true;

        this.contractToggleTaskStatus();
        this.contractAllTasks();
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
