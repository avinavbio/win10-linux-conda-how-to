# WSL + Conda/Bioconda for Bioinformatics

A practical guide to building a Linux-based bioinformatics environment on Windows using **WSL 2** and **Conda/Bioconda**.

> **Updated for 2026:** This repository preserves the original Windows 10 tutorial, but the installation instructions below have been modernized for current WSL 2 and Bioconda workflows.

## What this guide covers

1. Install WSL 2 and Ubuntu
2. Verify and manage WSL
3. Set up Linux for bioinformatics
4. Install Conda
5. Configure Bioconda correctly
6. Create isolated environments
7. Install bioinformatics software
8. Keep environments reproducible
9. Useful WSL and Conda commands
10. Legacy Windows 10 notes

---

## Why WSL for bioinformatics?

WSL allows Linux distributions and Linux command-line tools to run directly on Windows without a traditional virtual machine or dual-boot installation.

For bioinformatics, this provides access to the Linux ecosystem while retaining Windows applications for tasks such as documentation, visualization, and general desktop work.

Microsoft currently recommends WSL 2 for new installations. The `wsl --install` command can install the required components and Ubuntu on supported Windows versions. [Microsoft WSL documentation](https://learn.microsoft.com/en-us/windows/wsl/install) provides the current requirements and options.

---

# 1. Install WSL 2

### Recommended method

Open **PowerShell as Administrator** and run:

```powershell
wsl --install
```

Restart Windows if prompted.

The default installation includes Ubuntu. To see available distributions:

```powershell
wsl --list --online
```

To install a specific distribution:

```powershell
wsl --install -d Ubuntu
```

For supported Windows versions, Microsoft states that `wsl --install` enables the required components, installs the Linux kernel, sets WSL 2 as the default, and installs Ubuntu. [Microsoft](https://learn.microsoft.com/en-us/windows/wsl/install)

### Check your WSL installation

```powershell
wsl --status
wsl --version
wsl --list --verbose
```

A typical installation should show your distribution using **WSL 2**.

If necessary:

```powershell
wsl --set-version Ubuntu 2
```

Set WSL 2 as the default for future distributions:

```powershell
wsl --set-default-version 2
```

---

# 2. Start Ubuntu

Launch **Ubuntu** from the Windows Start menu, or use:

```powershell
wsl
```

The first launch asks you to create a Linux username and password.

These credentials are separate from your Windows account.

Update the Linux system:

```bash
sudo apt update
sudo apt upgrade
```

Useful basic commands:

```bash
pwd
ls
cd
mkdir
cp
mv
rm
```

---

# 3. Working with Windows files

Windows drives are normally accessible inside WSL under `/mnt`.

For example:

```bash
/mnt/c
/mnt/d
```

For bioinformatics projects, it is generally preferable to keep Linux-heavy working files inside the WSL Linux filesystem rather than repeatedly processing large datasets through mounted Windows paths.

Microsoft's WSL guidance also provides recommendations for file storage and development workflows:

https://learn.microsoft.com/en-us/windows/wsl/setup/environment

---

# 4. Install Conda

Conda creates isolated environments so different bioinformatics projects can use different software and dependency versions.

## Option A — Miniconda

Download the current Linux x86_64 installer from the official Miniconda documentation:

https://www.anaconda.com/docs/getting-started/miniconda/install

For example, after downloading the current installer:

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

Follow the installer prompts.

Then reload your shell:

```bash
source ~/.bashrc
```

Check the installation:

```bash
conda --version
conda info
```

> **Do not use the old `repo.continuum.io/miniconda` command from the original version of this tutorial.** The original repository predates the current Miniconda installation workflow.

## Alternative: Miniforge

Miniforge is another lightweight option, particularly useful for workflows centered on conda-forge. Bioconda's documentation discusses Miniforge and other conda-compatible approaches.

---

# 5. Configure Bioconda

The Bioconda channel configuration has changed since this repository was originally written.

### Current recommended Conda configuration

Run:

```bash
conda config --add channels bioconda
conda config --add channels conda-forge
conda config --set channel_priority strict
```

Bioconda currently recommends **conda-forge + bioconda with strict channel priority** and no longer recommends adding `defaults` for the standard Bioconda setup. [Bioconda documentation](https://bioconda.github.io/)

Check your configuration:

```bash
conda config --show channels
conda config --show channel_priority
```

---

# 6. Create an environment

Avoid installing every bioinformatics program into the base environment.

Instead, create an environment for a project or workflow.

Example:

```bash
conda create -n bioinfo python=3.11
conda activate bioinfo
```

Install packages into the environment:

```bash
conda install pandas numpy matplotlib
```

For bioinformatics tools:

```bash
conda create -n ngs fastqc multiqc bwa samtools
conda activate ngs
```

You can also create an environment directly with the required channels:

```bash
conda create -n ngs fastqc multiqc bwa samtools \
  --channel conda-forge \
  --channel bioconda \
  --strict-channel-priority
```

---

# 7. Example: Installing common bioinformatics tools

### FastQC

```bash
conda install fastqc
fastqc --version
```

### BWA

```bash
conda install bwa
bwa
```

### Samtools

```bash
conda install samtools
samtools --version
```

### MultiQC

```bash
conda install multiqc
multiqc --version
```

### STAR

```bash
conda install star
STAR --version
```

The exact package availability and versions change over time, so check the current Bioconda package index when choosing software.

---

# 8. Reproducible environments

One of Conda's major advantages is the ability to export an environment.

Export:

```bash
conda env export > environment.yml
```

A more portable approach is to export the explicitly requested packages:

```bash
conda env export --from-history > environment.yml
```

Recreate the environment:

```bash
conda env create -f environment.yml
```

List environments:

```bash
conda env list
```

Remove an environment:

```bash
conda env remove -n myenvironment
```

For reproducible research, keep the `environment.yml` file with the project code whenever practical.

---

# 9. Useful Conda commands

### Create an environment

```bash
conda create -n myenv
```

### Create an environment with a Python version

```bash
conda create -n py311 python=3.11
```

### Activate

```bash
conda activate myenv
```

### Deactivate

```bash
conda deactivate
```

### List environments

```bash
conda env list
```

### List installed packages

```bash
conda list
```

### Search packages

```bash
conda search samtools
```

### Install a package

```bash
conda install samtools
```

### Remove a package

```bash
conda remove samtools
```

### Update Conda

```bash
conda update -n base conda
```

---

# 10. Common WSL commands

From PowerShell:

```powershell
wsl --list --verbose
wsl --shutdown
wsl --update
wsl --status
```

Open a specific distribution:

```powershell
wsl --distribution Ubuntu
```

Set the default distribution:

```powershell
wsl --set-default Ubuntu
```

Microsoft maintains the current WSL command reference here:

https://learn.microsoft.com/en-us/windows/wsl/basic-commands

---

# 11. Troubleshooting

## `conda: command not found`

First try:

```bash
source ~/.bashrc
```

Then:

```bash
conda --version
```

If Conda was installed but the shell was not initialized:

```bash
~/miniconda3/bin/conda init bash
source ~/.bashrc
```

If you installed Conda somewhere else, replace the path accordingly.

## WSL distribution will not start

Check:

```powershell
wsl --list --verbose
wsl --status
wsl --update
```

Then restart WSL:

```powershell
wsl --shutdown
```

and launch Ubuntu again.

For installation-specific problems, consult Microsoft's current WSL troubleshooting documentation.

---

# 12. Original tutorial and historical notes

This repository originally documented a **Windows 10 + WSL 1 + Miniconda/Bioconda** workflow.

The original guide was useful for its time, but several assumptions are now obsolete:

- Windows 10 build 16215 is no longer the current baseline.
- WSL 2 is now the normal choice for new installations.
- The Microsoft Store installation workflow has changed.
- Python 2.7, 3.4, 3.5 and 3.6 should not be presented as general requirements for modern Conda.
- The old `repo.continuum.io` Miniconda URL should not be used.
- Bioconda's recommended channel configuration has changed.
- The old `defaults + bioconda + conda-forge` configuration should be replaced with the current Bioconda recommendation.

The original images and historical material remain in this repository for reference.

---

# 13. Recommended bioinformatics workflow

A clean project can follow this pattern:

```text
project/
├── README.md
├── data/
├── results/
├── scripts/
├── notebooks/
├── environment.yml
└── docs/
```

Create the environment:

```bash
conda env create -f environment.yml
conda activate <environment-name>
```

Keep raw data, analysis scripts, results, and software dependencies clearly separated.

---

## References

- [Microsoft — Install WSL](https://learn.microsoft.com/en-us/windows/wsl/install)
- [Microsoft — WSL development environment](https://learn.microsoft.com/en-us/windows/wsl/setup/environment)
- [Microsoft — Basic WSL commands](https://learn.microsoft.com/en-us/windows/wsl/basic-commands)
- [Bioconda — Usage](https://bioconda.github.io/)
- [Bioconda — FAQs](https://bioconda.github.io/faqs.html)
- [Anaconda — Miniconda installation](https://www.anaconda.com/docs/getting-started/miniconda/install)
- [Bioconda publication — Nature Methods](https://www.nature.com/articles/s41592-018-0046-7)

---

### License

This repository contains an educational guide and historical documentation. See [LICENSE](LICENSE) for the repository license.

> **Bioinformatics on Windows, powered by Linux.**
