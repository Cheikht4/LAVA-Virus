# LAVA-Virus - LAMP Primer Design Engine for Hypervariable Genomes

[![Perl](https://img.shields.io/badge/Perl-5.26.2-orange.svg)](https://perl.org)
[![Primer3](https://img.shields.io/badge/Primer3-2.6.1-purple.svg)](https://primer3.org)
[![BioPerl](https://img.shields.io/badge/BioPerl-1.6.924-blue.svg)](https://bioperl.org)
[![License](https://img.shields.io/badge/License-BSD--3--Clause-yellow.svg)](LICENSE)

## Overview

**LAVA-Virus** is an advanced open-source scientific engine written in Perl for designing Loop-Mediated Isothermal Amplification (LAMP) primer sets optimized for genomic diversity and highly variable viral targets. Derived from the original LAVA algorithm, LAVA-Virus introduces modern thermodynamic modeling, asymmetric sigmoid penalty scoring, and full degenerate base (IUPAC) incorporation directly into the primer search space.

By evaluating multiple sequence alignments (MSAs) and calculating exact signature intersections across variant targets, LAVA-Virus ensures high diagnostic coverage while respecting strict enzymatic kinetics at isothermal amplification temperatures (65°C).

A web interface is also available at [URL to be completed].

## System Requirements

The engine relies on the following software stack (specified in `environment.yml` and `Makefile.PL`):
- **Perl** 5.26.2+
- **Primer3** 2.6.1 (or >= 2.5 with embedded thermodynamic parameters)
- **BioPerl** 1.6.924 (`Bio::SeqIO`, `Bio::AlignIO`, `Bio::SimpleAlign`, `Bio::LocatableSeq`)
- Core Perl modules: `XML::LibXML`, `Clone` (transitive dependency via `Bio::Root::Root`)

## Installation

### Option 1 - Using Conda / Mamba (Recommended)

You can create an isolated environment with all dependencies pre-installed using the provided `environment.yml`:

```bash
conda env create -f environment.yml
conda activate primer3
```

### Option 2 - Manual Installation

If installing dependencies manually on Linux or macOS:
```bash
# Install system packages (Debian/Ubuntu example)
sudo apt update && sudo apt install -y perl primer3 bioperl libxml-libxml-perl libclone-perl

# Or on macOS via Homebrew
brew install perl primer3 cpanminus
cpanm BioPerl XML::LibXML Clone
```

## Command-Line Usage

LAVA-Virus provides two independent LAMP primer design scripts:
1. `lava_loop_primer.pl` - Designs loop-LAMP primer sets (`F3, B3, FIP [F1c+F2], BIP [B1c+B2], FLOOP, BLOOP`) or classical 6-primer LAMP sets (`--include_loop_primers 0`).
2. `lava_stem_primer.pl` - Designs stem-LAMP primer sets (`F3, B3, FIP [F1c+F2], BIP [B1c+B2], FSTEM, BSTEM`) or classical 6-primer LAMP sets (`--include_stem_primers 0`).

### Quick Start / Example Workflow

Run the two scripts on the included 40-sequence Rotavirus VP6 alignment (`t/fixtures/rota_canary_40.fasta`) using the XML preset files in `t_data/`:

#### 1. Designing Loop-LAMP Primers (8 primers)
```bash
perl -Ilib lava_loop_primer.pl --option_file t_data/normal_parameters.xml \
  --alignment_fasta t/fixtures/rota_canary_40.fasta --output_file /tmp/out_loop.fasta
```

#### 2. Designing Stem-LAMP Primers (8 primers)
```bash
perl -Ilib lava_stem_primer.pl --option_file t_data/normal_stem_parameters.xml \
  --alignment_fasta t/fixtures/rota_canary_40.fasta --output_file /tmp/out_stem.fasta
```

#### 3. Designing Classical 6-Primer LAMP Sets (F3, B3, F2, B2, F1, B1)
```bash
perl -Ilib lava_loop_primer.pl --option_file t_data/normal_parameters.xml \
  --alignment_fasta t/fixtures/rota_canary_40.fasta --output_file /tmp/out_classic.fasta \
  --include_loop_primers 0
```

### Parameter Presets (`t_data/`)

- **Command-line precedence**: Any option passed on the command line overrides the corresponding tag in `--option_file` (for instance `--alignment_fasta`, `--output_file`, or `--include_loop_primers 0`).
- **Loop-LAMP preset gradation** on `t/fixtures/rota_canary_40.fasta` (40 sequences):
  - `t_data/strict_parameters.xml` : **1,121** candidate combinations evaluated -> **1** final signature (`37/40` sequences, `92.50%` coverage, penalty `7.90`).
  - `t_data/normal_parameters.xml` : **5,418** candidate combinations evaluated -> **1** final signature (`37/40` sequences, `92.50%` coverage, penalty `7.90`).
  - `t_data/loose_parameters.xml` : **17,333** candidate combinations evaluated -> **1** final signature (`37/40` sequences, `92.50%` coverage, penalty `7.90`).
  - Classical 6-primer mode (`--option_file t_data/normal_parameters.xml --include_loop_primers 0`): **27,433** candidate combinations evaluated -> **1** final signature (`37/40` sequences, `92.50%` coverage, penalty `3.70`).
- **Note on `t_data/normal_stem_parameters.xml`**: The test fixture `t/fixtures/rota_canary_40.fasta` offers only **205 exploitable nucleotides**, which is very short for stacking 8 non-overlapping primers (`F3, F2, F1, FSTEM, BSTEM, B1, B2, B3`). Accordingly, `t_data/normal_stem_parameters.xml` uses reduced stem primer lengths (`15/18/22 nt`) tailored to this short test window (**417** candidate combinations evaluated -> **1** final signature, `92.50%` coverage, penalty `7.89`); it serves as an installation-verification starting point rather than a universal production preset.

## Output Files

The pipeline outputs structured results summarizing thermodynamic stability and candidate geometry:
- `*.primers` - Complete catalog of candidate primers generated across targeted genomic windows, detailing Tm, GC content, degenerate positions, and individual coverage penalties.
- `*.all_signatures` - Exhaustive list of valid LAMP primer set combinations (signatures) ranked by total penalty score.
- `*.dash` - Summary dashboard metrics report providing global coverage statistics per primer role (F3, B3, F2, B2, F1c, B1c, FLOOP, BLOOP).
- `*.fasta` - Target sequences formatted with annotation headers indicating primer hybridization loci.

## License

This project is dual-licensed under the **BSD 3-Clause License**:
- **Part 1 (Inherited LAVA engine)**: Copyright (c) 2010, Lawrence Livermore National Security, LLC. Written by Clinton Torres.
- **Part 2 (Extended modules & thermodynamic enhancements)**: Copyright (c) 2026, Cheikh Talibouya.

See the [LICENSE](LICENSE) file for complete terms.

## Acknowledgments & Credits

- **Original LAVA Engine**: Developed at Lawrence Livermore National Laboratory (LLNL) by Clinton Torres (2010).
- **Algorithmic Foundations**: LAVA methodology and conservation scoring adaptations referenced in Bekaert et al. (2016).
- **Primer3 Core**: Rozen & Skaletsky for the foundational Primer3 thermodynamic calculation library.

### Legacy Funding & Grant Notices

This work was supported by a NIBIB Point-of-Care Technologies Research Network Center grant (Gerald J. Kost, PI of the UC Davis / LLNL POCT Center, NIH U54 EB007959). The content is solely the responsibility of the authors and does not necessarily represent the official views of the National Institute of Biomedical Imaging and Bioengineering or the National Institutes of Health.

This work performed under the auspices of the U.S. Department of Energy by Lawrence Livermore National Laboratory under Contract DE-AC52-07NA27344.

### Release History

- **LAVA release 0.1.3**: Compatibility with Primer3 thermodynamic embedded parameters.
- **LAVA release 0.1.2** (Michael Bekaert): Cross-distribution Linux compatibility and parameter exposure (`outer_pair_target_length`, `middle_pair_target_length`, `inner_pair_target_length`).
- **LAVA release 0.1.1** (Michael Bekaert): Primer3 and BioPerl updates, thermodynamic path parameter (`PRIMER_THERMODYNAMIC_PARAMETERS_PATH`).
- **LAVA release 0.1** (Clinton Torres): Original distribution and LAMP multiple sequence alignment algorithm.
