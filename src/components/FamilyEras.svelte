<script>
  import { onMount } from 'svelte';

  const families = [
    {
      head: 'Jón Órækjason', spouse: 'Guðrún Snorradóttir',
      years: '1703', size: 11, color: '#5c7a4e',
      detail: 'Elsta þekkta fjölskyldan. Bóndi 39 ára, þrjár dætur, vinnuhjú og hlutabúðarmaður. Patronymið Órækjason er sjaldgæft.',
      gapAfter: '98 ára eyða — Skaftáreldar og Móðuharðindi'
    },
    {
      head: 'Jón Sverrisson', spouse: 'Halldóra Þorláksdóttir',
      years: '1801', size: 6, color: '#6d8b74',
      detail: 'Bóndi 32 ára. Halldóra 46 ára, eldri en maður sinn, með tvö börn úr fyrra hjúskap.',
      gapAfter: null
    },
    {
      head: 'Þorlákur Árnason', spouse: null,
      years: '1816', size: 6, color: '#7ba7bc',
      detail: 'Ekkill 69 ára. Fjórir fullorðnir börn heima, og Rannveig 10 ára fósturbarn.',
      gapAfter: null
    },
    {
      head: 'Ólafur Ólafsson', spouse: 'Anna Eyjólfsdóttir',
      years: '1835–1860', size: 14, color: '#8B6914',
      detail: 'Sat lengst allra — 25 ár. Byrjaði með föður sínum. Missti fyrstu konu, giftist húsráðskonunni Önnu. Sigríður Pálsdóttir, 89 ára, var elsti heimilismaðurinn.',
      gapAfter: null
    },
    {
      head: 'Jón Jónsson', spouse: 'Katrín Pálsdóttir',
      years: '1870–1890', size: 20, color: '#4a6741',
      detail: 'Stærsta heimilið í sögu bæjarins: 20 manns árið 1890. Steingrímur Sveinsson, 87 ára, elsti íbúinn. Sonur Páll bjó á bænum hlið við hlið.',
      gapAfter: null
    },
    {
      head: 'Jón Þorkelsson', spouse: 'Ólöf Jónsdóttir',
      years: '1901–1910', size: 12, color: '#a87b5e',
      detail: 'Byssusmiður auk bónda. Lést á milli 1901 og 1910. Ólöf tók við bænum sem ekkja — sjaldgæft á þessum tíma.',
      gapAfter: null
    },
    {
      head: 'Helgi Jónsson', spouse: 'Gyðríður Pálsdóttir',
      years: '1920–1949', size: 12, color: '#5d7d53',
      detail: 'Bóndi í 29 ár. Sat í sveitarstjórn Kirkjubæjarhrepps. Lést 55 ára. Gyðríður hélt áfram — hún lifði til 97 ára aldurs (d. 1994).',
      gapAfter: null
    },
    {
      head: 'Jón Helgason', spouse: 'Guðrún Þorkelsdóttir',
      years: '1950–1979', size: null, color: '#2c5282',
      detail: 'Yngstur fjögurra systkina. Frá bóndasyni í Landbroti til forseta Alþingis. Varðveitti sögu bæjarins í ritum og örnefnaskrá.',
      gapAfter: null
    }
  ];

  let visible = false;
  let element;

  onMount(() => {
    const obs = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) { visible = true; obs.unobserve(element); }
      },
      { threshold: 0.05 }
    );
    obs.observe(element);
    return () => obs.disconnect();
  });
</script>

<div class="fe" bind:this={element}>
  <h3 class="fe-title">Ættir Seglbúða</h3>
  <p class="fe-subtitle">Átta fjölskyldur á tæpum þremur öldum</p>

  {#each families as fam, i}
    <!-- Family card -->
    <div
      class="fe-card"
      class:fe-vis={visible}
      style="--fc: {fam.color}; --delay: {i * 100}ms"
    >
      <div class="fe-accent"></div>
      <div class="fe-body">
        <div class="fe-top">
          <span class="fe-years">{fam.years}</span>
          {#if fam.size}
            <span class="fe-count">{fam.size} manns</span>
          {/if}
        </div>
        <h4 class="fe-head">{fam.head}</h4>
        {#if fam.spouse}
          <p class="fe-spouse">og {fam.spouse}</p>
        {/if}
        {#if fam.size}
          <div class="fe-dots">
            {#each { length: fam.size } as _, j}
              <span
                class="fe-dot"
                style="--dot-delay: {i * 100 + j * 30}ms"
                class:fe-vis={visible}
              ></span>
            {/each}
          </div>
        {/if}
        <p class="fe-detail">{fam.detail}</p>
      </div>
    </div>

    <!-- Transition -->
    {#if fam.gapAfter}
      <div class="fe-gap" class:fe-vis={visible} style="--delay: {i * 100 + 60}ms">
        <div class="fe-gap-line"></div>
        <span class="fe-gap-text">{fam.gapAfter}</span>
        <div class="fe-gap-line"></div>
      </div>
    {:else if i < families.length - 1}
      <div class="fe-join" class:fe-vis={visible} style="--delay: {i * 100 + 60}ms"></div>
    {/if}
  {/each}
</div>

<style>
  .fe {
    max-width: 600px;
    margin: 3rem auto;
    padding: 0 1rem;
  }

  .fe-title {
    font-family: 'Lora', serif;
    font-size: 1.35rem;
    font-weight: 700;
    text-align: center;
    color: #1a1a1a;
    margin-bottom: 0.2rem;
  }
  .fe-subtitle {
    font-family: 'Inter', sans-serif;
    font-size: 0.8rem;
    text-align: center;
    color: #5c5347;
    margin-bottom: 2.5rem;
  }

  /* ── Card ───────────────────────────── */
  .fe-card {
    display: flex;
    opacity: 0;
    transform: translateY(16px);
    transition: opacity 0.6s ease var(--delay), transform 0.6s ease var(--delay);
  }
  .fe-card.fe-vis {
    opacity: 1;
    transform: translateY(0);
  }

  .fe-accent {
    width: 4px;
    background: var(--fc);
    border-radius: 2px 0 0 2px;
    flex-shrink: 0;
  }
  .fe-body {
    flex: 1;
    background: white;
    padding: 1rem 1.25rem;
    border-radius: 0 4px 4px 0;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  }

  .fe-top {
    display: flex;
    justify-content: space-between;
    align-items: baseline;
  }
  .fe-years {
    font-family: 'Inter', sans-serif;
    font-weight: 700;
    font-size: 0.75rem;
    color: var(--fc);
    letter-spacing: 0.04em;
  }
  .fe-count {
    font-family: 'Inter', sans-serif;
    font-weight: 600;
    font-size: 0.65rem;
    color: var(--fc);
    opacity: 0.7;
  }

  .fe-head {
    font-family: 'Lora', serif;
    font-size: 1.15rem;
    font-weight: 700;
    color: #1a1a1a;
    margin: 0.1rem 0 0;
    line-height: 1.3;
  }
  .fe-spouse {
    font-family: 'Lora', serif;
    font-size: 0.9rem;
    color: #5c5347;
    font-style: italic;
    margin: 0 0 0.4rem;
  }

  /* ── People dots ────────────────────── */
  .fe-dots {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin: 0.5rem 0 0.7rem;
  }
  .fe-dot {
    width: 9px;
    height: 9px;
    border-radius: 50%;
    background: var(--fc);
    opacity: 0;
    transform: scale(0);
    transition: opacity 0.4s ease var(--dot-delay), transform 0.4s ease var(--dot-delay);
  }
  .fe-dot.fe-vis {
    opacity: 0.6;
    transform: scale(1);
  }

  .fe-detail {
    font-family: 'Lora', serif;
    font-size: 0.82rem;
    line-height: 1.55;
    color: #5c5347;
    margin: 0;
  }

  /* ── Transitions ────────────────────── */
  .fe-gap {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    padding: 1.2rem 0;
    opacity: 0;
    transition: opacity 0.6s ease var(--delay);
  }
  .fe-gap.fe-vis { opacity: 1; }

  .fe-gap-line {
    flex: 1;
    height: 1px;
    background: #d4cbbf;
  }
  .fe-gap-text {
    font-family: 'Inter', sans-serif;
    font-size: 0.65rem;
    font-weight: 500;
    color: #9e8e7e;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    text-align: center;
    white-space: nowrap;
  }

  .fe-join {
    width: 2px;
    height: 16px;
    background: #d4cbbf;
    margin: 0.4rem auto;
    opacity: 0;
    transition: opacity 0.6s ease var(--delay);
  }
  .fe-join.fe-vis { opacity: 1; }
</style>
