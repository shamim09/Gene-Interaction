<script>
  import { onMount} from 'svelte';
  import * as d3 from 'd3';
  import { select } from 'd3-selection';
  import { scaleLinear } from 'd3-scale';
  import { linkHorizontal } from 'd3-shape';
 


  export let interactionsData = [];
  let genesData = [];

  const width = 600;
  const height = 600;
  const margin = { top: 20, right: 20, bottom: 30, left: 40 };


  const minDistance = .4;
  const maxDistance = 1;
  const geneRadius = 2;
  const interactionOpacity = 2;
  const controlPointDistance = 10;
  const majorAngle = (2 * Math.PI) / 4;
  const minorAngle = majorAngle / 2;

   let svg; // Declare the svg variable here




 import InteractionDetails from './InteractionDetails.svelte'; // Adjust the path as needed

let interactionDetailsComponent;

      export let interactionDetailsClass = ''; 

      

    async function showInteractionDetails(d) {
      const InteractionDetails = (await import('./InteractionDetails.svelte')).default;

      if (interactionDetailsComponent) {
        interactionDetailsComponent.$destroy();
      }

      interactionDetailsComponent = new InteractionDetails({
        target: document.body,
        props: {
          interaction: d,
          interactionDetailsClass: 'bottom-interaction-details', // Pass the class name here
        },
      });

      // Wait for the component to be rendered
      await interactionDetailsComponent.$$.fragment;
    }



  onMount(async () => {
    const interactionsResponse = await fetch('/src/routes/merged_data.json');
    interactionsData = await interactionsResponse.json();
    console.log(interactionsData);
   const genesResponse = await fetch('/src/routes/merged_data.json');
    genesData = await genesResponse.json();
    console.log(genesData);


    svg = d3.select('.hive-plot')
      .attr('width', width)
      .attr('height', height);

    createVisualization();
  });



  function createVisualization() {


    const nodesByType = genesData.reduce((acc, gene) => {
      const typeKey = gene.axes;
      if (!acc[typeKey]) {
        acc[typeKey] = { key: typeKey, values: [], count: 0 };
      }
      acc[typeKey].values.push(gene);
      acc[typeKey].count++;
      return acc;
    }, {});

    const nodesByTypeArray = Object.values(nodesByType);
    nodesByTypeArray.forEach(type => {
      type.values.forEach((gene, i) => {
        gene.index = i;
      });
    });



    // const angleScale = d3.scaleOrdinal()
    //   .domain(["source", "source-target", "target-source", "target"])
    //   .range([5, majorAngle - minorAngle,  2 * majorAngle,majorAngle - minorAngle,]);


    // const axes = svg.selectAll(".axis")
    //   .data(nodesByTypeArray)
    //   .enter()
    //   .append("line")
    //   .attr("class", "axis")
    //   .attr("transform", d => `rotate(${degrees(angleScale(d.key))})`)
    //   .attr("x1", radiusScale(-1))
    //   .attr("x2", d => radiusScale(d.count + 1));

 // Calculate the angle for each gene type
  const typeAngles = {
    "regulator": -Math.PI / 2, // Red, stacked upwards
    "manager": Math.PI / 4,    // Yellow, stacked to the bottom right
    "workhorse": (3 * Math.PI) / 4, // Green, stacked to the bottom left
  };

  const angleScale = d3.scaleOrdinal()
    .domain(["regulator", "manager", "workhorse"]) // Update domain for gene types
    .range([typeAngles["regulator"], typeAngles["manager"], typeAngles["workhorse"]]);


    const radiusScale = d3.scaleLinear()
      .range([minDistance, maxDistance]);


  const axes = svg.selectAll(".axis")
    .data(nodesByTypeArray)
    .enter()
    .append("g") // Create a group element for each axis
    .attr("class", "axis")
    .attr("transform", d => `rotate(${degrees(angleScale(d.key))})`);




  const geneCircles = svg
    .selectAll(".gene-circle")
    .data(genesData)
    .enter()
    .append("circle")
    .attr("class", "gene-circle")
    .attr("cx", d => {
      const angle = angleScale(d.axes);
      const radius = radiusScale(d.index);
      return width / 2 + Math.cos(angle) * radius;
    })
    .attr("cy", d => {
      const angle = angleScale(d.axes);
      const radius = radiusScale(d.index);
      return height / 2 + Math.sin(angle) * radius;
    })
    .attr("r", geneRadius)
    .style("fill", d => {
      if (d.axes === "regulator") {
        return "red";
      } else if (d.axes === "manager") {
        return "yellow";
      } else if (d.axes === "workhorse") {
        return "green";
      }
    });
          


    geneCircles
      .on('mouseover', (event, d) => {
        select(event.currentTarget).attr('opacity', 1).attr('stroke', 'red');

      })
      .on('mouseout', (event) => {
        select(event.currentTarget).attr('opacity', 1).attr('stroke', ' ');
      });

 // ...

const interactions = svg
  .append("g")
  .attr("class", "interactions")
  .selectAll(".interaction")
  .data(interactionsData)
  .enter()
  .append("a")

  .append("path")
  .style("opacity", 0.3)
  .attr("class", "interaction")
  .attr("d", d => drawInteractionPath(d))
  .style("opacity", interactionOpacity)
  .style("stroke", "grey") // Set the default stroke color to white
  .style("stroke-width", 1) // Add stroke-width to control the thickness
  .style("fill", "none") // Set fill to none to avoid filling the path
  .on("mouseover", (event, d) => {
    select(event.currentTarget)
      .style("opacity", 0.8)
      .style("stroke", "red")
      .style("stroke-width", 5);
     })
    .on("mouseout", (event, d) => {
      const path = select(event.currentTarget);
      path.style("opacity", interactionOpacity).style("stroke", "grey").style("stroke-width", 1);
    })
    .on('click', (event, d) => {
  showInteractionDetails(d);
});

// ...





      function drawInteractionPath(d) {

        const sourceGene = genesData.find(gene => gene.gene_name.toLowerCase() === d.from_gene_name.toLowerCase());
        const targetGene = genesData.find(gene => gene.gene_name.toLowerCase() === d.to_ngn.toLowerCase());

        if (!sourceGene || !targetGene) {
          return;
        }

        // Rest of the code for drawing the path




        if (!sourceGene || !targetGene) {
          return;
        }

        const sourceAngle = angleScale(sourceGene.axes);
        const targetAngle = angleScale(targetGene.axes);
        const sourceRadius = radiusScale(sourceGene.index);
        const targetRadius = radiusScale(targetGene.index);

        // const controlPointRadius = (sourceRadius + targetRadius) / 2;
        // const controlPointAngle = (sourceAngle + targetAngle) / 2;


        const controlPointRadius = 150; // Distance of control point
        const controlPointAngle = (sourceAngle + targetAngle) / 2;

              

        const sourceX = width / 2 + Math.cos(sourceAngle) * sourceRadius;
        const sourceY = height / 2 + Math.sin(sourceAngle) * sourceRadius;

        const controlPointX = width / 2 + Math.cos(controlPointAngle) * controlPointRadius;
        const controlPointY = height / 2 + Math.sin(controlPointAngle) * controlPointRadius;

        const targetX = width / 2 + Math.cos(targetAngle) * targetRadius;
        const targetY = height / 2 + Math.sin(targetAngle) * targetRadius;

        return `M ${sourceX},${sourceY}
                Q ${controlPointX},${controlPointY}
                  ${targetX},${targetY}`;




      }





  // Modify the interactionMouseover function
  function interactionMouseover(event) {
    select(event.currentTarget)
      .attr('stroke', 'red')
      .style('opacity', 1);
  }

  // Modify the interactionMouseout function
  function interactionMouseout(event) {
    select(event.currentTarget)
      .attr('stroke', '')
      .style('opacity', interactionOpacity);
  }


    function degrees(radians) {
      return radians * 180 / Math.PI;
    }

}
</script>

<style>
  .hive-plot {
    display: block;
    margin: auto;
    position: absolute;
  }




</style>

<div class={`interaction-details ${interactionDetailsClass}`}>
  <!-- Interaction details content -->
</div>

<div>
  <svg class="hive-plot" viewBox={`60 258 ${width} ${height}`}>
    <!-- Rest of your SVG content -->
  </svg>
</div>
