<script>
  import Scrolly from "./Scrolly.svelte";
  import { select } from "d3-selection";

  let value;

  // Initial setup for diamonds with positions and events
  const diamonds = new Array(10).fill(null).map((_, i) => {
    const xOffset = 50 + i * 30; // Staggering the diamonds horizontally
    const yOffset = 100; // Same vertical alignment initially
    return {
      color: 'lightblue',
      initialPos: [xOffset, yOffset], // Initial positions in a line
      groupAPos: [50 + i * 30, yOffset + (i < 5 ? 0 : 50)], // Group A on top row
      groupBPos: [50 + i * 30, yOffset + (i < 5 ? 50 : 0)], // Group B on bottom row
    };
  });

  // Paragraph text for scrolly
  $: steps = [
    `<p>Imagine you're an antique jewelry seller specializing in rare and vintage diamonds. One day, you're approached by a supplier offering a new polishing substance that promises to enhance the brilliance of old diamonds. You're intrigued but skeptical. To make an informed decision, you decide to conduct an experiment using statistics.<br><br>In statistical testing, we structure experiments in terms of null & alternative hypotheses. Your test will have the following hypothesis:<br><br>&Eta;<sub>0</sub>: &mu;<sub>treatment</sub> <= &mu;<sub>control</sub><br>&Eta;<sub>A</sub>: &mu;<sub>treatment</sub> >  &mu;<sub>control</sub><br><br>Your null hypothesis claims that the new polishing substance does not increase the brilliance of old diamonds. The alternative hypothesis claims the opposite; new polishing substance yields superior diamond shine.</p>`,
    `<h1 class='step-title'>Setting the Scene</h1><p>In your collection, you have 20 vintage diamonds. You randomly split them into two groups:<br><br>Group A: 10 diamonds to be polished with your usual method.<br><br>Group B: 10 diamonds to be polished with the new substance.<br><br>After polishing, you evaluate the brilliance of each diamond, assigning a score based on their sparkle and shine.</p>`,
    // More steps...
  ];

  // Update diamonds based on scroll value
  function updateDiamonds() {
    const svg = select("#chart1");
    svg.selectAll("*").remove(); // Clear previous SVG elements

    diamonds.forEach((diamond, index) => {
      const pos = value > 0 ? (index % 2 === 0 ? diamond.groupBPos : diamond.groupAPos) : diamond.initialPos;
      svg.append('path')
        .attr('d', "M17 20, L40 20, L50 40, L27 60, L5 40, Z M5 40, L50 40, M27 60, L40 20, M27 60, L17 20, M22 40, L28 21, L33 40")
        .attr('transform', `translate(${pos[0]}, ${pos[1]}) scale(0.5)`)
        .attr('style', `fill:${diamond.color}; stroke:grey; stroke-width:1`);
    });
  }

  // Reactive function to redraw diamonds when scrolling
  $: if (value !== undefined) {
    updateDiamonds();
  }
</script>

<section>
  <div class="section-container">
    <div class="steps-container">
      <Scrolly bind:value>
        {#each steps as text, i}
          <div class="step" class:active={value === i}>
            <div class="step-content">{@html text}</div>
          </div>
        {/each}
        <div class="spacer" />
      </Scrolly>
    </div>
    <div class="charts-container">
      <svg id="chart1" width="500" height="300" style="border: 1px solid black;"></svg>
    </div>
  </div>
</section>

<style>
  #chart1,
  #chart2 {
    width: 100%;
    height: 100%;
  }
  .chart-one {
    width: 100%;
    height: 100%;
    border: 3px solid skyblue;
  }
  .charts-container {
    position: sticky;
    top: 10%;
    display: grid;
    width: 50%;
    grid-template-columns: 100%;
    height: 85vh;
    border: 0px;
  }

  .section-container {
    margin-top: 1em;
    text-align: center;
    transition: background 100ms;
    display: flex;
  }

  .step {
    height: 110vh;
    display: flex;
    place-items: center;
    justify-content: center;
  }

  .step-content {
    font-size: 18px;
    background: var(--bg);
    color: #ccc; 
    padding: 1rem 1rem;
    display: flex;
    flex-direction: column;
    justify-content: center;
    transition: background 400ms ease;
    text-align: left;
    width: 75%;
    margin: auto;
    max-width: 500px;
    font-family: var(--font-main);
    line-height: 1.3;
    border: 0px;
  }

  .step.active .step-content {
    background: #f1f3f3ee;
    color: var(--squid-ink);
  }

  .steps-container {
    height: 100%;
  }

  .steps-container {
    flex: 1 1 40%;
    z-index: 10;
  }

  /* Responsive adjustments for smaller screens */
  @media screen and (max-width: 950px) {
    .section-container {
      flex-direction: column-reverse;
    }

    .steps-container {
      pointer-events: none;
    }

    .charts-container {
      top: 7.5%;
      width: 95%;
      margin: auto;
    }

    .step {
      height: 130vh;
    }

    .step-content {
      width: 95%;
      max-width: 768px;
      font-size: 17px;
      line-height: 1.6;
    }

    .spacer {
      height: 100vh;
    }
  }
</style>
