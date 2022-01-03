<template>
  <div class="text-center"></div>
</template>

<script lang="ts">
import { ethers } from "ethers";
import { useEthers, displayEther } from "vue-dapp";
import TodoListAbi from "../abi/TodoListAbi.json";
export default {
  name: "Home",

  setup() {
    const { address, balance, chainId, isActivated, signer } = useEthers();
    // create contract helper object
    const contractInterface = new ethers.utils.Interface(TodoListAbi);
    const contractAddress = "0x8Dec08063931947e0be134A294a0047bD9e39442";
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
