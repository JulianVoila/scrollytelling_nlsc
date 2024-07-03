<script>
  import data from "$data/data.js";
  console.log(data);

  import { scaleLinear } from "d3-scale";
  import AxisX from "$components/AxisX.svelte";
  import AxisY from "$components/AxisY.svelte";
  import Tooltip from "$components/Tooltip.svelte";
  import { fly } from "svelte/transition";
  

  import Scrolly from "./helpers/Scrolly.svelte";
  let currentStep;
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

  let hoveredData;
  $: console.log(hoveredData); // To track what we're hovering

  let initialData = data.sort((a, b) => a.grade - b.grade);
  let renderedData = initialData;

  $: X_MIDPOINT = (xScale.domain()[0] + xScale.domain()[1]) / 2;
  $: Y_MIDPOINT = (yScale.domain()[0] + yScale.domain()[1]) / 2;

  $: {
    if (currentStep === 0) {
      renderedData = initialData.map((d) => ({
        ...d,
        grade: X_MIDPOINT,
        hours: Y_MIDPOINT,
      }));
      hoveredData = null;
    } else if (currentStep === 1) {
      renderedData = initialData.map((d) => ({
        ...d,
        hours: Y_MIDPOINT,
      }));
      hoveredData = null;
    } else if (currentStep === 2) {
      renderedData = initialData;
      hoveredData = renderedData[13];
    }
  }
</script>

<section>
  <div class="sticky">
    <h1>Students who studied longer scored higher on their final exam</h1>
    <!-- Svelte each bloc -->
    <div class="chart-container" bind:clientWidth={width}>
      <svg {width} {height}>
        <g transform="translate({margin.left} {margin.top})">
          {#if currentStep>0}
          <AxisX {xScale} {innerHeight} {innerWidth} />
          {/if}
          {#if currentStep > 1}
          <AxisY {yScale} {innerWidth} />
          {/if}
          {#each renderedData as d}
            <!-- <p> <strong>{d.name}</strong> studied for {d.hours} hours</p> -->
            <!-- svelte-ignore a11y-mouse-events-have-key-events -->
            <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
            <circle
              in:fly={{ x: -10, opacity: 0, duration: 500 }}
              cx={xScale(d.grade)}
              cy={yScale(d.hours)}
              r={hoveredData === d ? 20 : 10}
              opacity={hoveredData ? (hoveredData === d ? 1 : 0.45) : 1}
              fill="purple"
              stroke="black"
              stroke-width="1"
              on:mouseover={() => (currentStep >= 2 ? hoveredData = d : null)}
              on:mouseleave={() => (currentStep >= 2 ? hoveredData = d : null)}
              on:focus={() => (hoveredData = d)}
              tabindex="0"
            />
          {/each}
        </g>
      </svg>
      {#if hoveredData}
        <Tooltip data={hoveredData} {xScale} {yScale} {width} />
      {/if}
    </div>
  </div>
  <div class="steps">
    <Scrolly bind:value={currentStep}>
      {#each ["Hello", "SC", "World"] as text, i}
        <div class="step" class:active={currentStep === i}>
          <div class="step-content">
            <p>{text}</p>
          </div>
        </div>
      {/each}
    </Scrolly>
  </div>
</section>

<style>
  :global(.tick text, .axis-title) {
    font-weight: 400;
    font-size: 12px;
    fill: #8f8f8f;
  }

  section {
    position: relative;
  }

  .sticky {
    position: sticky;
    top: 0;
  }

  .chart-container {
    position: relative;
  }

  circle {
    transition:
      r 300ms ease,
      opacity 500ms ease;
    cursor: pointer;
  }

  h1 {
    font-size: 1.45rem;
    font-weight: 600;
    margin: 0.75rem;
  }

  .step {
    height: 90vh;
    opacity: 0.3;
    transition: opacity 300ms ease;

    display: flex;
    justify-content: center;
    place-items: center;
  }

  .step.active {
    opacity: 1;
  }

  .step-content {
    background-color: white;
    width: 100px;
    border: 1px solid black;
    padding: 1rem;
    border-radius: 8px;

    z-index: 99;
  }

  circle {
    transition:
      cx 500ms ease,
      cy 500ms ease,
      r 300ms ease,
      opacity 500ms ease;
  }

  .steps{
    pointer-events: none;
  }
</style>
