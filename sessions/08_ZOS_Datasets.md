# z/OS Datasets

Contents
1. Linux Filesystem Comparison Point
2. z/OS Dataset Types
3. Deeper (Theory)
4. Practical Management of Datasets

## 1. Linux File Systems - comparison point.

## 2. Introduction to Dataset Types in z/OS

Sequential datasets (PS)
Partitioned datasets (PDS and PDSE)
Virtual Storage Access Method (VSAM)
Generation Data Groups (GDGs)
z/OS File System - Posix(ZFS)

## 3. Defining and Using Datasets (Theory)

Sequential Datasets:
* Characteristics: Linear access, fixed or variable record lengths.
* Use cases: Storing large amounts of sequential data.

Partitioned Datasets (PDS and PDSE):
- Structure: Directory with members.
- Applications: Source code, scripts, and libraries.

VSAM Datasets:
- Features: Indexed or direct access capabilities.
- Use cases: Efficient concurrent read and update operations.

Generation Data Groups (GDGs):
- Purpose: Version control and historical data retention

## 4. Management of Datasets (Practical)

Allocating datasets in JCL and ISPF Panels:
- Inspect Dataset Allocation parameters for existing datasets
- Allocate New Datasets using ISPF (SEQ, PDS, PDSE)
- JCL to define New Datasets (SEQ, PDS, PDSE)
- SMS attributes for storage management. (introductory)

Working with Datasets - browse, edit, copy etc...

VSAM Datasets
- view
- allocate
- work with

ZFS Datasets
- view
- allocate
- mount
- work with
