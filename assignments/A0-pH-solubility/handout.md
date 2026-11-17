# PharmSci 275 — Assignment A0: Is this pKa real?

*Agent-based analysis of experimental pH-solubility data*

**DRAFT 2026-11-17 — not yet reviewed by Luna.**

- **Released:** Mon Jan 11 · **Due:** Mon Jan 25, before class (9:00 am)
- **Graded for:** PharmSci 275 only
- **Expected effort:** several hours

## AI Usage

- Do NOT:
  - paste this handout into your coding agent and ask it to complete the assignment
  - have the coding agent do all of the work for you as if it were you
- Do:
  - Adhere to syllabus AI policy (explicitly note what tools you use and how; inspect what was done and decide if you agree with it; take ownership of all output and results)
  - use the AI/coding agent to help with the assigned tasks below
  - use the AI/coding agent to check your own work/thinking after you do it, after which you should write your own reflections about what you learned and any updates you made

## Why this assignment

An agent can rapidly fit a curve to almost anything, in seconds, and report a
confident number. It might be confidently wrong, but it will be confident. The skill comes in asking the right questions and deciding whether the number and analysis are likely to be correct. Real experimental data come with messy files, models that fit equally well, values near detection limits, and occasional results
that make no chemical sense.

In this assignment you will use a coding agent to analyze real pH-dependent
solubility measurements on drug-like compounds and estimate the compounds' pKa values.
You don't need to do any coding, as the agent should be able to handle it. However, **the judgment and thinking should be yours**, and that's mostly what we'll grade.

## Background

The solubility of an ionizable compound depends on pH because the ionized
form is far more soluble than the neutral form.
For a monoprotic acid,

  S = S₀ (1 + 10^(pH − pKa))

and for a monoprotic base,

  S = S₀ (1 + 10^(pKa − pH)),

where S₀ is the intrinsic solubility of the neutral form. Plotted as log S
against pH, the solubility of an acid is flat at low pH and rises with slope 1 above its pKa;
a base is the mirror image. Compounds with more than one ionizable group,
or with other limits on how much can dissolve, need other models. Here, the assignment involves deciding what model to use.

## The data

The data come from the SAMPL8 blind-prediction challenge. GSK measured them
and they are described in Bahr et al., *J. Comput.-Aided Mol. Des.* 35,
1141–1155 (2021), doi:10.1007/s10822-021-00427-0.

**File:** `Aqueous_Solubility for SAMPL8 Pub.xlsx`, exactly as the
experimentalists shared it. It contains 23 compounds; you will analyze the
ones listed below. Your coding agent can likely get the data out of this spreadsheet, but checking that it pulled the right compounds and columns is part of the assignment. The spreadsheet is in the course GitHub repository ([MobleyLab/drug-computing-v2](https://github.com/MobleyLab/drug-computing-v2)), in the `data/` folder next to this handout.

**How the measurements were made** (summarized from the paper's methods):
- Solid compound (nominally 1 mg) was added to 500 µL of Britton–Robinson
  buffer, 0.1 M ionic strength, at 11 nominal pH values from about 2 to 12.
- Samples were stirred for 24 h at 22 °C, filtered, and the dissolved
  concentration measured by HPLC-UV.
- The pH was measured again after equilibration.
- Concentrations below roughly 0.005 mg/mL are at or near the detection limit.

**Compounds to analyze:**

| ID | SMILES | pKa from an independent titration (Sirius T3) |
|---|---|---|
| SAMPL8-1 | `OC(=O)c1ccccc1Nc1cccc(c1)C(F)(F)F` | 3.99 ± 0.07 |
| SAMPL8-2 | `CS(=O)(=O)c1ccc(CCC(O)=O)cc1` | 4.50 ± 0.07 |
| SAMPL8-5 | `[Na+].[O-]C(=O)Cc1ccccc1Nc1c(Cl)cccc1Cl` | 4.23 ± 0.07 |
| SAMPL8-7 | `Nc1nc2ccc(Br)cc2n1CC1(O)CCOCC1` | not measured |
| SAMPL8-22 | `COc1cc2nc(Cl)nc(N)c2cc1OC` | 2.57 ± 0.09 |
| SAMPL8-23 | `Cc1nc2cc(O)ccc2s1` | not measured |

The titration values were measured later, by a different lab with a
different method, on four of the compounds.

## Tasks

### 1. Predict before you fit (your own work, no agent)
For each of the six compounds, **before asking the agent to fit anything**:
- identify or speculate about the ionizable group(s) and whether each is acidic or basic;
- estimate a rough pKa for each from your chemistry knowledge (or guess one);
- sketch or describe the log S vs. pH shape you expect.

It is OK if you're wrong here! What we want to see is not that you're right, but that you thought about it and came up with a shape that's consistent with your understanding.

Write these in your own words in your report. Your prompt log should show
that they came first. 

You can check them with the agent or the literature afterwards; being wrong here costs nothing, so long as you say what changed
your mind later. For example, you may wish to have an agent speculate about the ionizable group (AFTER doing the analysis above) and ask it why it made the choices it did, then include these in your report.

### 2. Load and plot
Use Claude to extract the data for your six compounds and plot log S against
pH for each. The spreadsheet has both a "Starting pH" and a "Final pH"
column. **State which you used and why**, in one or two sentences.

### 3. Fit and choose a model
For each compound, fit one or more pH-solubility models. Report:
- the model you chose, and why (both statistically and chemically);
- pKa value(s) with an uncertainty estimate, and how you obtained it;
- the plot with data and fitted curve.

If two models fit about equally well, say so and explain how you chose.

### 4. Compare with the titration values
For the four compounds with Sirius T3 values, compare your pKa with the
titration. For any difference larger than about 0.3 units, give your best
explanation, using what you know about the data and the experiment.

### 5. Two U-shaped curves
SAMPL8-1 and SAMPL8-23 both have solubility that rises at **both** low and
high pH, and both can be fit with a model that has two pKa values.
- For each, is a two-pKa interpretation chemically reasonable? Use the
  structures.
- Where it isn't, what else could explain the shape of the data?

### 6. Trust and data quality
(a) Of your six results, which would you **least** trust, and why?

(b) Look at SAMPL8-11 (`Cn1c2c(OC[C@H]3CC[C@H](N)CC3)c(F)cnc2ccc1=O`) in the
same spreadsheet. Is its curve a pH-solubility profile? Explain using the
structure and the methods above.

(c) Check the measured concentrations against the methods description. Is
anything inconsistent? If so, what could cause it, and does it affect your
pKa values? (Real datasets often leave questions like this unanswered; a
clear statement of the issue and its likely impact is a full answer.)

### 7. Reflection (your own words, no agent)
In half a page or less:
- Where did the agent help most, and where did you have to correct or
  redirect it? Give a specific example.
- What would you check first if you were handed a new pH-solubility dataset?

## What to submit

1. **Your Claude session share link(s)**, covering all your work. The full
   prompt log is part of the record we grade.
2. **Your notebook** (`.ipynb`), with outputs saved, containing the agent's
   code, plots and fits. The agent can write this.
3. **A short written report** (Markdown or PDF), in your own words,
   containing:
   - your Task 1 predictions;
   - this results table:

     | ID | Model | pKa(s) ± uncertainty | Titration pKa | Difference | Trusted? (Y/N) |
     |---|---|---|---|---|---|

   - your answers to Tasks 2–7, referring to the notebook's plots as
     needed.

Submit all three on Canvas.

## Rules for this assignment

- The course AI policy applies: your prompts and your reasoning must be your
  own. Tasks 1 and 7 are written without the agent. All agent usage must be disclosed.
- These data are public, and published analyses exist. You may consult the
  literature, **but cite anything you use**. Reproducing a published number
  without your own justification earns no credit; most of the grade is for
  your reasoning.
- Discuss approaches with classmates if you like, but don't share prompts,
  code or written answers.
