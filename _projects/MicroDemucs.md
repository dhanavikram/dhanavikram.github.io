---
layout: page
title: Micro Demucs
description: Music-Separation-as-a-service (MSaaS). Micro demucs is a microservice backend for Music Separation as a services that uses Facebook's demucs model.
img: https://vocalremover.org/img/splitter/player_en.png
redirect: https://github.com/dhanavikram/Micro-Demucs
importance: 2
---

Micro demucs is a microservice backend for Music Separation as a services that uses Facebook's [demucs](https://github.com/facebookresearch/demucs) model. Hence the name. It is essentially a collection of  kubernetes clusters that provides a REST API frontend for automatic music separation service and prepares the different tracks for retrieval. The reason for turning this into a micro-service is because it takes a long time to run the source separation (about 3-4x the running time of a song).

For implementation, code and more details, visit [here]().