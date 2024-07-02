<script>
  import data from "$data/data.js";
  console.log(data);

  import { scaleLinear } from "d3-scale";
  // import { extent } from "d3-array";

  const margin = {
    top: 20,
    right: 20,
    bottom: 20,
    left: 15,
  };

  let width = 400;
  $: innerWidth = width - (margin.left + margin.right);
  $: xScale = scaleLinear().domain([0, 100]).range([0, innerWidth]);

  // let yDomain = extent({data.hours})

  let height = 400;
  let innerHeight = width - (margin.top + margin.bottom);
  let yScale = scaleLinear().domain([0, 60]).range([innerHeight, 0]);

  import AxisX from "$components/AxisX.svelte";
  import AxisY from "$components/AxisY.svelte";
  import Tooltip from "$components/Tooltip.svelte";
  import {fly} from "svelte/transition"

  let hoveredData;
  $: console.log(hoveredData); // To track what we're hovering
</script>

<h1>Students who studied longer scored higher on their final exam</h1>
<!-- Svelte each bloc -->
<div class="chart-container" bind:clientWidth={width}>
  <svg {width} {height}>
    <g transform="translate({margin.left} {margin.top})">
      <AxisX {xScale} {innerHeight} {innerWidth} />
      <AxisY {yScale} {innerWidth} />
      {#each data.sort((a,b) => a.grade - b.grade ) as d}
        <!-- <p> <strong>{d.name}</strong> studied for {d.hours} hours</p> -->
        <!-- svelte-ignore a11y-mouse-events-have-key-events -->
        <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
        <circle 
          in:fly={{ x: -10, opacity: 0, duration:500}}
          cx={xScale(d.grade)}
          cy={yScale(d.hours)}
          r={ hoveredData===d ? 20: 10}
          opacity={ hoveredData ?
          (hoveredData === d ? 1 : 0.45) : 1 }
          fill="purple"
          stroke="black"
          stroke-width="1"
          on:mouseover={() => (hoveredData = d)}
          on:mouseleave= {() => (hoveredData = null)}
          on:focus={() => (hoveredData = d)}
          tabindex="0"
        />
      {/each}
    </g>
  </svg>
  {#if hoveredData}
    <Tooltip data={hoveredData} {xScale} {yScale} {width}/>
  {/if}
</div>

<style>
  :global(.tick text, .axis-title) {
    font-weight: 400;
    font-size: 12px;
    fill: #8f8f8f;
  }

  .chart-container {
    position: relative;
  }

  circle{
    transition:  r 300ms ease, opacity  500ms ease;
    cursor: pointer;
  }

  h1{
    font-size: 1.45rem;
    font-weight: 600;
    margin: 0.75rem;

  }
</style>
