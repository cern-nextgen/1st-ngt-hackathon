---
title: MadGraph Event Generator on GPUs
layout: main
section: eventgenerators 
---

## Resources
- [MadGraph Github repository](https://github.com/mg5amcnlo/mg5amcnlo)
- [`cudacpp` plugin GitHub repository](https://github.com/madgraph5/madgraph4gpu)

## Plan for the hackaton
- [MadGraph introduction](./madgraph_introduction.html);
- [Getting familiar with MadGraph](./getting_familiar.html):
    - how to install;
    - first event generation;
    - change the configuration;
    - install and link additional tools (e.g. Pythia);
    - AOB and any suggestions welcome.
- [The `cudacpp` plugin](./the_cudacpp_plugin.html), a.k.a. running MadGraph on GPU/vectorized CPU:
    - the MadGraph plugin system and how to install;
    - details on hardware acceleration in MadGraph;
    - perform a MadGraph run using `cudacpp` plugin;
    - various possible settings.
- [Finding bottlenecks in the code](./profiling.html):
    - profile MadGraph;
    - using [Adaptyst](https://adaptyst.web.cern.ch/) to obtain flamegraphs;
    - discuss future updates.

## References
- Alwall, J., Frederix, R., Frixione, S., Hirschi, V., Maltoni, F., Mattelaer, O., Shao, H. S., Stelzer, T., Torrielli, P., Zaro, M. [The automated computation of tree-level and next-to-leading order differential cross sections, and their matching to parton shower simulations](https://inspirehep.net/literature/1293923). arXiv:[1405.0301](https://arxiv.org/abs/1405.0301). DOI: [10.1007/JHEP07(2014)079](https://doi.org/10.1007/JHEP07(2014)079). Published in: JHEP 07 (2014), 079.
- Alwall, J., Herquet, M., Maltoni, F., Mattelaer, O., Stelzer, T. [MadGraph 5: Going Beyond](https://inspirehep.net/literature/912611). arXiv:[1106.0522](https://arxiv.org/abs/1106.0522). DOI: [10.1007/JHEP06(2011)128](https://doi.org/10.1007/JHEP06(2011)128). Published in: JHEP 06 (2011), 128.
- Valassi, A., Childers, T., Hageboeck, S., Massaro, D., Mattelaer, O., Nichols, N., Optolowicz, F., Roiser, S., Teig, J., Wettersten, Z. [Madgraph on GPUs and vector CPUs: towards production (The 5-year journey to the first LO release CUDACPP v1.00.00)](https://inspirehep.net/literature/2905710). arXiv:[2503.21935](https://arxiv.org/abs/2503.21935). Contribution to CHEP 2024.

