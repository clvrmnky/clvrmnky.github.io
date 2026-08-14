---
layout: post
title: "Vanta was never supposed to be a matter of taste"
date: 2026-08-14 08:00:00 -0700
categories: design-engineering
---

<style>
.vanta-viz {
  --v-ground: #fdfcfa;
  --v-ground-2: #f4f1ea;
  --v-ink: #17140f;
  --v-ink-dim: #57503f;
  --v-rule: rgba(23,20,15,0.14);
  --v-rule-strong: rgba(23,20,15,0.34);
  --v-accent: oklch(0.50 0.19 268.58);
  --v-card: #ffffff;
  width: 100vw; max-width: 100vw;
  margin: 2.6rem 0 2.6rem calc(50% - 50vw);
  padding: 2rem 1.5rem 2.2rem;
  background: var(--v-ground);
  color: var(--v-ink);
  border-top: 1px solid var(--v-rule);
  border-bottom: 1px solid var(--v-rule);
  box-sizing: border-box;
  font-family: -apple-system, "Segoe UI", ui-sans-serif, system-ui, sans-serif;
  line-height: 1.55;
}
@media (prefers-color-scheme: dark) {
  .vanta-viz {
    --v-ground: oklch(0.0705 0 0);
    --v-ground-2: oklch(0.11 0 0);
    --v-ink: oklch(0.94 0.005 90);
    --v-ink-dim: oklch(0.68 0.01 90);
    --v-rule: rgba(255,255,255,0.12);
    --v-rule-strong: rgba(255,255,255,0.30);
    --v-accent: oklch(0.72 0.14 268.58);
    --v-card: oklch(0.10 0 0);
  }
}
.vanta-inner { max-width: 1100px; margin: 0 auto; }
.vanta-viz .v-label {
  font-family: Georgia, "Iowan Old Style", Palatino, serif;
  font-variant: small-caps; letter-spacing: 0.08em;
  color: var(--v-ink-dim); font-size: 0.78rem; margin: 0 0 0.9rem;
}
.vanta-viz .v-mono { font-family: ui-monospace, "SF Mono", Menlo, Consolas, monospace; font-variant-numeric: tabular-nums; }
.vanta-viz .v-cap { font-size: 0.8rem; color: var(--v-ink-dim); margin: 0.9rem 0 0; max-width: 68ch; }

/* spectrum ruler */
.v-band {
  position: relative; height: 92px; border-radius: 3px;
  background: linear-gradient(to right,
    oklch(0.55 0.20 15) 0%, oklch(0.58 0.22 29.54) 17.5%, oklch(0.59 0.12 73.27) 29.4%,
    oklch(0.59 0.12 101.78) 35.2%, oklch(0.59 0.14 126.5) 44.5%, oklch(0.60 0.10 165) 55%,
    oklch(0.59 0.09 203.44) 66.8%, oklch(0.59 0.20 268.58) 83.1%,
    oklch(0.59 0.27 314.78) 94.75%, oklch(0.55 0.22 330) 100%);
  box-shadow: inset 0 0 0 1px var(--v-rule);
}
.v-tick { position: absolute; top: -6px; bottom: -6px; width: 1px; background: rgba(0,0,0,0.55); }
.v-tick-label { position: absolute; top: 100px; transform: translateX(-50%); text-align: center; width: 90px; }
.v-tick-letter { font-family: Georgia, serif; font-style: italic; font-size: 1rem; display: block; }
.v-tick-nm { font-size: 0.68rem; color: var(--v-ink-dim); display: block; margin-top: 1px; }
.v-ruler-cap { display: flex; justify-content: space-between; margin-top: 68px; font-size: 0.72rem; color: var(--v-ink-dim); letter-spacing: 0.04em; }

/* plates */
.v-plates { display: grid; grid-template-columns: repeat(auto-fill, minmax(210px, 1fr)); gap: 1.1rem; }
.v-plate { background: var(--v-card); border: 1px solid var(--v-rule); border-radius: 4px; overflow: hidden; display: flex; flex-direction: column; }
.v-swatch { height: 100px; }
.v-plate-body { flex: 1; padding: 0.9rem 1rem 1.1rem; display: flex; flex-direction: column; gap: 0.3rem; }
.v-plate-name { font-family: Georgia, serif; font-size: 1.12rem; }
.v-plate-src { font-size: 0.8rem; color: var(--v-ink-dim); margin-top: 0.15rem; }
.v-plate-data { display: flex; justify-content: space-between; font-size: 0.7rem; color: var(--v-ink-dim); margin-top: auto; padding-top: 0.5rem; border-top: 1px solid var(--v-rule); }

/* charts */
.v-charts { display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; }
@media (max-width: 700px) { .v-charts { grid-template-columns: 1fr; } }
.v-chart-svg { width: 100%; height: auto; overflow: visible; }
.vanta-viz .v-axis { stroke: var(--v-rule-strong); stroke-width: 1; }
.vanta-viz .v-axis-label { font-size: 8px; fill: var(--v-ink-dim); }
.vanta-viz .v-curve { fill: none; stroke: var(--v-accent); stroke-width: 1.6; }
.vanta-viz .v-pt { fill: var(--v-accent); }
.vanta-viz .v-pt.mid { fill: var(--v-ink); stroke: var(--v-accent); stroke-width: 1.5; }
.v-formula { display: block; margin-top: 0.5rem; padding: 0.55rem 0.7rem; background: var(--v-ground-2); border-radius: 3px; font-size: 0.78rem; overflow-x: auto; }

/* ramps */
.v-ramps { display: flex; flex-direction: column; gap: 0.85rem; }
.v-ramp-row { display: grid; grid-template-columns: 84px 1fr; align-items: center; gap: 0.9rem; }
.v-ramp-name { font-family: Georgia, serif; font-size: 0.86rem; }
.v-ramp-strip { display: grid; grid-template-columns: repeat(11, 1fr); height: 38px; border-radius: 3px; overflow: hidden; }
.v-ramp-step.mid { box-shadow: inset 0 0 0 2px var(--v-accent); }

/* diptych */
.v-diptych { display: grid; grid-template-columns: 1fr 1fr; gap: 1.1rem; }
@media (max-width: 640px) { .v-diptych { grid-template-columns: 1fr; } }
.v-prim { border: 1px solid var(--v-rule); border-radius: 4px; overflow: hidden; background: var(--v-card); }
.v-prim .v-swatch { height: 140px; border-bottom: 1px solid var(--v-rule); }
.v-refl-bar { height: 6px; border-radius: 3px; background: var(--v-rule); overflow: hidden; margin-top: 0.5rem; }
.v-refl-fill { height: 100%; background: var(--v-accent); }
</style>

The diagrams in [the last post]({{ site.baseurl }}/the-harness-outlives-the-model.html) carry a footnote about something called Vanta. A few people asked what that actually is.

This is the answer.

The question I started with was narrow. Can you build a working design colour system out of scientific data?

Not colour *inspired by* science. Every studio has a deck with a nebula in it. I mean the actual constraint. Pick a physical source, derive the values from measurements of that source, refuse to choose by eye.

Seven colours. None of them chosen.

<div class="vanta-viz"><div class="vanta-inner">
<p class="v-label">The palette, positioned by wavelength</p>
<div class="v-band" id="v-band"></div>
<div class="v-ruler-cap"><span>700nm</span><span>visible spectrum</span><span>380nm</span></div>
<p class="v-cap">Each tick is a real emission line. The position is the wavelength, not a design decision.</p>
</div></div>

## Colour was information before it was decoration

The thing that makes this work is not new. It is about four hundred years old.

**It starts in 1666.** Isaac Newton, twenty-three, Cambridge shut down by plague, sitting alone at Woolsthorpe Manor with a prism. For nearly two thousand years the accepted theory, inherited from Aristotle and refined by Descartes, held that colour was something glass or water *added* to light.

Newton proved the opposite. White light already contains every colour. The prism only separates what was there the whole time.

First real proof that colour is physics.

**It becomes a science of identification in 1814.** Joseph von Fraunhofer mapped the sun's spectrum with more precision than anyone before him, and found the continuous rainbow Newton described was not continuous at all. It was interrupted by hundreds of thin dark lines, each at an exact, repeatable wavelength.

He catalogued the brightest with letters. C, D, F, G′, h. He never fully explained why they were there.

That explanation came about forty-five years later, from Kirchhoff and Bunsen. Each line is a fingerprint of a specific element, absorbing or emitting at wavelengths unique to its own atomic structure.

Spectroscopy was born in the gap between Fraunhofer's catalogue and its explanation. Reading *what something is made of* from the exact colour it emits.

That is the idea the whole palette is built on.

Every hue in Vanta does not start as a design decision. It starts as a measured wavelength attached to a real event in the history of that same science.

## The seven lines

<div class="vanta-viz"><div class="vanta-inner">
<p class="v-label">Mid-ramp OKLCH, as implemented in panda.config.ts</p>
<div class="v-plates">
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0.22 29.54);"></div><div class="v-plate-body"><div class="v-plate-name">Cadmium</div><div class="v-plate-src">Michelson measured the Paris meter bar against this line in 1892. The first serious attempt to define length by light instead of metal. It won him the 1907 Nobel Prize.</div><div class="v-plate-data v-mono"><span>643.85nm</span><span>H 29.54</span></div></div></div>
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0.12 73.27);"></div><div class="v-plate-body"><div class="v-plate-name">Krypton</div><div class="v-plate-src">Krypton-86's transition was the legally defined SI metre from 1960 to 1983. A wavelength standing in for a bar of metal.</div><div class="v-plate-data v-mono"><span>605.78nm</span><span>H 73.27</span></div></div></div>
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0.12 101.78);"></div><div class="v-plate-body"><div class="v-plate-name">Helium</div><div class="v-plate-src">Janssen saw this unexplained line in the sun's chromosphere during the 1868 eclipse. The only element ever discovered in space before it was found on Earth.</div><div class="v-plate-data v-mono"><span>587.49nm</span><span>H 101.78</span></div></div></div>
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0.14 126.5);"></div><div class="v-plate-body"><div class="v-plate-name">Oxygen</div><div class="v-plate-src">The aurora's forbidden transition green. Ångström saw it in 1868. It took until the 1920s and two Canadian physicists to explain why it was there.</div><div class="v-plate-data v-mono"><span>557.70nm</span><span>H 126.5</span></div></div></div>
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0.09 203.44);"></div><div class="v-plate-body"><div class="v-plate-name">Hbeta</div><div class="v-plate-src">Hydrogen's Balmer series, n=4 to 2. An electron falling two energy levels. Fraunhofer's own line F, catalogued in 1814.</div><div class="v-plate-data v-mono"><span>486.10nm</span><span>H 203.44</span></div></div></div>
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0.20 268.58);"></div><div class="v-plate-body"><div class="v-plate-name">Hgamma</div><div class="v-plate-src">Balmer n=5 to 2, Fraunhofer's line G′. Closest of the Balmer lines to the eye's peak photopic sensitivity, which is why it carries the brand.</div><div class="v-plate-data v-mono"><span>434.10nm</span><span>H 268.58</span></div></div></div>
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0.27 314.78);"></div><div class="v-plate-body"><div class="v-plate-name">Calcium</div><div class="v-plate-src">The calcium H line. One of the pair Edwin Hubble tracked the redshift of across forty spiral nebulae in 1929, discovering the universe is expanding.</div><div class="v-plate-data v-mono"><span>396.80nm</span><span>H 314.78</span></div></div></div>
  <div class="v-plate"><div class="v-swatch" style="background: oklch(0.59 0 0);"></div><div class="v-plate-body"><div class="v-plate-name">Achromatic</div><div class="v-plate-src">Zero chroma. The reference grey every ramp's contrast is checked against. No wavelength attached.</div><div class="v-plate-data v-mono"><span>n/a</span><span>C 0</span></div></div></div>
</div>
</div></div>

The names are the derivation. Look up what emits at that wavelength and you have both the colour and its history in one word.

That solves a problem I did not set out to solve. "Blue 600" tells you nothing. "Hgamma" tells you it is hydrogen, Balmer series, and you can go check.

Four of the eight trace to hydrogen alone. Five real physical sources total, not eight independent picks.

## The Fraunhofer thread, found not planned

Here is the part I did not engineer.

Only *after* the Balmer colours were already adopted did cross-checking against Fraunhofer's 1814 letter catalogue show that several of them are literally his own lines. C. F. G′.

The palette had reconstructed a piece of nineteenth-century spectroscopy without intending to.

The thread is thinner now than it was. Yellow used to carry Fraunhofer's D line through sodium, and got traded for helium's discovery story. Brand secondary used to be Hδ and carried the h line, and got traded for calcium and Hubble.

Both were real trade-offs, decided directly rather than defaulted into. This palette values the single strongest story over thread completeness when the two conflict. Consistently, not just once.

## The method

Wavelength to hue is a pipeline, not a lookup.

<div class="vanta-viz"><div class="vanta-inner">
<p class="v-label">Derivation</p>
<span class="v-formula v-mono">wavelength (nm) → sRGB (Bruton's method) → OKLCH, extract H only</span>
<p class="v-cap">Only the hue carries over. The raw conversion produces maximum-saturation spectral colour, far too neon for interface use. Lightness and chroma get built separately.</p>
</div></div>

OKLCH is the source of truth throughout, not hex. Browsers consume L, C and H directly. There is no reason for hex to be the primary record.

Primary and secondary were not a preference call either. Candidates got ranked by distance from 555nm, the peak of the CIE photopic luminosity function. The wavelength the human eye is physiologically most sensitive to.

Hgamma won. Not because I liked it.

## Wavelength picks hue. The system picks light.

This is the part that turned an experiment into a system.

Derivation gives you hue. It gives you nothing else. Lightness and chroma fall out of the raw conversion as whatever happens to fall out, and what falls out is unusable.

So they get built, per a stated formula each.

Lightness runs 0.26 to 0.93. Narrowed from an initial 0.15 to 0.97 because black and white are separate primitives and must never be aliased to a ramp's extremes. Step spacing is a symmetric warp rather than linear, concentrating resolution in the vivid middle instead of wasting it near the ends where a hue physically cannot read as itself.

Chroma sits at 92% of whatever is achievable in gamut at each lightness. Pushed up from 78%, grounded in the Helmholtz-Kohlrausch effect. Saturated colours read as more luminous than lightness alone predicts. Real vision science, still used in aviation signal-light standards.

<div class="vanta-viz"><div class="vanta-inner">
<div class="v-charts">
  <div>
    <p class="v-label">Lightness, symmetric warp, k = 2.2</p>
    <svg viewBox="0 0 340 200" class="v-chart-svg" role="img" aria-label="Lightness warp curve across 11 ramp steps">
      <line x1="40" y1="10" x2="40" y2="160" class="v-axis" /><line x1="40" y1="160" x2="320" y2="160" class="v-axis" />
      <polyline id="v-lcurve" class="v-curve" /><g id="v-lpoints"></g>
    </svg>
    <p class="v-cap">Steps bunch near the middle and spread near the ends. Resolution goes where hue is actually legible.</p>
  </div>
  <div>
    <p class="v-label">Chroma at fixed L = 0.595, by hue</p>
    <svg viewBox="0 0 340 200" class="v-chart-svg" role="img" aria-label="Chroma achieved per hue at the same lightness">
      <line x1="40" y1="10" x2="40" y2="160" class="v-axis" /><line x1="40" y1="160" x2="320" y2="160" class="v-axis" />
      <g id="v-cbars"></g>
    </svg>
    <p class="v-cap">Same lightness, same 92% rule. Chroma still ranges widely, because violet has far more gamut headroom at this lightness than orange does.</p>
  </div>
</div>
<p class="v-label" style="margin-top:2rem;">The full 11-step ramp, per colour</p>
<div id="v-ramps" class="v-ramps"></div>
<p class="v-cap">Computed live from the two formulas, not transcribed. Step 6, outlined, is the mid-ramp value in the table above.</p>
</div></div>

Look at the lightness column across all seven. It is identical.

That is not a coincidence to investigate. It is the rule. Every ramp shares one warp schedule, so step 6 of Cadmium and step 6 of Calcium carry exactly the same perceptual weight.

Only hue changes. Swap one for another in a diagram and the composition does not shift. Only the meaning does.

That property is the entire reason the palette works for information design.

And none of it came from the physics.

The physics contributed hue angle. That is all it contributed.

Which is the real finding, and it is not the romantic one. Provenance is good at the question taste is worst at: why this blue and not that one. It is useless at the question that actually determines whether a system works, which is how much light each colour throws.

You need both. The data settles the argument nobody can settle by eye. Perception settles everything else.

## Black and white, sourced the same way

Not `#000` and `#fff`.

Both extremes get a real measured material, using the same rigour as every hue. Reflectance instead of emission.

<div class="vanta-viz"><div class="vanta-inner">
<div class="v-diptych">
  <div class="v-prim"><div class="v-swatch" style="background: oklch(0.0705 0 0);"></div><div class="v-plate-body"><div class="v-plate-name">Vantablack</div><div class="v-plate-src v-mono" style="font-size:0.72rem;">colors.vanta</div><div class="v-plate-src">Surrey NanoSystems' carbon nanotube forest. Light entering the gaps between vertically aligned tubes rarely escapes. 99.965% absorption.</div><div class="v-refl-bar"><div class="v-refl-fill" style="width:0.035%;"></div></div><div class="v-plate-data v-mono"><span>Y = 0.00035</span><span>L 0.0705</span></div></div></div>
  <div class="v-prim"><div class="v-swatch" style="background: oklch(0.9936 0 0);"></div><div class="v-plate-body"><div class="v-plate-name">Purdue White</div><div class="v-plate-src v-mono" style="font-size:0.72rem;">colors.white</div><div class="v-plate-src">Xiulin Ruan's barium sulfate paint at 60% pigment concentration, engineered to reflect sunlight and passively cool a roof. Guinness record holder.</div><div class="v-refl-bar"><div class="v-refl-fill" style="width:98.1%;"></div></div><div class="v-plate-data v-mono"><span>Y = 0.981</span><span>L 0.9936</span></div></div></div>
</div>
</div></div>

The conversion is the fussy part, and worth being precise about.

Reflectance is a linear-light quantity. OKLCH lightness is perceptually compressed. You cannot treat a percentage as an L value directly.

For an achromatic colour, OKLab's L reduces to the sum of its three L-row coefficients times the cube root of reflectance. That coefficient sum computes to roughly 0.99999999 for the published matrix. Close enough to exactly 1 that L is, in practice, just the cube root.

Vantablack at 0.035% reflectance lands at L 0.0705. Purdue white at 98.1% lands at L 0.9936.

Both sit outside the ramp's own range on purpose. The ramp never fades to true black or true white, so all eleven steps keep working contrast, while the extremes carry the real measured values.

Fitting, for a project named vanta.

## What got thrown away

Two other methods were tried first. Both are documented rather than deleted, because the reasoning trail is the point.

**Golden angle rotation. Rejected.** A φ-derived seed rotated by 360°/φ² provably never clusters, no matter how many hues you generate. The same mechanism behind sunflower seed spirals. Real maths with a genuine guarantee.

Checked against actual colour targets it landed three of four culturally recognised hues at best. Never four of four. It reliably missed true amber.

Not a failure of the maths. A mismatch between well-distributed and recognisable.

**An Enochian algorithm for the hue rotation. Explicitly rejected.** It would have replaced a mechanism with a provable property with a number chosen for symbolic meaning alone.

That is decoration wearing rigour's clothes. Using mysticism to generate actual pixel values and then presenting it as derivation.

I have a knowledge-base rule that exists specifically to prevent that, and it caught this.

**Cherenkov radiation. Computed, then rejected.** The blue glow in a reactor pool. Great story, 1958 Nobel, and the sources describe it in almost exactly the language this project reaches for on its own.

But its emission is a continuous spectrum, not a discrete line. I computed a photon-weighted centroid across the visible range anyway rather than just flagging the gap. It resolves to 523.7nm.

Green. Not blue at all.

And the value swings wildly depending on where you draw the ultraviolet cutoff. Cherenkov blue is a perceptual effect, human cone sensitivity weighted against a UV-heavy spectrum. There is no principled way to reduce it to one hue.

So it is rejected, not left open. The check was run.

## What I actually got

Can you build a design colour system on scientific data?

Yes. Roughly half of one.

The data gave me seven hue angles I can defend without appealing to taste, a naming scheme that carries its own derivation, and a reasoning trail a future contributor can reconstruct rather than trust.

It did not give me lightness. Or chroma. Or step spacing, gamut behaviour, or contrast.

Those came from ordinary perceptual work, and they are the parts carrying the load.

What I would say to anyone trying this: adopt the constraint *because* it is arbitrary. An external rule you cannot negotiate with kills the circular conversation about whether the blue is right. And it costs less than you would think.

Just do not confuse having a citation with having a system.

The citations are why the palette is interesting.

The formulas are why it works.

<div class="vanta-viz"><div class="vanta-inner">
<p class="v-label">Sources</p>
<p class="v-cap"><a href="https://en.wikipedia.org/wiki/Balmer_series">Balmer series</a> · <a href="https://en.wikipedia.org/wiki/Fraunhofer_lines">Fraunhofer lines</a> · <a href="https://www.nist.gov/document/museum-timelinepdf">Krypton-86 metre standard, NIST</a> · <a href="https://academic.oup.com/astrogeo/article/50/5/5.25/194606">Aurora green line history</a> · <a href="https://www.smithsonianmag.com/history/how-scientists-discovered-helium-first-alien-element-1868-180970057/">Discovery of helium</a> · <a href="https://science.nasa.gov/mission/hubble/science/science-behind-the-discoveries/hubble-cosmological-redshift/">Hubble cosmological redshift</a> · <a href="https://en.wikipedia.org/wiki/Vantablack">Vantablack</a> · <a href="https://www.purdue.edu/newsroom/archive/releases/2021/Q2/the-whitest-paint-is-here-and-its-the-coolest.-literally..html">Purdue ultra-white paint</a> · <a href="https://en.wikipedia.org/wiki/Helmholtz%E2%80%93Kohlrausch_effect">Helmholtz-Kohlrausch effect</a> · <a href="https://en.wikipedia.org/wiki/Cherenkov_radiation">Cherenkov radiation</a></p>
</div></div>

<script>
(function () {
  var band = document.getElementById('v-band');
  var stops = [
    { nm: 643.85, l: 'Cd' }, { nm: 605.78, l: 'Kr' }, { nm: 587.49, l: 'He' },
    { nm: 557.70, l: 'O' }, { nm: 486.10, l: 'Hβ' }, { nm: 434.10, l: 'Hγ' },
    { nm: 396.80, l: 'Ca' }
  ];
  var lo = 380, hi = 700;
  stops.forEach(function (s) {
    var pct = ((hi - s.nm) / (hi - lo)) * 100;
    var t = document.createElement('div');
    t.className = 'v-tick'; t.style.left = pct + '%'; band.appendChild(t);
    var lab = document.createElement('div');
    lab.className = 'v-tick-label'; lab.style.left = pct + '%';
    lab.innerHTML = '<span class="v-tick-letter">' + s.l + '</span><span class="v-tick-nm v-mono">' + s.nm + 'nm</span>';
    band.appendChild(lab);
  });

  function oklchToLinearSrgb(L, C, Hdeg) {
    var h = Hdeg * Math.PI / 180;
    var a = C * Math.cos(h), b = C * Math.sin(h);
    var l_ = L + 0.3963377774 * a + 0.2158037573 * b;
    var m_ = L - 0.1055613458 * a - 0.0638541728 * b;
    var s_ = L - 0.0894841775 * a - 1.2914855480 * b;
    var l = l_ * l_ * l_, m = m_ * m_ * m_, s = s_ * s_ * s_;
    return [4.0767416621 * l - 3.3077115913 * m + 0.2309699292 * s,
            -1.2684380046 * l + 2.6097574011 * m - 0.3413193965 * s,
            -0.0041960863 * l - 0.7034186147 * m + 1.7076147010 * s];
  }
  function inGamut(rgb) { return rgb.every(function (v) { return v >= -1e-4 && v <= 1 + 1e-4; }); }
  function maxChroma(L, H) {
    var lo = 0, hi = 0.5;
    for (var i = 0; i < 24; i++) {
      var mid = (lo + hi) / 2;
      if (inGamut(oklchToLinearSrgb(L, mid, H))) lo = mid; else hi = mid;
    }
    return lo;
  }

  var LMIN = 0.26, LMAX = 0.93, K = 2.2, CF = 0.92;
  function stepL(i) {
    var p = i / 10, t = 2 * p - 1;
    var warp = Math.sign(t) * Math.pow(Math.abs(t), K);
    return LMIN + (warp + 1) / 2 * (LMAX - LMIN);
  }
  var lSteps = []; for (var i = 0; i < 11; i++) lSteps.push(stepL(i));

  var x0 = 40, x1 = 320, y0 = 10, y1 = 160;
  var xFor = function (i) { return x0 + (i / 10) * (x1 - x0); };
  var yForL = function (L) { return y1 - ((L - LMIN) / (LMAX - LMIN)) * (y1 - y0); };
  document.getElementById('v-lcurve').setAttribute('points',
    lSteps.map(function (L, i) { return xFor(i) + ',' + yForL(L); }).join(' '));
  var lg = document.getElementById('v-lpoints');
  var NS = 'http://www.w3.org/2000/svg';
  lSteps.forEach(function (L, i) {
    var c = document.createElementNS(NS, 'circle');
    c.setAttribute('cx', xFor(i)); c.setAttribute('cy', yForL(L));
    c.setAttribute('r', i === 5 ? 4 : 2.6);
    c.setAttribute('class', 'v-pt' + (i === 5 ? ' mid' : ''));
    lg.appendChild(c);
    if (i === 0 || i === 5 || i === 10) {
      var t = document.createElementNS(NS, 'text');
      t.setAttribute('x', xFor(i)); t.setAttribute('y', yForL(L) + (i === 10 ? -8 : 16));
      t.setAttribute('text-anchor', 'middle'); t.setAttribute('class', 'v-axis-label v-mono');
      t.textContent = L.toFixed(3); lg.appendChild(t);
    }
  });

  var palette = [
    { name: 'Cadmium', short: 'Cd', hue: 29.54 }, { name: 'Krypton', short: 'Kr', hue: 73.27 },
    { name: 'Helium', short: 'He', hue: 101.78 }, { name: 'Oxygen', short: 'O', hue: 126.5 },
    { name: 'Hbeta', short: 'Hβ', hue: 203.44 }, { name: 'Hgamma', short: 'Hγ', hue: 268.58 },
    { name: 'Calcium', short: 'Ca', hue: 314.78 }
  ];

  var cg = document.getElementById('v-cbars');
  var midL = lSteps[5];
  var chromaAtMid = palette.map(function (p) { return CF * maxChroma(midL, p.hue); });
  var cMax = Math.max.apply(null, chromaAtMid);
  var bw = (x1 - x0) / palette.length;
  palette.forEach(function (p, i) {
    var cVal = chromaAtMid[i];
    var bx = x0 + i * bw + bw * 0.18;
    var bh = (cVal / cMax) * (y1 - y0);
    var by = y1 - bh;
    var r = document.createElementNS(NS, 'rect');
    r.setAttribute('x', bx); r.setAttribute('y', by);
    r.setAttribute('width', bw * 0.64); r.setAttribute('height', Math.max(bh, 0.5));
    r.setAttribute('fill', 'oklch(' + midL + ' ' + cVal + ' ' + p.hue + ')');
    cg.appendChild(r);
    var lab = document.createElementNS(NS, 'text');
    lab.setAttribute('x', bx + bw * 0.32); lab.setAttribute('y', y1 + 14);
    lab.setAttribute('text-anchor', 'middle'); lab.setAttribute('class', 'v-axis-label v-mono');
    lab.textContent = p.short; cg.appendChild(lab);
    var val = document.createElementNS(NS, 'text');
    val.setAttribute('x', bx + bw * 0.32); val.setAttribute('y', by - 5);
    val.setAttribute('text-anchor', 'middle'); val.setAttribute('class', 'v-axis-label v-mono');
    val.textContent = cVal.toFixed(2); cg.appendChild(val);
  });

  var ramps = document.getElementById('v-ramps');
  palette.forEach(function (p) {
    var row = document.createElement('div'); row.className = 'v-ramp-row';
    var n = document.createElement('div'); n.className = 'v-ramp-name'; n.textContent = p.name;
    row.appendChild(n);
    var strip = document.createElement('div'); strip.className = 'v-ramp-strip';
    lSteps.forEach(function (L, i) {
      var c = CF * maxChroma(L, p.hue);
      var st = document.createElement('div');
      st.className = 'v-ramp-step' + (i === 5 ? ' mid' : '');
      st.style.background = 'oklch(' + L + ' ' + c + ' ' + p.hue + ')';
      st.title = 'step ' + (i + 1) + ' · L ' + L.toFixed(3) + ', C ' + c.toFixed(3) + ', H ' + p.hue;
      strip.appendChild(st);
    });
    row.appendChild(strip); ramps.appendChild(row);
  });
})();
</script>

---

*Vanta is set in Atkinson Hyperlegible. Seven hues, none of them chosen.*
