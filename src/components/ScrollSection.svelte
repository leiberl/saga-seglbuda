<script>
  import { onMount } from 'svelte';

  let element;
  let visible = false;

  /** @type {'narrow' | 'wide' | 'full'} */
  export let width = 'narrow';

  /** @type {number} */
  export let delay = 0;

  onMount(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setTimeout(() => {
            visible = true;
          }, delay);
          observer.unobserve(element);
        }
      },
      { threshold: 0.15, rootMargin: '0px 0px -60px 0px' }
    );
    observer.observe(element);
    return () => observer.disconnect();
  });
</script>

<div
  bind:this={element}
  class="scroll-section {width}"
  class:visible
>
  <slot />
</div>

<style>
  .scroll-section {
    opacity: 0;
    transform: translateY(30px);
    transition: opacity 0.8s ease, transform 0.8s ease;
    transition-delay: var(--delay, 0ms);
  }

  .scroll-section.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .narrow {
    max-width: 680px;
    margin-left: auto;
    margin-right: auto;
    padding-left: 1.5rem;
    padding-right: 1.5rem;
  }

  .wide {
    max-width: 960px;
    margin-left: auto;
    margin-right: auto;
    padding-left: 1.5rem;
    padding-right: 1.5rem;
  }

  .full {
    width: 100%;
  }
</style>
