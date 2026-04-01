<script>
  import { onMount } from 'svelte';
  import timelineData from '../data/timeline.json';

  let items = timelineData;
  let visibleItems = new Set();
  let elements = [];

  onMount(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            const idx = parseInt(entry.target.dataset.idx);
            visibleItems.add(idx);
            visibleItems = visibleItems;
            observer.unobserve(entry.target);
          }
        });
      },
      { threshold: 0.2, rootMargin: '0px 0px -40px 0px' }
    );

    elements.forEach((el) => {
      if (el) observer.observe(el);
    });

    return () => observer.disconnect();
  });
</script>

<div class="timeline">
  <div class="timeline-line"></div>
  {#each items as item, i}
    <div
      class="timeline-item"
      class:visible={visibleItems.has(i)}
      class:right={i % 2 === 1}
      data-idx={i}
      bind:this={elements[i]}
    >
      <div class="timeline-dot"></div>
      <div class="timeline-card">
        <span class="timeline-year">{item.year}</span>
        <h4 class="timeline-label">{item.label}</h4>
        <p class="timeline-text">{item.text}</p>
      </div>
    </div>
  {/each}
</div>

<style>
  .timeline {
    position: relative;
    max-width: 800px;
    margin: 3rem auto;
    padding: 2rem 0;
  }

  .timeline-line {
    position: absolute;
    left: 50%;
    top: 0;
    bottom: 0;
    width: 2px;
    background: #d4cbbf;
    transform: translateX(-50%);
  }

  .timeline-item {
    position: relative;
    width: 50%;
    padding: 0 2.5rem 3rem 0;
    opacity: 0;
    transform: translateX(-20px);
    transition: opacity 0.6s ease, transform 0.6s ease;
  }

  .timeline-item.right {
    margin-left: 50%;
    padding: 0 0 3rem 2.5rem;
    transform: translateX(20px);
  }

  .timeline-item.visible {
    opacity: 1;
    transform: translateX(0);
  }

  .timeline-dot {
    position: absolute;
    right: -7px;
    top: 6px;
    width: 14px;
    height: 14px;
    border-radius: 50%;
    background: #f5f0e8;
    border: 3px solid #4a6741;
    z-index: 1;
  }

  .timeline-item.right .timeline-dot {
    left: -7px;
    right: auto;
  }

  .timeline-card {
    background: white;
    padding: 1.25rem 1.5rem;
    border-radius: 4px;
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
  }

  .timeline-year {
    font-family: 'Inter', sans-serif;
    font-weight: 700;
    font-size: 0.85rem;
    color: #c45a2c;
    letter-spacing: 0.03em;
  }

  .timeline-label {
    font-family: 'Lora', serif;
    font-size: 1.1rem;
    font-weight: 700;
    color: #1a1a1a;
    margin: 0.25rem 0 0.4rem;
  }

  .timeline-text {
    font-family: 'Lora', serif;
    font-size: 0.9rem;
    line-height: 1.55;
    color: #5c5347;
    margin: 0;
  }

  @media (max-width: 768px) {
    .timeline-line {
      left: 20px;
    }

    .timeline-item,
    .timeline-item.right {
      width: 100%;
      margin-left: 0;
      padding: 0 0 2rem 50px;
      transform: translateY(20px);
    }

    .timeline-item.visible {
      transform: translateY(0);
    }

    .timeline-dot,
    .timeline-item.right .timeline-dot {
      left: 13px;
      right: auto;
    }
  }
</style>
