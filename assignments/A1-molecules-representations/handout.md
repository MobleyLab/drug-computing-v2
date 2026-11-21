# PharmSci 175/275 — Assignment A1: What is this molecule, really?

**DRAFT — needs review and testing by Luna.**

- **Released:** Wed Jan 20 · **Due:** Mon Feb 1, 9:00 am (175) · Mon Feb 8,
  9:00 am (275)
- **175:** Tasks 1–4, plus the Reflection.
- **275:** Tasks 1–5, plus the Reflection.
- Task 5 is available to 175 students as **optional extra credit**.

## AI Usage

- Do NOT:
  - paste this handout into your coding agent and ask it to complete the assignment
  - have the coding agent do all of the work for you as if it were you
- Do:
  - Adhere to syllabus AI policy (explicitly note what tools you use and how; inspect what was done and decide if you agree with it; take ownership of all output and results)
  - use the AI/coding agent to help with the assigned tasks below
  - use the AI/coding agent to check your own work/thinking after you do it, after which you should write your own reflections about what you learned and any updates you made

## Why this assignment

Everything you will do for the rest of this course starts with a molecule
represented in some way, including as text or in a file. This includes representing them as a SMILES string, an SDF, or a PDB file. Those
representations look interchangeable, but they are not necessarily so.

An agent will happily compute a molecular weight, a logP, a filter verdict or
a similarity ranking from whatever representation you hand it. It will not tell you that if the representation you hand it is not the one you might have intended.  Most of the ways such errors occur are silent, with no error or warning, just incorrect values, or values that really belong to a different compound.

In this assignment you will work with some common representations of molecules and see how to use them and how they affect results. This will include tests in which you break molecules on purpose, watch what the breakage does to your calculations, and then run a small library search where
the difference shows up as a difference in the science. **You don't need to
write the code yourself; the agent can do that. The judgment is yours, and that is what we grade.**

## The data

All files are in the `data/` folder of the assignment repository.

| File | What it is |
|---|---|
| `search_pool.csv` | 534 compounds: 54 confirmed HIV-1 integrase binders and 480 fragments that were tested and did not bind. Columns: `name`, `smiles`, `is_active`. |
| `library_chembl_sample.csv` | 2,000 bioactive compounds sampled from ChEMBL. Columns: `chembl_id`, `pref_name`, `smiles`. |
| `approved_drugs_chembl.csv` | 3,311 approved small-molecule drugs from ChEMBL. Same columns. |
| `3NF8_ligand.pdb` | The ligand from PDB entry 3NF8, a crystal structure of HIV-1 integrase with an inhibitor bound. |

A note on what these are: the ChEMBL sample is a slice of *compounds that
appear in the medicinal-chemistry literature*. It is not a vendor screening
catalogue, and it has not been cleaned up for you. The approved-drug set is
every small molecule ChEMBL lists as having reached approval, recorded in
the form in which it was registered.

The binder/non-binder data come from the SAMPL4 blind prediction challenge.
See the repository `README.md` for full provenance and licensing.

---

## Task 1 — Four ways to break a molecule

For each of the four manipulations below: **write down what you expect to
happen before you compute anything.** Then test it, and record what actually
happened.

For each case, report the molecular formula and molecular weight before and
after, whether the change raised an error or passed silently, and — this is
the part that matters — **how you would catch this in a pipeline where you
had not been told to look for it.** You may wish to have the agent draw a 2D structure of each molecule to help you think through what's going on.

**(a) Salt form.** Metformin hydrochloride is written
`Cl.CN(C)C(=N)N=C(N)N`. Compute its properties. Now compute the properties of
metformin itself. Which one belongs in a property calculation, and why? (Hint: What does the period mean? You can iterate with the agent on how to understand SMILES strings for help with this task.)

**(b) Case.** Take phenol, `c1ccccc1O`. Convert the whole string to
UPPERCASE and parse it again. Then convert the original to lowercase and
parse that. Report what each one gives you. One of these two failures is far
more dangerous than the other — say which and why. This example may seem silly, but I've seen a company attempting to make and sell a commercial machine learning model make this exact error due to lack of "domain expertise".

**(c) The same mistake, a different molecule.** Caffeine can be written
`Cn1cnc2c1c(=O)n(C)c(=O)n2C` or in Kekulé form as
`CN1C=NC2=C1C(=O)N(C)C(=O)N2C`. Apply the uppercase manipulation from (b) to
each. Explain why the two forms behave differently.

**(d) A stereocentre that isn't.** Open `search_pool.csv` and look at the
compounds whose names begin with `AVX`. A number of them carry a stereo
descriptor on a nitrogen atom, written like `[N@@H+]`. Find how many. Then
answer: is a protonated tertiary amine a stable stereocentre in aqueous
solution at room temperature? What should a modelling pipeline do with these
tags, and what would go wrong if it took them at face value?

Finally: **report at least one thing in this task that you did not notice
until a number looked wrong**, or explain how you caught all four up front.

## Task 2 — From SMILES to 3D, and what information different file formats throw away

**(a)** Take any compound from `search_pool.csv`. Generate a 3D conformation
from its SMILES and display it interactively with `py3Dmol`. Include the view
in your notebook.

**(b)** Now test how well 3D structures survive a round trip through two file
formats. For **all 54 active compounds** in `search_pool.csv`:

1. Build each molecule from SMILES, add explicit hydrogens, and generate 3D
   coordinates.
2. Write each to **SDF**, read it back, and check whether the molecule you
   read back is the same molecule you wrote.
3. Do the same through **PDB**.
4. Repeat the whole thing with **at least three different random seeds** for
   the 3D coordinate generation.

Report a table: how many of the 54 survived each format, at each seed.

**(c)** Explain your results. Your answer should address:
- HOW did you check that this is the same molecule? Why is that a good check?
- Why do the two formats behave differently? What information does each one
  store about the bonds in a molecule?
- Why does the random seed change the answer at all? What does that tell you
  about a pipeline that was tested once and appeared to work?
- Pick one failure and read the error message. Does it point at the atom that
  is actually causing the problem? What would you have concluded if you were
  debugging this without knowing the cause?

**(d) Short answer.** Generating 3D coordinates means choosing *one* shape
for a molecule that can adopt many. What did the code choose, and how would
you know whether that choice mattered for what you were about to do with it?
(We return to this question later in the course.)

## Task 3 — Filters, and what they cost you

**(a)** Apply a property filter to `library_chembl_sample.csv`. At minimum
use Lipinski's Rule of Five and the PAINS structural alerts. Report how many
compounds pass, how many fail, and on which criterion.

**Justify every cutoff you use.** A filter you cannot defend is a filter you
should not be running. If you use the standard Ro5 thresholds, say why those
numbers and not others.

**(b)** Run **one substructure search** over the same library using a SMARTS
pattern of your choosing. Say what you were looking for and what you found.

**(c)** Visualize a sample of what survived your filter, and a sample of what
it removed, using `mols2grid` or an equivalent.

**(d) The approved-drug check.** Now run the *same* filter over
`approved_drugs_chembl.csv`. These are drugs that reached approval — they
work, they were taken by patients, and your filter was supposed to tell you
what good compounds look like.

- How many approved drugs does your filter reject?
- Look at the ones it rejects. Do you see any pattern in what they are?
- Does this change your filter? If you decide to keep it unchanged, defend
  that decision.

**(e) Look at how those molecules are written.** Count how many entries in
`approved_drugs_chembl.csv` contain a `.` in their SMILES, and recall from
Task 1(a) what that means. Strip these down to the largest component, re-run
your filter, and report how many verdicts changed.

Then do the same for `library_chembl_sample.csv`. The two files behave very
differently here. Explain why — what is it about how approved drugs get
registered that the ChEMBL literature sample does not share?

**(f) The cost of filtering.** Apply your filter to the 54 compounds in
`search_pool.csv` that are marked `is_active` — compounds that are *known* to
bind HIV-1 integrase.

How many, **if any**, would your filter have thrown away? Then test whether
that answer is stable: if you tighten your rule (for example, from "two or
more violations" to "any violation at all"), does the number change? What if you
loosen it?

In Task 4 you are going to search for exactly these compounds. Given what you
just found, what would have happened if you had filtered the library first —
and how much of your answer to that question was decided back in (a), when you
chose your thresholds?

## Task 4 — A simple similarity search

The file `search_pool.csv` is a small screening library: 54 compounds known
to bind HIV-1 integrase, hidden among 480 that were tested against the same
target and did not. You are going to try to find them.

Your query is the inhibitor from the crystal structure 3NF8:

```
OC(=O)c1c(CN2C(=O)Cc3cc(Cl)ccc23)ccc4OCCOc14
```

**(a)** Rank all 534 compounds in the pool by **Tanimoto similarity** to the
query, using **Morgan fingerprints with radius 2 and 2048 bits**. Use these
settings as given; do not tune them.

Report **how many of the 54 actives appear in your top 30**. Compare that
with what you would expect from picking 30 compounds at random (work this
number out — it is a very simple calculation you can do on paper, not a simulation).

**(b)** Now build the query a second way. The file `3NF8_ligand.pdb` contains
the same ligand, as it appears in the crystal structure. Read the molecule
directly from that PDB file and use *it* as your query, with the identical
search code.

Report the top-30 count again.

Then compare the two query molecules directly: molecular weight, number of
aromatic atoms, and the SMILES each one produces. **Explain what information was missing from the PDB file, what the software did about it, and how that explains the difference in your search results.**

This is the same issue you measured in Task 2. Here it impacts the quality of your results.

**(c) One paragraph.** Your query came from a crystal structure of this exact
target, and the 54 actives are chemically related to it. Suppose instead you
were starting a project against a target with no known ligands. Would you
expect this method to work as well? Say what you would expect and why. *(One
paragraph — we analyze this in more detail in A2.)*

## Task 5 — Protonation state *(275 required; 175 optional extra credit)*

Every structure you have handled so far came with its protonation state
already decided by whoever created the file. In Task 1(d) you saw charged
nitrogens in the SAMPL4 data. In Task 3 you filtered molecules whose HBD,
HBA and logP values all depend on which atoms carry a proton.

**(a)** Choose at least five compounds that failed your Ro5 filter in Task 3.
For each, determine the likely dominant protonation state at
**pH 7.4** (via some tool or your own chemistry insight, subject of course to caveats that the predictions may not be accurate), and recompute MW, HBD, HBA and logP for that state.

**(b)** How many of your five change their Ro5 verdict? A compound that
"fails Lipinski" as drawn may pass as it exists in solution, or the reverse.

**(c)** A good place to start is atorvastatin, which appears in
`approved_drugs_chembl.csv` in more than one form. Compare the entry as
registered, the largest component of that entry, and the form you would
expect at pH 7.4. You should get three different answers to the question
"does this molecule obey the Rule of Five?"

**(d) Be honest about the tools.** Free pKa prediction (or more broadly, pKa prediction in general) is not very reliable.
We are not asking for a correct answer. We are asking for:
- your predicted state,
- how confident you are and what that confidence rests on,
- **what would change in your conclusions if you were wrong.**

Name the tool or reasoning you used. Candidates include Dimorphite-DL,
OpenBabel's `-p` option, or chemical reasoning from the functional groups
themselves — a carboxylic acid and a secondary amine do not need software.
An answer that says "the tool returned this and I am not confident in it,
here is why, and here is what it would change" is worth **more** than a
confident wrong answer.

**(e)** Connect this back to A0 (275 students): you spent A0 deciding whether
a fitted pKa was chemically believable. What is the relationship between that
question and this one?

## Reflection *(both courses — your own words, no agent)*

This will likely end up being one to two pages, but you're not being graded on length, but rather your answers. Do not have the agent write this, and do not have it edit
your draft.

1. Where did your agent produce something that was confidently wrong, or
   confidently incomplete? What tipped you off? If nothing did, what would
   have?
2. Which of the failures in this assignment would you have caught in your own
   work, and which would have gone straight into a result?
3. You now have a measured example of a representation problem changing a
   scientific result (Task 4b). Describe one place in your own research, or in
   a paper you have read, where the same thing could happen without anyone
   noticing.
4. What will you check from now on, before you trust a calculation on a
   molecule you did not draw yourself?

## What to submit

1. **Your agent session share link(s)**, covering all your work. The full
   prompt log is part of the record we grade.
2. **Your notebook** (`.ipynb`), with outputs saved, containing the agent's
   code, tables and figures. The agent can write this.
3. **A short written report** (Markdown or PDF), in your own words,
   containing:
   - your answers to the written parts of Tasks 1–4 (and Task 5, for 275),
     referring to the notebook's tables and figures as needed;
   - your Reflection.

Submit all three on Canvas.

## Rules for this assignment

- Use the agent freely for code, plots and tables. Use it to check your
  reasoning *after* you have done the reasoning. Use it to help you understand concepts you don't understand. 
- Every number in your report should be one you can point to in your
  notebook.
- Where this handout asks you to predict before computing, write the
  prediction down first. You are not graded on being right. You are graded on
  whether you noticed the difference between what you expected and what you
  got.
- If a result surprises you, say so. "This surprised me and here is what I
  think is going on" is particularly valuable.
