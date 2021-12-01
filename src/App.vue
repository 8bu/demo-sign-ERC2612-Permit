<script setup lang="ts">
import { ref, unref, computed } from "vue";
import { signERC2612Permit } from "eth-permit";

const value = ref("10000000000000");
const tokenAddress = ref("0x0Ed8E0A2D99643e1e65CCA22Ed4424090B8B7458");
const spender = ref("0x05fF2B0DB69458A0750badebc4f9e13aDd608C7F");
const account = ref("");
const result = ref({});
const generatedJSON = computed(() =>
  JSON.stringify(unref(result), undefined, 4)
);
const connect = async () => {
  account.value = (
    await window.ethereum.request({
      method: "eth_requestAccounts",
    })
  )[0];
  console.debug(account.value);
};
// You can replace window.ethereum with any other web3 provider.
const getPermitResult = async () => {
  result.value = await signERC2612Permit(
    window.ethereum,
    unref(tokenAddress),
    unref(account),
    unref(spender),
    unref(value)
  );
};
</script>

<template>
  <div class="grid grid-cols-1 gap-4 mx-auto max-w-144">
    <div class="text-center">
      <button @click="connect">Connect</button>
    </div>
    <div class="text-center">
      {{ account }}
    </div>
    <label class="grid grid-cols-1">
      <span class="text-left"> Token address </span>
      <input placeholder="Token Address" type="text" v-model="tokenAddress" />
    </label>
    <label class="grid grid-cols-1">
      <span class="text-left"> Spender </span>
      <input placeholder="Spender" type="text" v-model="spender" />
    </label>

    <label class="grid grid-cols-1">
      <span class="text-left"> Value (wei) </span>
      <input placeholder="Value in Wei" type="text" v-model="value" />
    </label>

    <div class="text-center">
      <button @click="getPermitResult">Sign</button>
    </div>

    <pre class="p-2 text-left bg-gray-50 text-xs">{{ generatedJSON }}</pre>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
