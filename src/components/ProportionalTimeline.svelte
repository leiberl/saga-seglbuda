<script>
  import { onMount } from 'svelte';

  const PX_PER_YEAR = 1.6;
  const PAD = { top: 80, bottom: 80 };

  const items = [
    { year: 934, label: 'Eldgjárgos', text: 'Hraunið skapar landið sem bæirnir munu standa á. Landbrotshólar myndast.', cat: 'land', w: 3 },
    { year: 1150, label: 'Fyrsta byggð á hrauninu', text: 'Fornleifarannsóknir sýna bæi reista beint á Eldgjágjósku.', cat: 'land', w: 2 },
    { year: 1280, label: '«í Landbroti»', text: 'Héraðið nefnt í 149. kafla Brennu-Njáls sögu.', cat: 'doc', w: 1 },
    { year: 1343, label: '«selbudir»', text: 'Jörðin nefnd í máldaga Kirkjubæjarklausturs — elsta heimildin.', cat: 'name', w: 3 },
    { year: 1402, label: 'Plágan', text: 'Tólfahringur — hringur 12 bæja — eyðist í pestfaraldri.', cat: 'disaster', w: 2 },
    { year: 1550, label: 'Siðaskipti', text: 'Klaustrið lagt niður. Seglbúðir áfram klausturseign.', cat: 'doc', w: 1 },
    { year: 1641, label: '«Seglbúðir»', text: 'Nafnformið birtist fyrst í skjölum. Hljóðbreytingin l → gl lokið.', cat: 'name', w: 2 },
    { year: 1703, label: 'Jón Órækjason', text: 'Fyrsta manntal. Bóndi 39 ára, Guðrún Snorradóttir — 11 manns.', cat: 'family', w: 2 },
    { year: 1728, label: 'Bruni í Höfn', text: 'Jarðabókin brenn í Kaupmannahöfn. Gögn um sýsluna tapast.', cat: 'disaster', w: 1 },
    { year: 1783, label: 'Skaftáreldar', text: 'Hraunið nær Landbroti. Oddur lifir á silungi úr Grenlæk.', cat: 'disaster', w: 3 },
    { year: 1835, label: 'Ólafur Ólafsson', text: 'Tekur við bænum. Situr í 25 ár — lengst allra bænda.', cat: 'family', w: 1 },
    { year: 1844, label: 'Á korti', text: 'Seglbúðir á Uppdráttur Íslands eftir Björn Gunnlaugsson.', cat: 'doc', w: 1 },
    { year: 1870, label: 'Jón Jónsson', text: 'Nýr bóndi. Heimilið vex í 20 manns — stærst í sögu bæjarins.', cat: 'family', w: 1 },
    { year: 1887, label: 'Til sölu', text: 'Jörðin auglýst: 1.200 kr. Lækkar í 900 kr. árið 1891.', cat: 'doc', w: 1 },
    { year: 1910, label: 'Ólöf húsbóndi', text: 'Ekkja tekur við bænum — sjaldgæft á þessum tíma.', cat: 'family', w: 2 },
    { year: 1920, label: 'Helgi og Gyðríður', text: 'Helgi Jónsson tekur við, 26 ára. Bóndi í 29 ár.', cat: 'family', w: 1 },
    { year: 1949, label: 'Kynslóðaskipti', text: 'Helgi deyr 55 ára. Jón Helgason tekur við, 18 ára.', cat: 'family', w: 2 },
    { year: 1972, label: '«Glæsilegustu bæir»', text: '„Meðal glæsilegustu bændabýla á Íslandi" — Tíminn.', cat: 'doc', w: 1 },
    { year: 1979, label: 'Forseti Alþingis', text: 'Jón Helgason frá Seglbúðum kjörinn forseti.', cat: 'politics', w: 3 }
  ];

  const minYear = items[0].year;
  const maxYear = items[items.length - 1].year;

  // Proportional Y positions
  const positions = items.map(d => PAD.top + (d.year - minYear) * PX_PER_YEAR);
  const totalHeight = positions[positions.length - 1] + PAD.bottom;

  // Century marks
  const centuries = [];
  for (let c = 1000; c <= 1900; c += 100) {
    if (c > minYear && c < maxYear) {
      centuries.push({ year: c, y: PAD.top + (c - minYear) * PX_PER_YEAR });
    }
  }

  // Name evolution annotation: connect 1343 → 1641
  const nameStart = positions[items.findIndex(d => d.year === 1343)];
  const nameEnd = positions[items.findIndex(d => d.year === 1641)];

  // Gap annotation for the long silence (934→1150)
  const silenceY = PAD.top + (((1150 - 934) / 2) + (934 - minYear)) * PX_PER_YEAR;

  // Category colors
  const catColor = {
    land: '#8B6914', doc: '#5c5347', name: '#c45a2c',
    disaster: '#9e2a2a', family: '#4a6741', politics: '#2c5282'
  };
  const dotR = { 1: 7, 2: 10, 3: 14 };

  // Scroll reveal
  let visibleItems = new Set();
  let elements = [];

  onMount(() => {
    const obs = new IntersectionObserver(
      (entries) => {
        entries.forEach(e => {
          if (e.isIntersecting) {
            visibleItems.add(+e.target.dataset.idx);
            visibleItems = visibleItems;
            obs.unobserve(e.target);
          }
        });
      },
      { threshold: 0.15, rootMargin: '0px 0px -30px 0px' }
    );
    elements.forEach(el => { if (el) obs.observe(el); });
    return () => obs.disconnect();
  });
</script>

<div class="ptl" style="height: {totalHeight}px">

  <!-- Scale reference -->
  <div class="ptl-scale" style="top: {PAD.top - 50}px">
    <div class="ptl-scale-bar" style="height: {100 * PX_PER_YEAR}px"></div>
    <span class="ptl-scale-label">100 ár</span>
  </div>

  <!-- Spine -->
  <div class="ptl-spine"></div>

  <!-- Century marks -->
  {#each centuries as c}
    <div class="ptl-century" style="top: {c.y}px">
      <span class="ptl-century-year">{c.year}</span>
    </div>
  {/each}

  <!-- Gap annotation: 934→1150 -->
  <div class="ptl-silence" style="top: {silenceY}px">
    <span class="ptl-silence-text">þögul öld</span>
  </div>

  <!-- Name evolution bracket -->
  <div class="ptl-name-bracket" style="top: {nameStart + 6}px; height: {nameEnd - nameStart - 12}px">
    <span class="ptl-name-transform">Sel­búðir → Segl­búðir</span>
  </div>

  <!-- Events -->
  {#each items as item, i}
    <div
      class="ptl-event"
      class:ptl-right={i % 2 === 1}
      class:ptl-vis={visibleItems.has(i)}
      class:ptl-name={item.cat === 'name'}
      class:ptl-disaster={item.cat === 'disaster'}
      style="top: {positions[i] - 4}px"
      data-idx={i}
      bind:this={elements[i]}
    >
      <!-- Left card (desktop even items) -->
      <div class="ptl-side ptl-side-l">
        <div class="ptl-card">
          <span class="ptl-year" style="color: {catColor[item.cat]}">{item.year}</span>
          <h4 class="ptl-label">{item.label}</h4>
          <p class="ptl-text">{item.text}</p>
        </div>
      </div>

      <!-- Dot -->
      <div class="ptl-dot-col">
        <div
          class="ptl-dot"
          style="width:{dotR[item.w]}px; height:{dotR[item.w]}px; background:{catColor[item.cat]}"
        ></div>
      </div>

      <!-- Right card (desktop odd items + all mobile) -->
      <div class="ptl-side ptl-side-r">
        <div class="ptl-card">
          <span class="ptl-year" style="color: {catColor[item.cat]}">{item.year}</span>
          <h4 class="ptl-label">{item.label}</h4>
          <p class="ptl-text">{item.text}</p>
        </div>
      </div>
    </div>
  {/each}
</div>

<style>
  /* ── Container ─────────────────────── */
  .ptl {
    position: relative;
    max-width: 900px;
    margin: 2rem auto;
    padding: 0 1rem;
  }

  /* ── Spine ──────────────────────────── */
  .ptl-spine {
    position: absolute;
    left: 50%;
    top: 0;
    bottom: 0;
    width: 2px;
    background: linear-gradient(
      to bottom,
      transparent 0px,
      #d4cbbf 60px,
      #d4cbbf calc(100% - 60px),
      transparent
    );
    transform: translateX(-50%);
    z-index: 0;
  }

  /* ── Scale reference ────────────────── */
  .ptl-scale {
    position: absolute;
    right: calc(50% + 60px);
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .ptl-scale-bar {
    width: 2px;
    background: #c4bdb3;
    border-radius: 1px;
  }
  .ptl-scale-label {
    font-family: 'Inter', sans-serif;
    font-size: 0.6rem;
    font-weight: 600;
    color: #a09889;
    letter-spacing: 0.08em;
    white-space: nowrap;
  }

  /* ── Century marks ──────────────────── */
  .ptl-century {
    position: absolute;
    left: calc(50% - 20px);
    width: 40px;
    height: 0;
    z-index: 0;
  }
  .ptl-century-year {
    position: absolute;
    left: 50%;
    transform: translateX(-50%) translateY(-50%);
    font-family: 'Inter', sans-serif;
    font-size: 0.6rem;
    font-weight: 500;
    color: #c4bdb3;
    background: #f5f0e8;
    padding: 0 6px;
    letter-spacing: 0.06em;
  }

  /* ── Gap annotation ─────────────────── */
  .ptl-silence {
    position: absolute;
    left: 50%;
    transform: translateX(-50%) translateY(-50%);
    z-index: 0;
  }
  .ptl-silence-text {
    font-family: 'Lora', serif;
    font-style: italic;
    font-size: 0.75rem;
    color: #c4bdb3;
    letter-spacing: 0.1em;
  }

  /* ── Name evolution bracket ─────────── */
  .ptl-name-bracket {
    position: absolute;
    left: calc(50% + 40px);
    width: 3px;
    border-radius: 2px;
    background: linear-gradient(to bottom, #c45a2c, #d97a52);
    opacity: 0.25;
    z-index: 0;
  }
  .ptl-name-transform {
    position: absolute;
    top: 50%;
    left: 12px;
    transform: translateY(-50%);
    font-family: 'Lora', serif;
    font-style: italic;
    font-size: 0.7rem;
    color: #c45a2c;
    white-space: nowrap;
    opacity: 0.7;
  }

  /* ── Events (grid layout) ───────────── */
  .ptl-event {
    position: absolute;
    left: 0;
    right: 0;
    display: grid;
    grid-template-columns: 1fr 28px 1fr;
    align-items: start;
    opacity: 0;
    transition: opacity 0.7s ease;
  }
  .ptl-event.ptl-vis {
    opacity: 1;
  }

  /* Show correct side on desktop */
  .ptl-event:not(.ptl-right) .ptl-side-r .ptl-card { display: none; }
  .ptl-event.ptl-right .ptl-side-l .ptl-card { display: none; }

  .ptl-side-l { text-align: right; padding-right: 1.5rem; }
  .ptl-side-r { padding-left: 1.5rem; }

  /* ── Dot column ─────────────────────── */
  .ptl-dot-col {
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding-top: 2px;
    z-index: 2;
  }
  .ptl-dot {
    border-radius: 50%;
    border: 2.5px solid #f5f0e8;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    flex-shrink: 0;
  }
  .ptl-event:hover .ptl-dot {
    transform: scale(1.25);
  }
  .ptl-name .ptl-dot {
    box-shadow: 0 0 0 3px rgba(196, 90, 44, 0.2), 0 0 14px rgba(196, 90, 44, 0.35);
  }
  .ptl-disaster .ptl-dot {
    box-shadow: 0 0 0 2px rgba(158, 42, 42, 0.15);
  }

  /* ── Cards ──────────────────────────── */
  .ptl-card {
    background: white;
    padding: 0.85rem 1.1rem;
    border-radius: 4px;
    box-shadow: 0 2px 12px rgba(0, 0, 0, 0.05);
    transform: translateY(10px);
    transition: transform 0.7s ease, box-shadow 0.3s ease;
  }
  .ptl-vis .ptl-card {
    transform: translateY(0);
  }
  .ptl-event:hover .ptl-card {
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  }

  /* Name-event card accent */
  .ptl-event.ptl-right.ptl-name .ptl-side-r .ptl-card {
    border-left: 3px solid #c45a2c;
  }
  .ptl-event:not(.ptl-right).ptl-name .ptl-side-l .ptl-card {
    border-right: 3px solid #c45a2c;
  }

  .ptl-year {
    font-family: 'Inter', sans-serif;
    font-weight: 700;
    font-size: 0.78rem;
    letter-spacing: 0.04em;
    display: block;
  }
  .ptl-label {
    font-family: 'Lora', serif;
    font-size: 1rem;
    font-weight: 700;
    color: #1a1a1a;
    margin: 0.15rem 0 0.25rem;
    line-height: 1.3;
  }
  .ptl-text {
    font-family: 'Lora', serif;
    font-size: 0.82rem;
    line-height: 1.5;
    color: #5c5347;
    margin: 0;
  }

  /* ── Mobile ─────────────────────────── */
  @media (max-width: 768px) {
    .ptl-spine {
      left: 18px;
    }
    .ptl-event {
      grid-template-columns: 28px 1fr;
    }
    .ptl-side-l {
      display: none;
    }
    .ptl-side-r .ptl-card {
      display: block !important;
    }
    .ptl-side-r {
      padding-left: 1rem;
    }

    .ptl-event:not(.ptl-right).ptl-name .ptl-side-r .ptl-card {
      border-left: 3px solid #c45a2c;
      border-right: none;
    }

    .ptl-century {
      left: 4px;
      width: 28px;
    }
    .ptl-silence {
      left: 18px;
    }
    .ptl-name-bracket {
      left: 30px;
    }
    .ptl-scale {
      right: auto;
      left: 36px;
    }
    .ptl-dot-col {
      justify-content: center;
    }
  }
</style>
