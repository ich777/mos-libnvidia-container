# MOS libnvidia-container

libnvidia-container provides a **packaged libnvidia-container library**
for use within the MOS ecosystem.

This repository contains the **build scripts, packaging logic, and automation**
required to compile and package libnvidia-container for MOS.

---

## Overview

libnvidia-container is built from **upstream sources** and packaged for
integration into MOS.

It provides the low-level functionality required by the NVIDIA Container
Toolkit to enable GPU access inside containers.

No functional changes to the upstream library are intended.  
The goal of this repository is to provide **consistent and reproducible
libnvidia-container packages** for MOS.

---

## Licensing

The contents of this repository (build scripts, configuration files, and automation)
are licensed under **GPL-3.0**.

libnvidia-container itself remains licensed under its respective upstream license.

---

## Third-Party Software

This repository builds and packages third-party open-source software.
Packaged components remain licensed under their original upstream licenses.

Refer to `THIRD_PARTY.md` for details.
