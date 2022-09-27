<script>
  import { onMount } from 'svelte';
  import { writable } from "svelte/store";
  import Gearset from "./lib/Gearset.svelte";

  let cranksets;
  let cogsets;
  let circumference;
  let rpm;

  const cmPerMinute2kmPerHour = 100000/60;

  onMount(() => {
          circumference = writable(
            JSON.parse(
              localStorage.getItem("driveTrain_circumference")
            ) || []
          );
          circumference.subscribe(
            val => localStorage.setItem("driveTrain_circumference", JSON.stringify(val))
          );

          rpm = writable(
            JSON.parse(
              localStorage.getItem("driveTrain_rpm")
            ) || {min:30,max:90}
          );
          rpm.subscribe(
            val => localStorage.setItem("driveTrain_rpm", JSON.stringify(val))
          );
  })
</script>

<main>
  <h1>Calcul de braquet</h1>

  <h2>Mon vélo</h2>
    <p>plateaux (nb dents):
      <Gearset bind:gearsets={cranksets} name="cranksets" />
    </p>
    <p>pignons (nb dents):
      <Gearset bind:gearsets={cogsets}  name="cogsets" />
    </p>
    <p>circonférence :
      <input type="number" bind:value={$circumference} min=50 max=1000 />
      cm
    </p>
    {#if rpm && circumference}
      <p>fréquence de pédalage :
        <input type="number" bind:value={($rpm).min} min=0 max=180 />
        <input type="number" bind:value={($rpm).max} min=0 max=180 />
      </p>
    {/if}

  {#if cranksets && cogsets}
    <h2>Dérailleurs</h2>
      <p><em>Denture maximal plateau :</em> {Math.max(...$cranksets)} dents</p>
      <p><em>Limite arrière :</em> {Math.min(...$cogsets)}-{Math.max(...$cogsets)}</p>
      <p><em>Capacité minimal :</em> {Math.max(...$cranksets) - Math.min(...$cranksets) + Math.max(...$cogsets) - Math.min(...$cogsets)} dents</p>
  <h2>Braquets</h2>
    <table>
      <tr>
        <td colspan="2" class="noborder"></td>
        <th colspan={$cogsets.length} class="noborder">pignons</th>
      </tr>
      <tr>
        <td colspan="2" class="noborder"></td>
        {#each $cogsets as cogset}
          <th>{cogset}</th>
        {/each}
      </tr>
      {#each $cranksets as crankset}
      <tr>
        {#if $cranksets[0] == crankset}
          <th rowspan={$cranksets.length} class="vertical noborder">plateaux</th>
        {/if}
        <th>{crankset}</th>
        {#each $cogsets as cogset}
          <td>{(crankset/cogset).toFixed(2)}</td>
        {/each}
      </tr>
      {/each}
    </table>
  {/if}

  {#if cranksets && cogsets && circumference}
  <h2>Développement</h2>
    <table>
      <tr>
        <td colspan="2" class="noborder"></td>
        <th colspan={$cogsets.length} class="noborder">pignons</th>
      </tr>
      <tr>
        <td colspan="2" class="noborder"></td>
        {#each $cogsets as cogset}
          <th>{cogset}</th>
        {/each}
      </tr>
      {#each $cranksets as crankset}
      <tr>
        {#if $cranksets[0] == crankset}
          <th rowspan={$cranksets.length} class="vertical noborder">plateaux</th>
        {/if}
        <th>{crankset}</th>
        {#each $cogsets as cogset}
          <td>{(crankset/cogset*$circumference/100).toFixed(2)} m/tour</td>
        {/each}
      </tr>
      {/each}
    </table>
  {/if}

  {#if cranksets && cogsets && circumference && rpm}
  <h2>Vitesse</h2>
    <p>pour une fréquence de pédallage entre {$rpm.min} ~ {$rpm.max} tours/min</p>
    <table>
      <tr>
        <td colspan="2" class="noborder"></td>
        <th colspan={$cogsets.length} class="noborder">pignons</th>
      </tr>
      <tr>
        <td colspan="2" class="noborder"></td>
        {#each $cogsets as cogset}
          <th>{cogset}</th>
        {/each}
      </tr>
      {#each $cranksets as crankset}
      <tr>
        {#if $cranksets[0] == crankset}
          <th rowspan={$cranksets.length} class="vertical noborder">plateaux</th>
        {/if}
        <th>{crankset}</th>
        {#each $cogsets as cogset}
          <td>
            {(crankset/cogset*$circumference*$rpm.min/cmPerMinute2kmPerHour).toFixed(0)}
            ~
            {(crankset/cogset*$circumference*$rpm.max/cmPerMinute2kmPerHour).toFixed(0)}
            km/h
          </td>
        {/each}
      </tr>
      {/each}
    </table>
  {/if}

</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4rem;
    font-weight: 100;
    line-height: 1.1;
    margin: 2rem auto;
    max-width: 14rem;
  }

  p {
    margin: 1rem auto;
    line-height: 1.35;
  }

  table {
    margin: 1rem auto;
    border-collapse: collapse;
  }
  th, td {
    border: solid 1px #000;
    padding: 0.5em;
  }

  .noborder {
    border: none;
  }
  th.vertical {
    transform: rotate(-90deg);
  }

  input {
    width: 3em;
    margin: 0.2em;
    padding: 0.1em 0.4em;
    background: #f0f0f0;
  }

</style>
