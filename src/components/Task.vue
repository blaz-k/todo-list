<template>
  <h2>{{ task }}</h2>
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
