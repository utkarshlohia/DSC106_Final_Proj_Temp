<script>
  import Scrolly from "./Scrolly.svelte";
  import { select } from "d3-selection";

  let value;

  // Initial setup for diamonds with positions and events
  const diamonds = new Array(20).fill(null).map((_, i) => {
    const xOffset = 50 + (i % 10) * 30; // Staggering the diamonds horizontally in rows of 10
    const yOffset = 100 + Math.floor(i / 10) * 50; // Two rows for initial positions
    return {
      color: 'lightblue',
      initialPos: [xOffset, yOffset], // Initial positions in a grid
      groupAPos: [50 + (i % 10) * 30, 100], // Group A on the left side
      groupBPos: [400 + (i % 10) * 30, 100], // Group B on the right side
    };
  });

  // Paragraph text for scrolly
  $: steps = [
      `<p>
        Imagine you're an antique jewelry seller specializing in rare and vintage 
        diamonds. One day, you're approached by a supplier offering a new polishing 
        substance that promises to enhance the brilliance of old diamonds. You're 
        intrigued but skeptical. To make an informed decision, you decide to conduct
        an experiment using statistics.
        <br><br>In statistical testing, we structure experiments in terms of null & 
        alternative hypotheses. Your test will have the following hypothesis:<br><br>
        &Eta;<sub>0</sub>: &mu;<sub>treatment</sub> <= &mu;<sub>control</sub>
        <br>
        &Eta;<sub>A</sub>: &mu;<sub>treatment</sub> >  &mu;<sub>control</sub>
        <br><br>Your null hypothesis claims that the new polishing substance does not increase the brilliance of old diamonds. The alternative hypothesis claims the opposite; new polishing substance yields superior diamond shine. 
      </p>`,

    `<h1 class='step-title'>Setting the Scene</h1>
      <p>
        In your collection, you have 20 vintage diamonds. You randomly split them 
        into two groups:
        <br><br>Group A: 10 diamonds to be polished with your usual method.
        <br><br>Group B: 10 diamonds to be polished with the new substance.
        <br><br>After polishing, you evaluate the brilliance of each diamond, 
        assigning a score based on their sparkle and shine.
      </p>`,

    `<h1 class='step-title'>Observing the Results</h1>
      <p>
        The average brilliance score for diamonds polished with your usual method is 50, 
        and for those polished with the new substance, it's 55. 
        <br><br>The new substance seems promising, but you need to determine if 
        this difference is genuinely due to the polishing substance or if it could have occurred by chance.
      </p>`,

    `<h1 class='step-title'>The Permutation Test</h1>
    <p>
      To address this, you decide to use a permutation test. This test will help
       you determine whether the observed difference in brilliance scores is 
       statistically significant.
    </p>`,

    `<h1 class='step-title'>Step 1: Combining and Shuffling</h1>
    <p>
      First, you combine all the brilliance scores into a single pool, 
      disregarding which polishing method was used. Then, you randomly 
      shuffle, which we call permuting, these scores and split them into 
      two new groups of 10 diamonds each.
    </p>`,

    `<h1 class='step-title'>Step 2: Calculating the Difference</h1>
    <p>
      For each permutation, you calculate the difference in average brilliance 
      scores between the two new groups. This process is repeated many times 
      (e.g., 10,000 permutations) to build a distribution of differences under 
      the null hypothesis—that the polishing method doesn't affect the brilliance.
    </p>`,

    `<h1 class='step-title'>Step 3: Comparing the Observed Difference</h1>
    <p>
      Next, you compare your observed difference (55 - 50 = 5) to the 
      distribution of differences from your permutations. You count how many 
      times the permutation differences are equal to or greater than your 
      observed difference.
    </p>`,

    `<h1 class='step-title'>Calculating the p-Value</h1>
     <p>
      The p-value represents the probability of obtaining an observed difference as extreme as, or more extreme than, what was actually observed, assuming the null hypothesis is true. In this context, it's the fraction of permutations where the difference in brilliance scores is 5 or more.
      <br><br>For example, if only 100 out of 10,000 permutations result in a difference of 5 or more, the p-value would be 100/10,000 = 0.01.

    </p>`,

    `<h1 class='step-title'>Interpreting the Results</h1>
      <p>
        If only a small fraction of these permutations result in a difference of 5 or more, you can conclude that the observed difference is unlikely to have occurred by chance. This would suggest that the new polishing substance genuinely enhances the brilliance of the diamonds.
        <br><br>However, if a large number of permutations result in a difference of 5 or more, the observed difference could easily occur by random chance, and you might reconsider the effectiveness of the new substance.
        <br><br>We measure this statistically by using the p-value. If the p-value is very small (typically less than 0.05), it suggests that the observed difference is unlikely to have occurred by chance, indicating that the new polishing substance has a significant effect on the brilliance of the diamonds. However, if the p-value is large, it suggests that the observed difference could easily occur by random chance.
    </p>`,

  `<h1 class='step-title'>Decision Making</h1>
    <p>
      By comparing the observed difference to this distribution and calculating the p-value, you make an informed decision about the new polishing substance. Since the p-value is less than 0.05, you might adopt the new method, enhancing your reputation as a seller of the most brilliant antique diamonds.
    </p>
    `,
  ];

  // Update diamonds based on scroll value
  function updateDiamonds() {
    const svg = select("#chart1");
    svg.selectAll("*").remove(); // Clear previous SVG elements

    diamonds.forEach((diamond, index) => {
      const pos = value > 0 ? (index < 10 ? diamond.groupAPos : diamond.groupBPos) : diamond.initialPos;
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
      <svg id="chart1" width="600" height="300" style="border: 1px solid black;"></svg>
    </div>
  </div>
</section>

<style>
  #chart1 {
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
