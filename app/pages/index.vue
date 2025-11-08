<template>
  <div
    class="flex flex-col justify-start w-[80wh] h-screen items-center bg-slate-300 gap-5"
  >
    <div class="flex">
      <button
        @click="createWallet"
        class="center text-white bg-gray-800 hover:bg-gray-900 focus:outline-none focus:ring-4 focus:ring-gray-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 dark:bg-gray-800 dark:hover:bg-gray-700 dark:focus:ring-gray-700 dark:border-gray-700"
      >
        Create wallet
      </button>
    </div>
    <div v-for="account in accounts" class="flex justify-center">
      <Card
        :title="account.secretKey"
        :subtitle="account.publicKey"
        :badge="account.balances"
        @action="fund_account(account.publicKey)"
        action-text="Fondear cuenta"
      />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import { StellarConfiguration, Wallet } from "@stellar/typescript-wallet-sdk";
import * as Client from "../../packages/random_exchange";

interface WalletAccount {
  publicKey: string;
  secretKey: string;
  balances: string;
}

const accounts = ref<WalletAccount[]>([]);

let wallet = new Wallet({
  stellarConfiguration: StellarConfiguration.TestNet(),
});

const stellar = wallet.stellar();

async function fund_account(publicKey: string) {
  await stellar.fundTestnetAccount(publicKey);
  await fetch_funds(publicKey);
}

async function fetch_funds(publicKey: string) {
  const funds = await stellar.account().getInfo({ accountAddress: publicKey });
  console.log(funds.balances);
  // Busca la wallet y actualiza sus balances
  const idx = accounts.value.findIndex((a) => a.publicKey === publicKey);
  if (idx !== -1) {
    if (!accounts.value[idx]) return;
    accounts.value[idx].balances = funds.balances[0]?.balance ?? "0";
  }
}

async function createWallet() {
  const account = stellar.account();
  const keypair = account.createKeypair();
  accounts.value.push({
    publicKey: keypair.publicKey,
    secretKey: keypair.secretKey,
    balances: "0",
  });
}
</script>

/
<!-- <button @click="fetch_funds(account.publicKey)">Fetch funds</button>
    <button @click="fund_account(account.publicKey)">Fund account</button> -->
