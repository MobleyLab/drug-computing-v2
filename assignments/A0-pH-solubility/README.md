# A0: Is this pKa real? (pH-dependent solubility)

Assignment A0 for PharmSci 275 (graduate section), Winter 2027. Students use
a coding agent to fit real pH-dependent aqueous solubility measurements,
estimate pKa values, compare them with independent titration results, and
judge which results make chemical sense.

## Contents

| File | What it is |
|---|---|
| [`handout.md`](handout.md) | The assignment handout: background, tasks, submission instructions |
| [`data/Aqueous_Solubility for SAMPL8 Pub.xlsx`](data/) | Experimental pH-solubility data for the 23 SAMPL8 physical-property compounds, unmodified |

## Data provenance

The spreadsheet is copied **unmodified** (byte-identical) from the SAMPL8
challenge repository:

- **Source:** [samplchallenges/SAMPL8](https://github.com/samplchallenges/SAMPL8),
  file `physical_properties/experimental_data/Aqueous_Solubility for SAMPL8 Pub.xlsx`,
  at commit [`ae85dbd`](https://github.com/samplchallenges/SAMPL8/tree/ae85dbd542fea45819112200b4247cddb2e1f331/physical_properties/experimental_data).
  The SAMPL8 repository is distributed under the MIT license.
- **Measured by:** GSK, provided by Matt Bahr to the SAMPL8 organizers on
  Feb. 12, 2021.
- **Experimental paper:** Bahr, M. N. et al. Automated high throughput pKa and
  distribution coefficient measurements of pharmaceutical compounds for the
  SAMPL8 blind prediction challenge. *J. Comput.-Aided Mol. Des.* **35**,
  1141–1155 (2021). [doi:10.1007/s10822-021-00427-0](https://doi.org/10.1007/s10822-021-00427-0)

The titration pKa values quoted in the handout (Sirius T3) are also from the
SAMPL8 repository (`pKa - SiriusT3 measurements.csv`). They were measured by
the Czodrowski lab and provided in June 2022.

The spreadsheet is deliberately left in its original layout: extracting the
right data from it is part of the assignment.
