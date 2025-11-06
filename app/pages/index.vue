<template>
  <h1>hello world</h1>
  <button @click="createWallet">Create wallet</button>
  <br />
  <div>
    <br />

    <label for="">This is my public key bro</label>
    <br />
    <div v-for="account in accounts">
      {{ account.publicKey }}
      <br />
      {{ account.secretKey }}
      <br />
      $ {{ account.balances ?? 0 }}
      <br />
      <button @click="fetch_funds(account.publicKey)">Fetch funds</button>
      <button @click="fund_account(account.publicKey)">Fund account</button>
    </div>
  </div>
  <br />
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
