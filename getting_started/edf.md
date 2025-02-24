---
title: Getting started with EDF (European Data Format) data
tags: [dataformat, edf, eeg]
---

# Getting started with EDF (European Data Format) data

{% include markup/info %}
Please feel free to add information here if you're using EDF data and want to share info with other FieldTrip users.  
{% include markup/end %}

## Introduction

The [European Data Format (EDF)](http://www.edfplus.info) is a simple and flexible format for exchange and storage of multichannel biological and physical signals. It was developed by a few European 'medical' engineers who first met at the 1987 international Sleep Congress in Copenhagen. The EDF logo is derived from the congress logo which was the green pea from the fairy tale "The princess and the pea" by the Danish writer Hans Christian Andersen. With the support of the late professor Annelise Rosenfalck from Aalborg university, the engineers initiated the project "Methodology for the Analysis of the Sleep-Wakefulness Continuum" (1989-1992) that was funded by the European Community through its "Comité d'Action Concertée" (COMAC committee) on Biomedical Engineering. They wanted to apply their sleep analysis algorithms to each others data and compare the analysis results. So, on a morning in Leiden in March 1990, they agreed upon a very simple file format to exchange their sleep recordings. This format became known as the European Data Format. In August 1990, all participating labs had contributed an EDF sleep recording to the project.

EDF was published in 1992 in Electroencephalography and Clinical Neurophysiology 82, pages 391-393. Since then, EDF became the de-facto standard for EEG and PSG recordings in commercial equipment and multicenter research projects.

An extension of EDF, named EDF+, was developed in 2002 and is largely compatible to EDF: all existing EDF viewers also show EDF+ signals. But EDF+ files can also contain interrupted recordings, annotations, stimuli and events. Therefore, EDF+ can store any medical recording such as EMG, Evoked potentials, ECG, as well as automatic and manual analysis results such as deltaplots, QRS parameters and sleep stages. The specs are stricter than EDF which enables automatic localization and calibration of electrodes. And EDF+ fixed a few omissions in EDF such as the Y2K problem, little-endian integers, and comma vs dot.

EDF+ was published in 2003 in Clinical Neurophysiology 114, pages 1755-1761. Since then, hundreds of EDF+ files and several EDF+ viewers became available on the internet. Applications are mainly in Clinical Neurophysiology, Sleep, and Cardiology. Formal standards from other specialisms can also be integrated into EDF+. Most EDF applications have migrated to EDF+.

EDF and EDF+ are freely available without charge. The full specifications are in the above-mentioned publications as well as on the EDF website. The site also supports users and developers by offering free downloads of files and software, a list of EDF(+) compatible companies and further contact possibilities such as Yahoo's EDF group (source: http://www.edfplus.info).

## Preprocessing

### Preprocessing of continuous data

The preprocessing of continuous data can be as simple as this

    % define trials
    cfg            = [];
    cfg.dataset    = 'dataset.edf';
    cfg.continuous = 'yes';
    cfg.channel    = 'all';
    data           = ft_preprocessing(cfg);

followed by a visual inspection with

    % visually inspect the data
    cfg            = [];
    cfg.viewmode   = 'vertical';
    ft_databrowser(cfg, data);

In **[ft_databrowser](https://github.com/fieldtrip/fieldtrip/blob/release/ft_databrowser.m)** you can mark segments as artifacts, which can subsequently be removed with **[ft_rejectartifact](https://github.com/fieldtrip/fieldtrip/blob/release/ft_rejectartifact.m)**. You can also use **[ft_componentanalysis](https://github.com/fieldtrip/fieldtrip/blob/release/ft_componentanalysis.m)** and **[ft_rejectcomponent](https://github.com/fieldtrip/fieldtrip/blob/release/ft_rejectcomponent.m)** to subtract the artifactual EOG and ECG contributions from the data.

The code above reads the data as one long continuous segment (or trial). A common analysis strategy for continuous data is to use **[ft_redefinetrial](https://github.com/fieldtrip/fieldtrip/blob/release/ft_redefinetrial.m)** to cut it into shorter segments (e.g., one second) and **[ft_freqanalysis](https://github.com/fieldtrip/fieldtrip/blob/release/ft_freqanalysis.m)** with cfg.method='mtmfft' to compute the averaged power spectrum.

### Preprocessing of data with trials or events

The original EDF format did not allow for storing triggers or events. The EDF+ format extended the original format with “annotations”, which are stored in an extra channel. However, many cognitive/research EEG systems do not use the annotation channel. They rather use an extra channel with the binary TTL trigger values, sampled at the same rate as the EEG data itself. Or they use an extra analog channel that is connected to a photodiode. The lack of an adopted standard makes it a bit more difficult to work with events in EDF files.

#### EDF files that have a proper annotation channel

The annotation channel contains an ASCII encoded representation of the time in seconds that certain events happened. You can read the events using **[ft_read_event](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_event.m)** like this

    event = ft_read_event('testfile.edf', 'detect flank', [])

The detect flank option is explicitly disabled here, this indicates to the code that it should not try to read a continuously sampled TTL channel. With the following trialfun you can define trials (segments) for further processing.

#### Trial function that uses the annotation channel

    function [trl, event] = trialfun_annotation(cfg)

    % read the header, this is needed to determine the sampling rate of EEG channels
    hdr = ft_read_header(cfg.dataset);

    % read the events, don't detect flanks in a trigger channel but read annotations
    event = ft_read_event(cfg.dataset, 'detectflank', []);

    % make a selection of the Stimulus annotations
    sel = ismember({event.value}, {'Stimulus'});

    % determine the sample numbers of events
    smp = [event(sel).sample];

    begsample = smp-round(0.250*hdr.Fs);
    endsample = smp+round(0.750*hdr.Fs);
    offset    = -ones(size(begsample))*round(0.250*hdr.Fs);

    trl = [begsample(:) endsample(:) offset(:)];

    % remove trials that overlap with the beginning of the file
    sel = trl(:,1)>1;
    trl = trl(sel,:);

    % remove trials that overlap with the end of the file
    sel = trl(:,2)<hdr.nSamples;
    trl = trl(sel,:);

#### EDF files that are exported from EGI NetStation

Following the use of **[ft_read_header](https://github.com/fieldtrip/fieldtrip/blob/release/fileio/ft_read_header.m)**, the index of the annotation channel can be found in hdr.orig.annotation. However, when using the EGI 'Net Station', the events are written in a way that is not compatible with the edf+ reading implementation in FieldTrip. So, events do not come out properly. Also discontinuous epochs are "glued" together as one "continuous" data stream (please add, anyone, whether this also holds for other acquisition systems).

Events can also be stored in an extra channel that is appended to the brain data channels. For instance, a photodiode transducer attached to a stimulus presentation screen may convert screen light into current. This way, changes in light are captured as an analog signal in that data channel and can be used to synchronize timing of the experiment with the brain recording. When the name or the channel index of the event channel is known, it becomes feasible to use that channel for reading data segments or trials of interest into the MATLAB work environment using FieldTrip. The most straightforward approach is to create a trial function (as invoked with cfg.trialfun) that reads in the event channel and performs some form of thresholding on the signal in that channel to determine the events of interest. We provide an example pipeline below.

    % define trials
    cfg            = [];
    cfg.dataset    = 'dataset.edf';
    cfg.trialfun   = 'trialfun_ttl';
    cfg            = ft_definetrial(cfg);

where 'trialfun_ttl' is a self-specified MATLAB function for conditional selection of data segments or trials of interest. See below and [this page](/example/making_your_own_trialfun_for_conditional_trial_definition) for examples.

    % read and preprocess the data
    cfg.continuous = 'yes';
    cfg.channel    = 'all';
    data           = ft_preprocessing(cfg);

Note that filtering, re-referencing, etcetera can be performed at the preprocessing stage. Type 'help ft_preprocessing' to get an overview of the possibilities. Now that the raw data is in the MATLAB environment it becomes possible, as shown below, to use ft_databrowser to browse through the raw segmented data. See also [this page](/tutorial/visual_artifact_rejection) for a number of strategies on how to inspect and clean up the data from artifacts.

    % visually inspect the data
    cfg            = [];
    cfg.viewmode   = 'vertical';
    ft_databrowser(cfg, data);

### Trial functions

Hereunder are two examples of a trial function for parsing a continuously sampled TTL channel. See also [this page](/example/making_your_own_trialfun_for_conditional_trial_definition) for more examples. Ensure that your trial function is available on the MATLAB path for it to be found by MATLAB and invoked by the call to ft_define_trial (see above). The below examples assume that the experiment events are stored in a data channel whose name or index (referred to as chanindx in the code) is known to the user.

The example scripts also assume that the event is marked by an 'up flank' in the recorded signal (e.g., by virtue of an increase in light on the photodiode transducer). Down flanks can also be detected by specifying cfg.detectflank = 'down'. The trigger threshold can be a hard threshold, i.e. numeric, or flexibly defined by an executable string (e.g., to calculate the 'median' of the analog signal). In the first example, we define a 'segment' as one second preceding this trigger until 2 seconds thereafter:

    function [trl, event] = trialfun_ttl(cfg)

    % read the header information
    hdr           = ft_read_header(cfg.dataset);

    % read the events from the data
    chanindx      = 1;
    detectflank   = 'up';
    threshold     = '(3/2)*nanmedian'; % or, e.g., 1/2 times the median for down flanks
    event         = ft_read_event(cfg.dataset, 'chanindx', chanindx, 'detectflank', detectflank, 'threshold', threshold);

    % define trials around the events
    trl           = [];
    pretrig       = 1 * hdr.Fs; % e.g., 1 sec before trigger
    posttrig      = 2 * hdr.Fs; % e.g., 2 sec after trigger
    for i = 1:numel(event)
      offset    = -hdr.nSamplesPre;  % number of samples prior to the trigger
      trlbegin  = event(i).sample - pretrig;
      trlend    = event(i).sample + posttrig;
      newtrl    = [trlbegin trlend offset];
      trl       = [trl; newtrl]; % store in the trl matrix
    end

In this second example, we define a data segment as the time the trigger was 'on', i.e. during the 'up flank', until it was 'off

    function [trl, event] = trialfun_ttl(cfg)

    % read the header information
    hdr           = ft_read_header(cfg.dataset);

    % read the events from the data
    chanindx      = 1;
    detectflank   = 'both'; % detect up and down flanks
    threshold     = '(3/2)*nanmedian';
    event         = ft_read_event(cfg.dataset, 'chanindx', chanindx, 'detectflank', detectflank, 'threshold', threshold);

    % look for up events that are followed by down events (peaks in the analog signal)
    trl           = [];
    waitfordown   = 0;
    for i = 1:numel(event)
    if ~isempty(strfind(event(i).type, 'up')) % up event
      uptrl     = i;
      waitfordown = 1;
    elseif ~isempty(strfind(event(i).type, 'down')) && waitfordown % down event
      offset    = -hdr.nSamplesPre;  % number of samples prior to the trigger
      trlbegin  = event(uptrl).sample;
      trlend    = event(i).sample;
      newtrl    = [trlbegin trlend offset];
      trl       = [trl; newtrl]; % store in the trl matrix
      waitfordown = 0;
    end
    end

Alternatively, when the event channel index is unknown, but its name, or a part thereof (e.g., 'DC01'), is, one may us
chanindx = find(ismember(hdr.label, ft_channelselection('_DC01_', hdr.label)));

## See also

{% include seealso tag="edf" %}
