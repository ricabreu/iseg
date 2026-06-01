# Modelling the Diffusion of Generative AI Research: A Bass Framework with Bibliometric Calibration and Monte Carlo Analysis
This repository reproduces every quantitative result and figure in the paper
from the Scopus bibliometric export.

Reproducible code for the paper:

> Ricardo Abreu. AMMS 2026.

This repository reproduces every quantitative result and figure in the paper
from the Scopus bibliometric export.

## What the code does

`bass_diffusion_genai.py` runs the full pipeline:

1. **Data** — parses the Scopus RIS export and builds the annual publication
   trajectory (the partial 2026 year is annualised by linear extrapolation).
2. **Calibration** — fits the Bass diffusion model (market potential `m`,
   innovation coefficient `p`, imitation coefficient `q`) by nonlinear least
   squares against the annual flow.
3. **Forecast & decomposition** — projects the diffusion trajectory and splits
   it into innovation `p(1-F)m` and imitation `qF(1-F)m` components.
4. **Monte Carlo** — propagates parameter uncertainty over 10,000 runs to
   produce forecast confidence bands.
5. **Parameter space** — sweeps the `(p, q)` grid to map the peak-adoption year
   and the fit-quality (SSE) surfaces.
6. **Figures** — renders the three figures used in the paper.

## The Bass model

The model specifies the adoption hazard among non-adopters as a linear function
of the cumulative adoption fraction `F(t)`:

```
f(t) / (1 - F(t)) = p + q F(t)
```

with closed-form cumulative solution

```
F(t) = (1 - e^{-(p+q)t}) / (1 + (q/p) e^{-(p+q)t})
```

The ratio `q/p` summarises the diffusion regime: values well above unity
indicate an imitation-dominated process.

## Corpus

The corpus is a Scopus export of **517 records** (415 journal articles + 102
conference papers), English language, published 2022–2026, across six subject
areas (Social Sciences, Business/Management/Accounting, Computer Science,
Psychology, Engineering, Decision Sciences). The exact query:

```
TITLE-ABS-KEY ( ( "generative AI" OR "generative artificial intelligence"
  OR "large language model*" OR LLM* OR "foundation model*" OR ChatGPT
  OR Gemini OR Claude )
AND ( "technology adoption" OR "organizational adoption" OR "firm adoption"
  OR "business adoption" OR "innovation diffusion" OR "technology diffusion" ) )
AND ( LIMIT-TO ( LANGUAGE , "English" ) )
AND ( LIMIT-TO ( SUBJAREA , "SOCI" ) OR LIMIT-TO ( SUBJAREA , "BUSI" )
  OR LIMIT-TO ( SUBJAREA , "COMP" ) OR LIMIT-TO ( SUBJAREA , "PSYC" )
  OR LIMIT-TO ( SUBJAREA , "ENGI" ) OR LIMIT-TO ( SUBJAREA , "DECI" ) )
AND ( LIMIT-TO ( DOCTYPE , "ar" ) OR LIMIT-TO ( DOCTYPE , "cp" ) )
```

## Results

| Parameter | Symbol | Estimate |
|---|---|---|
| Market potential | m | 1083.4 |
| Innovation coefficient | p | 0.0101 |
| Imitation coefficient | q | 1.4109 |
| Diffusion regime | q/p | 139.2 |
| Peak adoption year | t* | 2025.5 |
| Goodness of fit | R² | > 0.999 |

The estimates are robust to corpus definition: a narrower 409-record,
journal-only, four-area corpus yields `q/p ≈ 137` and the same 2025 peak.

## Usage

```bash
pip install -r requirements.txt

# Reproduce from the Scopus export:
python bass_diffusion_genai.py --ris Paper2_biblio_517.ris --outdir figures

# Or reproduce the calibration/figures from the published counts (no RIS needed):
python bass_diffusion_genai.py --outdir figures
```

Figures are written to the `figures/` directory.

## Files

```
bass_diffusion_genai.py   Full analysis pipeline (data → calibration → figures)
requirements.txt          Python dependencies
Paper2_biblio_517.ris     Scopus RIS export (the corpus)  [add your export here]
README.md                 This file
```

## Dependencies

- numpy
- scipy
- matplotlib

## License

MIT
