<script>
    import { fade } from "svelte/transition";
    
    export let innerWidth;
    export let yScale;
    // export let height
    let yTicks = yScale.ticks(4);
</script>

<g class="axis y" transform="translate(0 0)">
    {#each yTicks as tick, index}
        <g  transition:fade={{ delay:index*200}} class="tick" transform="translate(0 {yScale(tick)})">
            <line 
            x1="0"
             x2={innerWidth} 
             y1="0" 
             y2="0" 
             stroke={index === 0 ? '#8f8f8f' : '#e5e7eb'} /> 
            <text
            x={0} y={-7}>
            {tick}
            {index === yTicks.length-1 ? ' hours studied' : "" }</text>
        </g>
    {/each}
</g>

<style>
</style>
