<script lang="ts">
  import lock from "$lib/assets/lock.svg";
  import Katex from "./Katex.svelte";
  const upgrades = $state([
    {
      name: "2x",
      description: "doubles click",
      price: 100,
      priceMultiplier: 3,
      math: (e: string) => `${e} * 2`,
      own: 0,
    },
    {
      name: "x^2",
      description: "squares click",
      price: 1000,
      priceMultiplier: 5,
      math: (e: string) => `(${e})^2`,
      own: 0,
    },
  ]);
  let count = $state(0);
  let equation = $state("1");

  const onClick = () => {
    count += eval(equation.replace("^", "**"));
  };

  const buyUpgrade = (index: number) => {
    const upgrade = upgrades[index];
    if (upgrade.price <= count) {
      count -= upgrade.price;
      equation = upgrade.math(equation);
      upgrade.price *= upgrade.priceMultiplier;
      upgrade.own++;
    }
  };
</script>

<div class="text-center">
  <h1 class="text-3xl">
    math clicker<span class="text-lg">
      - clicker game build with no engine</span
    >
  </h1>
</div>
<div class="flex">
  <div class="flex items-center md:w-[70%] w-[60%] flex-col">
    <p class="flex justify-center items-center h-[10dvh] text-sky-700 text-lg">
      equation:&nbsp;<Katex math={equation}/>
    </p>
    <div class="flex justify-center items-center h-[60dvh] flex-col">
      <button
        id="math"
        class="bg-sky-200 hover:bg-[#7bd0fdab] py-20 px-15 rounded-full"
        onclick={onClick}
      >
        <p class="text-sky-700 text-lg">balance: {count} m</p>
        <p class="text-5xl">math</p>
      </button>
    </div>
  </div>
  <div
    class="flex h-[95dvh] md:w-[30%] w-[40%] rounded-l-2xl border p-5 flex-col"
  >
    <p class="text-3xl text-center mb-5">
      shop <span class="text-sm">(click to buy)</span>
    </p>
    <ul class="lg:text-xl">
      {#each upgrades as upgrade, i}
        <div class="my-3">
          <li class="flex justify-between">
            <button onclick={() => buyUpgrade(i)} class="flex gap-2">
              {upgrade.name} - {upgrade.description}
              {#if count < upgrade.price}
                <img src={lock} class="size-6" alt="locked" />
              {/if}
            </button>
            <p
              class="font-bold {count < upgrade.price
                ? 'text-red-800'
                : 'text-green-800'}"
            >
              {upgrade.price} m
            </p>
          </li>
          <p class="text-sm text-sky-700">you own {upgrade.own}</p>
        </div>
      {/each}
    </ul>
  </div>
</div>
