<script>
  import { onMount } from 'svelte';
  import censusData from '../data/census.json';

  let visible = false;
  let element;
  let hoveredIndex = -1;

  const maxSize = Math.max(...censusData.map(d => d.size));
  const barMaxHeight = 200;

  onMount(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          visible = true;
          observer.unobserve(element);
        }
      },
      { threshold: 0.3 }
    );
    observer.observe(element);
    return () => observer.disconnect();
  });
</script>

<div class="census-chart" bind:this={element}>
  <h3 class="chart-title">Fjöldi heimilis&shy;manna á Seglbúðum</h3>
  <p class="chart-subtitle">Samkvæmt manntölum 1703--1920</p>

  <div class="chart-area">
    {#each censusData as item, i}
      <div
        class="bar-group"
        class:visible
        style="--delay: {i * 80}ms"
        on:mouseenter={() => hoveredIndex = i}
        on:mouseleave={() => hoveredIndex = -1}
        role="img"
        aria-label="{item.year}: {item.size} manns"
      >
        <div class="bar-value" class:show={visible}>
          {item.size}
        </div>
        <div
          class="bar"
          style="--height: {(item.size / maxSize) * barMaxHeight}px"
        ></div>
        <div class="bar-year">{item.year}</div>

        {#if hoveredIndex === i}
          <div class="tooltip">
            <strong>{item.head}</strong>
            {#if item.spouse}
              <br /><span class="tooltip-spouse">{item.spouse}</span>
            {/if}
            <br /><span class="tooltip-size">{item.size} manns</span>
            {#if item.note}
              <br /><span class="tooltip-note">{item.note}</span>
            {/if}
          </div>
        {/if}
      </div>
    {/each}
  </div>
</div>

<style>
  .census-chart {
    max-width: 700px;
    margin: 3rem auto;
    padding: 2rem 1rem;
  }

  .chart-title {
    font-family: 'Lora', serif;
    font-size: 1.25rem;
    font-weight: 700;
    text-align: center;
    margin-bottom: 0.25rem;
    color: #1a1a1a;
  }

  .chart-subtitle {
    font-family: 'Inter', sans-serif;
    font-size: 0.8rem;
    text-align: center;
    color: #5c5347;
    margin-bottom: 2rem;
  }

  .chart-area {
    display: flex;
    align-items: flex-end;
    justify-content: space-around;
    gap: 4px;
    height: 260px;
    padding-top: 30px;
  }

  .bar-group {
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 1;
    position: relative;
    cursor: pointer;
  }

  .bar-value {
    font-family: 'Inter', sans-serif;
    font-size: 0.75rem;
    font-weight: 600;
    color: #4a6741;
    margin-bottom: 4px;
    opacity: 0;
    transform: translateY(8px);
    transition: opacity 0.4s ease, transform 0.4s ease;
    transition-delay: var(--delay);
  }

  .bar-value.show {
    opacity: 1;
    transform: translateY(0);
  }

  .bar {
    width: 100%;
    max-width: 48px;
    height: 0;
    background: linear-gradient(to top, #4a6741, #5d7d53);
    border-radius: 2px 2px 0 0;
    transition: height 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94);
    transition-delay: var(--delay);
  }

  .bar-group.visible .bar {
    height: var(--height);
  }

  .bar-group:hover .bar {
    background: linear-gradient(to top, #c45a2c, #d97a52);
  }

  .bar-year {
    font-family: 'Inter', sans-serif;
    font-size: 0.7rem;
    color: #5c5347;
    margin-top: 6px;
    white-space: nowrap;
  }

  .tooltip {
    position: absolute;
    bottom: calc(100% + 12px);
    left: 50%;
    transform: translateX(-50%);
    background: #1a1a1a;
    color: #f5f0e8;
    padding: 0.75rem 1rem;
    border-radius: 4px;
    font-family: 'Inter', sans-serif;
    font-size: 0.75rem;
    line-height: 1.5;
    white-space: nowrap;
    z-index: 10;
    pointer-events: none;
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
  }

  .tooltip::after {
    content: '';
    position: absolute;
    top: 100%;
    left: 50%;
    transform: translateX(-50%);
    border: 6px solid transparent;
    border-top-color: #1a1a1a;
  }

  .tooltip-spouse {
    color: #a3c4d4;
  }

  .tooltip-size {
    color: #d97a52;
    font-weight: 600;
  }

  .tooltip-note {
    color: #999;
    font-style: italic;
  }

  @media (max-width: 768px) {
    .bar-year {
      font-size: 0.6rem;
      transform: rotate(-45deg);
      transform-origin: top center;
    }

    .chart-area {
      gap: 2px;
    }

    .tooltip {
      display: none;
    }
  }
</style>
