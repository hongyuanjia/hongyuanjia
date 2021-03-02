---
date: "2021-01-21T00:00:00+08:00"
external_link: ""
image:
  caption: ""
  focal_point: ""
summary: ""
tags:
- Building Enegy Simulation
- Climate Change
- Software Development
- EnergyPlus
- R
title: "epluspar: Conduct parametric analysis on EnergyPlus models in R"
url_code: "https://github.com/hongyuanjia/epluspar"
url_pdf: ""
url_slides: ""
url_video: ""
---

[epluspar](https://hongyuanjia.github.io/epluspar/) is a free, open-source
[R](https://www.r-project.org/) package for conducting parametric analyses on
[EnergyPlus](https://energyplus.net/) models. It is an extension of the
[eplusr](/en/project/eplusr) package.
epluspar provides new classes for conducting [sensitivity
analysis](https://hongyuanjia.github.io/epluspar/reference/SensitivityJob.html)
using the Morris method,
[optimization](https://hongyuanjia.github.io/epluspar/reference/GAOptimJob.html)
using Genetic Algorithm and
[calibration](https://hongyuanjia.github.io/epluspar/reference/BayesCalibJob.html)
using Bayesian Theory.
All the new classes introduced are based on the
[`ParametricJob`](https://hongyuanjia.github.io/eplusr/articles/param.html)
class in the eplusr package.
The main difference mainly lies in the specific statistical method used for
sampling parameter values when calling
[`apply_measure()`](https://hongyuanjia.github.io/eplusr/articles/param.html#specify-design-alternatives-1)
method.
Few examples of the epluspar package have been shown in this
[paper](/en/publication/2020-eplusr).

## Additional resources

* epluspar online manual: https://hongyuanjia.github.io/epluspar/
