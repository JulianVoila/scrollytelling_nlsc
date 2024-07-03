<script>
    import { index } from "d3-array";
    import { fade } from "svelte/transition";

    export let xScale;
    export let innerHeight;
    export let innerWidth;
    let xTicks = xScale.ticks(4);
</script>

<g class="axis x" transform="translate(0 {innerHeight})">
    {#each xTicks as tick, index}
        <g class="tick" transform="translate({xScale(tick)} 0)">
            <g transition:fade={{ delay: index * 100}}
            class="tick"
            transform="translate({xScale(tick)}, 0)">
            <line x1="0" x2="0" y1="0" y2="6" stroke="hsla(212, 10%, 53%, 1)"></line>
            <text
                x={0}
                y={6}
                dy={9}
                dominant-baseline="middle"
                text-anchor={index === 0 ? "left" : "middle"}
            >
                {tick}%</text
            >
            </g>
        </g>
    {/each}
   
        <text transition:fade class="axis-title" x={innerWidth} y="-5" text-anchor="end"
            >Final Grade &darr</text
        >
  
</g>

<style>
</style>
