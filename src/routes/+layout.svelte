<script lang="ts">
  import "../app.css";
  import favicon from "$lib/assets/favicon.svg";

  let { children } = $props();
</script>

<svelte:head>
  <link rel="icon" href={favicon} />
  <style>
    @import url("https://fonts.googleapis.com/css2?family=Comic+Relief:wght@400;700&display=swap");

    body {
      font-family: "Comic Relief", system-ui;
      font-weight: 400;
      font-style: normal;
    }
  </style>
</svelte:head>

{@render children?.()}
