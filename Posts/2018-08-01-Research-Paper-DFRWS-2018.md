---
title: Research Paper Presented at DFRWS 2018 USA
date: 2018-08-01 20:00:00
---

A research paper that I co-authored was presented at DFRWS (Digital Forensic
Research Conference) 2018 USA. As a contributor, I am proud to have the chance to
work with two researchers, Jan-Niclas Hilgert and Martin Lambertz from Fraunhofer
FKIE, Bonn, Germany.

The paper was titled as **Forensic analysis of multiple device BTRFS configurations
using The Sleuth Kit**. It discussed the multi-device feature offered by BTRFS, a modern
filesystem designed for Linux to achieve rich functions and better performance.

Since BTRFS implements many advanced concepts like subvolumes, snapshots, copy on
write (CoW). This paper attempted to resolve some problems brought by BTRFS
multi-device support whose answers could not be found via traditional digital
forensics process.

The complete paper can be found on DFRWS 2018 USA website. It was also published
on Digital Investigation Volume 26, Supplement, July 2018, Pages S21-S29 with open
access. Check either of the following links to read and download the paper:

[DFRWS - Papers & Presentations](https://www.dfrws.org/conferences/dfrws-usa-2018/sessions/forensic-analysis-multiple-device-btrfs-configurations-using)

[Elsevier - Digital Investigation - Proceedings of the Eighteenth Annual DFRWS USA](https://www.sciencedirect.com/science/article/pii/S1742287618301993)
<!--excerpt-->

### Abstract:

The analysis of file systems is a fundamental step in every forensic investigation. Long-known file
systems such as FAT, NTFS, or the ext family are well supported by commercial and open source forensics
tools. When it comes to more recent file systems with technologically advanced features, however, most
tools fall short of being able to provide an investigator with means to perform a proper forensic analysis.

BTRFS is such a file system which has not received the attention it should have. Although introduced in
2007, marked as stable in 2014, and being the default file system in certain Linux distributions, there is
virtually no research available in the area of digital forensics when it comes to BTRFS; nor are there any
software tools capable of analyzing a BTRFS file system in a way required for a forensic analysis.

In this paper we add support for BTRFS-including support for multiple device configurations-to The
Sleuth Kit, a widely used toolkit when it comes to open source file system forensics. Moreover, we
provide an analysis of forensically important features of BTRFS and show how our implementation can be
used to utilize these during a forensic analysis.