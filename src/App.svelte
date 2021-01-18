<script>
import CNNtext from './CNNtext.svelte';
import MadlibGenerator from './MadlibGenerator.svelte'
let words='';
let header;
let showOriginal =0;







</script>
<p>{header}</p>



<CNNtext bind:articleText={words} bind:headline={header}/>


<MadlibGenerator textToTransform={words} bind:madlibCreated = {showOriginal}/>


{#if showOriginal != 0}
   <br>
   <br>
   <br>
   <p>--------------original---------------</p> 
    <p>{words}</p>
{/if}