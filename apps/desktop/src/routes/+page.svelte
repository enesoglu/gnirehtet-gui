<script lang="ts">
  import { onMount } from "svelte";
  import HeaderBar, { type Panel } from "$lib/components/HeaderBar.svelte";
  import Banner from "$lib/components/Banner.svelte";
  import ConnectionView from "$lib/components/ConnectionView.svelte";
  import LogsView from "$lib/components/LogsView.svelte";
  import PreferencesView from "$lib/components/PreferencesView.svelte";
  import Sheet from "$lib/components/ui/sheet.svelte";
  import { app } from "$lib/app.svelte";

  let panel = $state<Panel | null>(null);

  onMount(() => {
    void app.start();
    return () => {
      app.stop();
    };
  });
</script>

<div class="window">
  <HeaderBar bind:panel />
  <main class:covered={panel !== null}>
    <ConnectionView />
  </main>
  <Banner onShowLog={() => (panel = "log")} />
</div>

<Sheet bind:open={() => panel === "log", (v) => (panel = v ? "log" : null)} title="Log" fill>
  <LogsView />
</Sheet>

<Sheet
  bind:open={() => panel === "preferences", (v) => (panel = v ? "preferences" : null)}
  title="Preferences"
>
  <PreferencesView />
</Sheet>

<style>
  .window {
    position: relative;
    display: flex;
    flex-direction: column;
    height: 100vh;
    overflow: hidden;
  }

  main {
    flex: 1;
    min-height: 0;
    overflow-y: auto;
    /* The phone illustration scales with the height available here. */
    container-type: size;
  }

  /* Hold the phone's animations while an overlay covers them. In WebKitGTK,
     running layers underneath make the overlay drop out for a frame when its
     slide-in ends. (!important: each `animation` shorthand resets it.) */
  main.covered :global(*) {
    animation-play-state: paused !important;
  }
</style>
