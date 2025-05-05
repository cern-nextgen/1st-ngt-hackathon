---
title: Fast Inference for ML
layout: main
section: ml
---

Inference is a crucial step in the machine learning workflow—especially in High-Energy Physics—where data is produced at extremely high rates and needs to be processed rapidly and efficiently. As part of the NGT, we’ve been exploring methods for fast ML inference optimized for heterogeneous architectures.

During the hackathon, we made significant progress toward our objectives. Our focus was on extending the capabilities of SOFIE—a tool developed at CERN for fast ML inference. We contributed by developing new features, addressing user requests, fixing bugs, and improving overall usability.

Highlights and Progress from the Hackathon:
- Introduction sessions on [ROOT](https://github.com/root-project/root), [TMVA](https://root.cern/manual/tmva/), and [SOFIE](https://github.com/root-project/root/tree/master/tmva/sofie)
- Successfully built ROOT with SOFIE
- Experiments with the Python interface to SOFIE
- Development and release of:
    - [SOFIE Standalone](https://github.com/sanjibansg/sofie)
    - [ROOT Docker image with SOFIE](https://hub.docker.com/repository/docker/sanjibansg/root-image/general)
- Discussions on memory allocation and memory planning in SOFIE
- Extended SOFIE support for user models ([PR #18348](https://github.com/root-project/root/pull/18348)):
    - SPANet
    - Smart Pixels
- Enhancements for [dynamic computation support](https://github.com/root-project/root/pull/18633) in SOFIE
- Implementation of [optimization modes](https://github.com/root-project/root/pull/18355) in SOFIE
- Initial integration of ALPAKA into SOFIE
- Integration of SOFIE with CLAD, including unit tests for validation. PR: [#18332](https://github.com/root-project/root/pull/18332), [#18341](https://github.com/root-project/root/pull/18341), [#18364](https://github.com/root-project/root/pull/18364), [#18476](https://github.com/root-project/root/pull/18476).
- Several bug fixes and general improvements. PR: [#18297](https://github.com/root-project/root/pull/18297), [#18299](https://github.com/root-project/root/pull/18299), [#18302](https://github.com/root-project/root/pull/18302,) [#18324](https://github.com/root-project/root/pull/18324), [#18349](https://github.com/root-project/root/pull/18349), [#18379](https://github.com/root-project/root/pull/18379), [#18399](https://github.com/root-project/root/pull/18399), [#18424](https://github.com/root-project/root/pull/18424)
