<!-- InteractionDetails.svelte -->

<script>
  export let interaction;
  export let interactionsData;
  export let interactionDetailsClass = '';

  const sourceGeneLink = `http://regulondb.ccg.unam.mx/gene?organism=ECK12&format=jsp&type=gene&term=${interaction.gene_id}`;
  const targetGeneLink = `http://regulondb.ccg.unam.mx/gene?organism=ECK12&format=jsp&type=gene&term=${interaction.gene_id}`;


  console.log('Source Gene Link:', sourceGeneLink);
console.log('Target Gene Link:', targetGeneLink);
</script>
<!-- InteractionDetails.svelte -->

<style>
  .interaction-details {
    position: fixed;
    bottom: 20px;
    left: 20px;
    width: 300px;
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

<div class={`interaction-details ${interactionDetailsClass}`}>
  <h2>Interaction Details</h2>
  <ul>
    <li>From Gene: <a href="{sourceGeneLink}" target="_blank">{interaction.from_gene_name}</a></li>
    <li>To Gene: <a href="{targetGeneLink}" target="_blank">{interaction.to_gene_name}</a></li>
    <li>Function: {interaction.function}</li>
    <li>Confidence: {interaction.confidence}</li>
    <li>From ID: {interaction.from_id}</li>
    <li>To ID: {interaction.to_id}</li>
    <li><a href="{targetGeneLink}" target="_blank">RegulonDB targetGeneLink</a></li>
    <li><a href="{sourceGeneLink}" target="_blank">RegulonDB sourceGeneLink</a></li>
    <!-- Add more details as needed -->
  </ul>
</div>
