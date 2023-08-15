
 <!-- GeneData.svelte -->
<script>
  import { onMount } from 'svelte';
  import { select } from 'd3-selection';
  import { scaleLinear } from 'd3-scale';
  import { linkHorizontal } from 'd3-shape';

  let genesData;
  let geneDetailsScrollPosition = 800; // Adjust this value to your desired default scroll position

export let GeneDetailsClass = '';

let geneDetailsComponent; // Declare it outside the function

  async function showGeneDetails(d) {
    const GeneDetails = (await import('./GeneDetails.svelte')).default;

    if (geneDetailsComponent) {
      geneDetailsComponent.$destroy(); // Destroy the previous component
    }

    geneDetailsComponent = new GeneDetails({
      target: document.body,
      props: { gene: d,GeneDetailsClass: 'bottom-interaction-details',  },
    });

      // Wait for the component to be rendered
      await geneDetailsComponent.$$.fragment;

  }




  // Fetch the gene data from the URL
  onMount(async () => {
    const response = await fetch('https://vda-lab.github.io/assets/genes.json');
    genesData = await response.json();
    drawGeneOverview();
  });

  function drawGeneOverview() {
    const width = 600;
    const height = 600;
    const radius = 250;
    const margin = { top: 20, right: 20, bottom: 30, left: 40 };

    const svg = select('svg.gene-overview')
      .attr('width', width)
      .attr('height', height);

    // const chromosomeCircle = svg
    //   .append('circle')
    //   .attr('cx', width / 2-50)
    //   .attr('cy', height / 2)
    //   .attr('r', radius)
    //   .attr('fill', 'none')
    //   .attr('stroke', 'black');

    const xScale = scaleLinear()
      .domain([0, 4000000]) // Replace 4000000 with the maximum gene_start value
      .range([0, Math.PI * 2]);



// const geneLines = svg
//   .selectAll('path.gene-half-circle')
//   .data(genesData)
//   .enter()
//   .append('path')
//   .attr('class', 'gene-half-circle')
//   .attr('d', (d) => {
//     const angle = xScale(d.gene_start);
//     const radiusOffset = (d.gene_strand === 'forward') ? radius +1.5 : radius -1.5;
//     const cx = width / 2 + Math.cos(angle) * (radiusOffset);
//     const cy = height / 2 + Math.sin(angle) * (radiusOffset);
//     return `M ${cx+1},${cy-1} ${cx+5.25},${cy - 2.25}`;
//   })
//   .attr('stroke', (d) => {
//     if (d.gene_strand === 'forward') {
//       return 'black';
//     } else {
//       return 'black'; // Color for genes on the reverse strand
//     }
//   });


const geneLines = svg
  .selectAll('circle.gene-circle')
  .data(genesData)
  .enter()
  .append('circle')
  .attr('class', 'gene-circle')
  .attr('cx', (d) => {
    const angle = xScale(d.gene_start);
    const radiusOffset = (d.gene_strand === 'forward') ? radius + 2.5 : radius - 2.5;
    return width / 2 + Math.cos(angle) * radiusOffset;
  })
  .attr('cy', (d) => {
    const angle = xScale(d.gene_start);
    const radiusOffset = (d.gene_strand === 'forward') ? radius + 2.5 : radius - 2.5;
    return height / 2 + Math.sin(angle) * radiusOffset;
  })
  .attr('r',2.8) // Circle radius
  .attr('fill', (d) => {
    if (d.gene_strand === 'forward') {
      return 'black';
    } else {
      return 'black'; // Color for genes on the reverse strand
    }
  });










const tickData = [];
  for (let i = 0; i <= 4000000; i += 200000) {
    tickData.push(i);
  }

  const tickLines = svg
    .selectAll('line.tick-line')
    .data(tickData)
    .enter()
    .append('line')
    .attr('x1', (d) => width / 2 + Math.cos(xScale(d)) * radius)
    .attr('y1', (d) => height / 2 + Math.sin(xScale(d)) * radius)
    .attr('x2', (d) => width / 2 + Math.cos(xScale(d)) * (radius + 20)) // Lengthen the tick lines
    .attr('y2', (d) => height / 2 + Math.sin(xScale(d)) * (radius + 20)) // Lengthen the tick lines
    .attr('stroke', 'black') // Lighter color for tick lines
    .attr('opacity', .5); // Opacity of tick lines





    const geneNames = svg
      .selectAll('text.gene-name')
      .data(genesData)
      .enter()
      .append('text')
      .attr('x', (d) => {
        const angle = xScale(d.gene_start);
        return width / 2 + Math.cos(angle) * (radius - radius);
      })
      .attr('y', (d) => {
        const angle = xScale(d.gene_start);
        return height / 2 + Math.sin(angle) * (radius - radius);
      })
      .attr('text-anchor', 'middle')
      .attr('dominant-baseline', 'middle')
      .attr('class', 'gene-name')
      .text((d) => d.gene_name)
      .style('opacity', 0); // Initially hidden


    function showGeneNameAtCenter(geneName) {
      geneNames
        .filter((d) => d.gene_name === geneName)
        .style('opacity', 1);
    }

    function hideGeneNameAtCenter() {
      geneNames.style('opacity', 0);
    }


    geneLines
      .on('mouseover', (event, d) => {
        select(event.currentTarget).attr('opacity', 1).attr('stroke', 'red');
        showGeneName(d.gene_name);
        showGeneNameAtCenter(d.gene_name); // Add this line to show the gene name at the center
      })
      .on('mouseout', (event) => {
        select(event.currentTarget).attr('opacity', 0.5).attr('stroke', 'black');
        showGeneName(null);
        hideGeneNameAtCenter(); // Add this line to hide the gene name at the center
        // Update geneDetailsScrollPosition based on the current scroll position
       

      })
      .on('click', (event, d) => {
        showGeneDetails(d);
      });




    function showGeneName(geneName) {
      svg.select('.gene-name').text(geneName);




    }
  }
</script>

<style>
  svg {
    display: block;
    margin: auto;
  }

  .gene-name {
    font-size: 18px;
    text-anchor: middle;
    dominant-baseline: middle;
    pointer-events: none;
  }
</style>
<div class={`interaction-details ${GeneDetailsClass}`}>
  <!-- Interaction details content -->
</div>

<svg class="gene-overview"></svg>
<div class="gene-name"></div>
