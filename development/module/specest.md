---
title: Spectral estimation of of EEG/MEG time series data
tags: [development, specest]
---

# Spectral estimation of of EEG/MEG time series data

The spectral estimation toolbox for FieldTrip, the specest module, contains a consistent set of functions for spectral estimation and decomposition of electrophysiological data. These specest functions allow other projects (such as SPM) to re-use the implemented methods separate from FieldTrip and facilitates external contributions to FieldTrip by separating the user interface from the functionality.

The low-level functions are combined in the **specest** toolbox which is self-contained and does not depend on other FieldTrip functions. It is available for download [here](ftp://ftp.fieldtriptoolbox.org/pub/fieldtrip/modules/).

## Specest Functions

1.  **[ft_specest_mtmconvol](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_mtmconvol.m)** Wavelet convolution in the time-domain by multiplication in the frequency domain. Wavelets used are complex sinusoids multiplied with a windowing function (e.g., Slepian, Hanning, Hamming, ...).

2.  **[ft_specest_mtmfft](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_mtmfft.m)** Non-time resolved spectral estimation by tapering the data with a windowing function (e.g., Slepian, Hanning, Hamming,...) prior to compution the Fourier transform.

3.  **[ft_specest_hilbert](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_hilbert.m)** Spectral estimation by bandpass filtering the data prior to computing the Hilbert transform.

4.  **[ft_specest_wavelet](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_wavelet.m)** Wavelet convolution in the time-domain by multiplication in the frequency domain. Wavelets used are Morlet wavelets.

5.  **[ft_specest_tfr](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_tfr.m)** Wavelet convolution in the time-domain using Morlet wavelets.

## Definition of the function-calls (API)

The functions should be called as

    [spectrum, ntaper, freqoi, timeoi]  = ft_specest_mtmconvol(dat, time, ...)
    [spectrum, ntaper, freqoi]          = ft_specest_mtmfft   (dat, time, ...)
    [spectrum, freqoi, timeoi]          = ft_specest_hilbert  (dat, time, ...)
    [spectrum, freqoi, timeoi]          = ft_specest_wavelet  (dat, time, ...)
    [spectrum, freqoi, timeoi]          = ft_specest_tfr      (dat, time, ...)

where `dat` consists of a matrix of dimensionality Nchan x Ntime, and `time` is a 1xNtime vector containing the time indices in seconds. The time vector is used to compute the sampling rate, and to determine, in the case of mtmfft, where t=0 resides with respect to `dat`.

Each function can take a set of common and function-specific additional arguments in key-value pairs. Which additional arguments a function can take is described in the reference documentation: **[ft_specest_mtmconvol](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_mtmconvol.m)**, **[ft_specest_mtmfft](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_mtmfft.m)**, **[ft_specest_hilbert](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_hilbert.m)**, **[ft_specest_wavelet](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_wavelet.m)**, **[ft_specest_tfr](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_tfr.m)**.

##### Definition of Phase

In the case of **[ft_specest_mtmconvol](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_mtmconvol.m)**, the phase at the peak of an oscillation in the data is defined to be 0, and the trough of an oscillation in the data is defined to be π/-π, progressing counter-clockwise around the circle.

In the case of **[ft_specest_mtmfft](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_mtmfft.m)**, the average phase of the data segment is from the perspective that an oscillation in the data has a phase of 0, i.e. is at it's peak (following the same convention as ft_specest_mtmconvol), at time-point t=0.

For **[ft_specest_hilbert](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_hilbert.m)** how phase is defined at the peak and at the trough of an oscillation in the data depends on the type of filter and the filter order that is used to bandpass filter the data, prior to computing the Hilbert transform.

For **[ft_specest_wavelet](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_wavelet.m)** and **[ft_specest_tfr](https://github.com/fieldtrip/fieldtrip/blob/release/specest/ft_specest_tfr.m)** how phase is defined at the peak and at the trough of an oscillation in the data depends on the width of the Gaussian that is used.
