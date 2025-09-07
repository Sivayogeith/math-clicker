<script lang="ts">
  const upgrades = $state([
    {
      name: "2x",
      description: "doubles click",
      price: 100,
      priceMultiplier: 3,
      math: " * 2",
      own: 0,
    },
    {
      name: "x^2",
      description: "squares click",
      price: 1000,
      priceMultiplier: 5,
      math: " ^2",
      own: 0,
    },
  ]);
  let count = $state(0);
  let equation = $state("1");

  const onClick = () => {
    count += eval(equation);
  };

  const buyUpgrade = (index: number) => {
    const upgrade = upgrades[index];
    if (upgrade.price <= count) {
      count -= upgrade.price;
      equation += upgrade.math;
      upgrade.price *= upgrade.priceMultiplier;
      upgrade.own++;
    }
  };
</script>

<div class="text-center">
  <h1 class="text-3xl">math clicker</h1>
</div>
<div class="flex">
  <div class="flex items-center md:w-[70%] w-[60%] flex-col">
    <p class="flex justify-center items-center h-[10dvh] text-sky-700 text-lg">
      equation: {equation}
    </p>
    <div class="flex justify-center items-center h-[60dvh] flex-col">
      <p class="text-sky-700">balance: {count} m</p>
      <button class="text-5xl" onclick={onClick}>meth</button>
    </div>
  </div>
  <div class="flex h-[95dvh] md:w-[30%] w-[40%] rounded-l-2xl border p-5 flex-col">
    <p class="text-3xl text-center mb-5">
      shop <span class="text-sm">(click to buy)</span>
    </p>
    <ul class="lg:text-xl">
      {#each upgrades as upgrade, i}
        <div class="my-3">
          <li class="flex justify-between">
            <button onclick={() => buyUpgrade(i)}>
              {upgrade.name} - {upgrade.description}
            </button>
            <p class="font-bold">{upgrade.price} m</p>
          </li>
          <p class="text-sm text-sky-700">you own {upgrade.own}</p>
        </div>
      {/each}
    </ul>
  </div>
</div>
