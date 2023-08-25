<!-- GeneDetails.svelte -->


<script>
  export let gene;
  export let GeneDetailsClass = '';

  // import { setContext } from 'svelte';
  // // Create a context variable to pass the gene data
  // setContext('geneContext', gene);

  // Replace xxxxx with the actual gene ID
  const regulonDBLink = `http://regulondb.ccg.unam.mx/gene?organism=ECK12&format=jsp&type=gene&term=${gene.gene_id}`;
</script>








<style>
  .interaction-details {
    position: fixed;
    bottom: 20px;
    left: 20px;
    width: 500px;
    height: 500px;
    background-color: #ffffff;
    border: 1px solid #ccc;
    padding: 10px;
    box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease-in-out;
    transform: translateY(100%);
    z-index: 9999;
  }

  .bottom-interaction-details {
    transform: translateY(0);
  }

  .interaction-details h2 {
    font-size: 18px;
    margin-bottom: 10px;
    color: #333;
  }

  .interaction-details ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
  }

  .interaction-details li {
    margin-bottom: 5px;
    color: #666;
  }

  .interaction-details a {
    color: #007bff;
    text-decoration: none;
  }
</style>











<div class={`interaction-details ${GeneDetailsClass}`}>


<h2>GENE NAME: { gene.gene_name}</h2>
<ul>
  <li>ID: {gene.gene_id}</li>
  <li>Blattner number: {gene.gene_blattner_number}</li>
  <li>Start: {gene.gene_start}</li>
  <li>Stop: {gene.gene_stop}</li>
  <li>Strand: {gene.gene_strand}</li>
  <li>Product: {gene.gene_product}</li>
  {#if gene.gene_confidence}
    <li>Confidence: {gene.gene_confidence}</li>
  {/if}
  {#if gene.gene_evidence}
    <li>Evidence:
      <ul>
        {#each gene.gene_evidence.split(',') as evidence}
          <li>{evidence.trim()}</li>
        {/each}
      </ul>
    </li>
  {/if}
  <li><a href="{regulonDBLink}" target="_blank">RegulonDB link</a></li>
</ul>
</div>