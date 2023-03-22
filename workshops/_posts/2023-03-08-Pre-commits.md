---
layout: post
title:  "Knowledge-sharing: Pre-commits"
date:   2023-03-08 08:00:00 +0200
tags: [presenting, software security, knowledge-sharing, github, pre-commit hooks]
---

Hva er en pre-commit og hva kan den hjelpe oss med? Dette kan hjelpe oss på mange måter, hindre secretes, validere e.g terraform, formatere koden vår, kjøre eslint som feiler på warnings.. osv. her utforsker vi mulighetene. Vi ser spesielt på standardisert kodeformatering, og hvordan vi kan konfigurere pre-commits til å feile en commit av kode før det kommer i repoet.