---
title: What dataformats are supported?
tags: [faq, dataformat, preprocessing, raw]
---

# What dataformats are supported?

FieldTrip has a flexible way for supporting data formats. It uses a small number of reading functions that provide a common interface to all electrophysiological (MEG/EEG) and MRI file formats: **[ft_read_header](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_header.m)**, **[ft_read_data](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_data.m)** and **[ft_read_event](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_event.m)**. Where needed, these functions will call the appropriate low-level functions that are apecific to the file format. Some of the low-level functions are written by ourselves, others are supplied by the manufacturers and again others are obtained from other open source toolboxes. You can find more technical information on the reading functions on [this](/development/module/fileio) page.

What follows is a summary of the data formats that are supported by FieldTrip. You can find more details on some of the formats, especially regarding the different MEG systems and their auxiliary files (e.g., MRI and volume conduction models). We regularly implement new data formats and this documentation may be out of date, so if yours is not listed here, please check the code.

The following MEG data formats are supported by **[ft_read_header](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_header.m)**, **[ft_read_data](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_data.m)** and **[ft_read_event](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_event.m)**:

- [CTF](/getting_started/ctf) (.ds, .res4, .meg4)
- [Neuromag/Elekta/MEGIN](/getting_started/neuromag) (.fif)
- [BTi/4D](/getting_started/bti) (.m4d, .pdf, .xyz, and 4D's raw data files)
- [Yokogawa/Ricoh](/getting_started/yokogawa) (.ave, .con, .raw)
- ITAB
- Tristan BabySquid

The following EEG data formats are supported by **[ft_read_header](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_header.m)**, **[ft_read_data](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_data.m)** and **[ft_read_event](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_event.m)**:

- [ANT Neuro](/getting_started/antneuro) (.avr, .cnt, .trg)
- BESA (.avr, .swf)
- [Biosemi BDF](/getting_started/biosemi) (.bdf)
- [BrainVision](/getting_started/brainvision) (.eeg, .seg, .dat, .vhdr, .vmrk)
- CED - Cambridge Electronic Design (.smr)
- EEGLAB (.set, .fdt)
- [Electrical Geodesics, Inc. (EGI)](/getting_started/egi) (.egis, .ave, .gave, .ses, .raw, .mff)
- NeuroScan (.eeg, .cnt, .avg)
- Nexstim (.nxe)
- TMSi (.Poly5)
- generic standard formats (.edf, .gdf)

The following EEG/MEG sensor formats are supported by **[ft_read_sens](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_sens.m)**:

- All supported MEG formats (CTF, Neuromag/Elekta/MEGIN, BTi/4D, Yokogawa/Ricoh)
- ASA electrode file
- BESA positions (numeric file with accompanying .elp and .ela)
- BESA (.sfp)
- CTF Polhemus recording, which is used at the Donders also for EEG electrodes
- EEGLAB electrode positions
- FIL Polhemus recording
- SPM5/SPM8/SPM12 electrode positions

The following spike and LFP data formats are supported by **[ft_read_spike](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_spike.m)** and **[ft_read_data](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_data.m)**:

- [Plexon](/getting_started/plexon) (.nex, .plx, .ddt)
- [Neuralynx](/getting_started/neuralynx) (.ncs, .nse, .nts, .nev, .nrd, .dma, .log)
- CED - Cambridge Electronic Design (.smr)
- MPI - Max Planck Institute (.dap)
- Windaq (.wdq)

The following NIRS data formats are supported by **[ft_read_header](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_header.m)**, **[ft_read_data](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_data.m)** and **[ft_read_event](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_event.m)**:

- Artinis Medical Systems (.oxy3, .oxy4)
- ASCII-formatted data from the NIRS system from Birkbeck college, London (.txt)
- Homer (.nirs)

The following eye-tracker data formats are supported by **[ft_read_header](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_header.m)**, **[ft_read_data](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_data.m)** and **[ft_read_event](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_event.m)**:

- SR Research EyeLink (.asc)
- SMI - SensoMotoric Instruments (.txt)
- Tobii (.tsv)

The following anatomical MRI data formats are supported by **[ft_read_mri](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_mri.m)**:

- NIFTI (.nii / .nii.gz), using the FreeSurfer toolbox
- mgz (.mgz), using the FreeSurfer toolbox
- Analzye (.hdr, .img), using the SPM toolbox
- MINC (.mnc), using the SPM toolbox
- CTF (.mri)
- ASA (.mri, .iso)
- DICOM (.ima, .dcm)

The following surface and volume mesh formats are supported by **[ft_read_headshape](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_headshape.m)**:

- Generic meshes (.stl, .off)
- Visualisation Toolkit (.vtk)
- Stanford Triangle Format (.ply)
- Wavefront (.obj)
- gifti
- Tetgen
- BrainVista
- Caret
- FreeSurfer
- BrainSuite

## See also

{% include seealso tag1="faq" tag2="dataformat" %}
