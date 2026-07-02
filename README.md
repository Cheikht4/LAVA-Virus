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

The engine relies on the following exact software stack (specified in `environment.yml`):
- **Perl** 5.26.2+
- **Primer3** 2.6.1
- **BioPerl** 1.6.924
- Core Perl modules: `XML::LibXML`, `LWP::Simple`, `YAML`

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
sudo apt update && sudo apt install -y perl primer3 libbioperl-perl cpanminus libxml-libxml-perl

# Or on macOS via Homebrew
brew install perl primer3 cpanminus
cpanm BioPerl XML::LibXML
```

## Command-Line Usage

LAVA-Virus provides two main entry scripts:
1. `lava_stem_primer.pl` - Designs fundamental LAMP stem primer sets (F3/B3 outer primers and FIP/BIP inner primers targeting F1c-F2 and B1c-B2 regions).
2. `lava_loop_primer.pl` - Designs Loop Forward (FLOOP) and Loop Backward (BLOOP) primers to accelerate amplification for pre-computed stem signatures.

### Example Workflow

#### 1. Designing Stem Primers
Run `lava_stem_primer.pl` on a multiple sequence alignment or target sequence file:
```bash
perl lava_stem_primer.pl --target target_alignment.fasta --output results/stem_out
```

#### 2. Designing Loop Primers
Once valid stem signatures are generated, run `lava_loop_primer.pl` to discover matching loop primers:
```bash
perl lava_loop_primer.pl --target target_alignment.fasta --signatures results/stem_out --output results/loop_out
```

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
