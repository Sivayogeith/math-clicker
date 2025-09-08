<script lang="ts">
  import lock from "$lib/assets/lock.svg";
  import { onMount } from "svelte";
  import Katex from "./Katex.svelte";
  import { Buffer } from "buffer";
  import { copyText } from 'svelte-copy';

  const UPGRADES = [
    {
      name: "2x",
      description: "doubles click",
      price: 100,
      priceMultiplier: 3,
      math: (e: string) => `${e} * 2`,
    },
    {
      name: "x^2",
      description: "squares click",
      price: 1000,
      priceMultiplier: 5,
      math: (e: string) => `(${e})^2`,
    },
  ];
  let save: {
    m: number;
    equation: string;
    upgrades: { [name: string]: { own: number; price: number } };
  } = $state({
    m: 0,
    equation: "1",
    upgrades: {
      "2x": {
        price: UPGRADES[0].price,
        own: 0,
      },
      "x^2": {
        price: UPGRADES[1].price,
        own: 0,
      },
    },
  });
  let copySaveText = $state("copy save")

  const onClick = () => {
    save.m += eval(save.equation.replace("^", "**"));
    saveToLocalStorage();
  };

  const buyUpgrade = (index: number) => {
    const upgrade = UPGRADES[index];
    if (save.upgrades[upgrade.name].price <= save.m) {
      save.m -= save.upgrades[upgrade.name].price;
      save.upgrades[upgrade.name] = {
        price: save.upgrades[upgrade.name].price * upgrade.priceMultiplier,
        own: save.upgrades[upgrade.name].own + 1,
      };
      save.equation = upgrade.math(save.equation);
      saveToLocalStorage();
    }
  };

  const saveToLocalStorage = () => {
    localStorage.setItem("save", encodeSave(save));
  };

  const loadFromLocalStorage = () => {
    return decodeSave(localStorage.getItem("save") ?? "");
  };

  const decodeSave = (save: string) => {
    return JSON.parse(Buffer.from(save, "base64").toString());
  };

  const encodeSave = (save: object) => {
    return Buffer.from(JSON.stringify(save)).toString("base64");
  };

  const onClickCopySave = () => {
    saveToLocalStorage();
    copyText(encodeSave(save))
    copySaveText = "copied!"
    setInterval(() => copySaveText = "copy save", 2000)
  }

  const onClickLoadSave = () => {
    const pastedSave = prompt("Paste your save here:", encodeSave(save)) as string;
    save = decodeSave(pastedSave)
  }

  onMount(() => {
    save = loadFromLocalStorage();
  });
</script>

<div class="text-center">
  <h1 class="text-3xl">
    math clicker<span class="text-lg">
      - clicker game built with no engine</span
    >
  </h1>
</div>
<div class="flex">
  <div class="flex items-center md:w-[70%] w-[60%] flex-col">
    <p class="flex justify-center items-center h-[10dvh] text-sky-700 text-lg">
      equation:&nbsp;<Katex math={save.equation} />
    </p>
    <div class="flex justify-center items-center h-[60dvh] flex-col">
      <button
        id="math"
        class="bg-sky-200 hover:shadow-lg py-20 px-15 rounded-full"
        onclick={onClick}
      >
        <p class="text-sky-700 text-lg">balance: {save.m} m</p>
        <p class="text-5xl">math</p>
      </button>
    </div>
    <p
      class="flex justify-start items-end h-[25dvh] w-[100%] text-lg ml-10 gap-5"
    >
      <button onclick={onClickCopySave}>{copySaveText}</button>
      <button onclick={onClickLoadSave}>load</button>
    </p>
  </div>
  <div
    class="flex h-[95dvh] md:w-[30%] w-[40%] rounded-l-2xl border p-5 flex-col"
  >
    <p class="text-3xl text-center mb-5">
      shop <span class="text-sm">(click to buy)</span>
    </p>
    <ul class="lg:text-xl">
      {#each UPGRADES as upgrade, i}
        <div class="my-3">
          <li class="flex justify-between">
            <button onclick={() => buyUpgrade(i)} class="flex gap-2">
              {upgrade.name} - {upgrade.description}
              {#if save.m < save.upgrades[upgrade.name].price}
                <img src={lock} class="size-6" alt="locked" />
              {/if}
            </button>
            <p
              class="font-bold {save.m < save.upgrades[upgrade.name].price
                ? 'text-red-800'
                : 'text-green-800'}"
            >
              {save.upgrades[upgrade.name].price} m
            </p>
          </li>
          <p class="text-sm text-sky-700">
            you own {save.upgrades[upgrade.name].own}
          </p>
        </div>
      {/each}
    </ul>
  </div>
</div>
