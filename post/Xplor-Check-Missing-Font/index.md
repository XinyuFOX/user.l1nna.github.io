+++
title = "IEEE Xplore PDF Check - Missing Fonts"
date = "2019-4-7"
description = "Embedding fonts using preflight."
tags = ["Writing", "Xplore"]
author = "Steven"
image = "https://picsum.photos/1024/400/?image=1073"
+++

We got a notification last week that our recent paper did not pass the IEEE Xplore compliance check. After some investigations, we found that our paper is missing several system-wise fonts. They are not included in the PDF file. The cause is obvious because we directly include pre-generated PDF figures in the paper. Those figures are generated using R-Studio and Microsoft Visio. We didn't specify the font embedding option when saving it.

Generating the figures again is difficult and cannot guarantee the same layout, size, and consistency, since we edit the title etc. directly on the PDF figures. Therefore we need a solution to embed fonts without re-generating the figures. One approach is to use the Acrobat PDF printer, it can embed all the fonts again. However, it distorts some of the layouts and background pattern. After trying different options for the whole morning, it still does not work as we expected. Then we seek for an alternative way to minimally modify the PDF figures. We use the `preflight` tool in Acrobat reader. Here are the necessary steps: 

* Open Adobe Acrobat.
* Ctrl+D open property panel.
* Select the Font tab to check any fonts that are not embedded.
* Open the tool panel, and search for print.
* Open the `preflight` tool.
* Lookup and select PDF fixups -> embed missing fonts.
* Click `analyze`, and save the new file.

Simple! But it takes me the whole morning to figure out! The great advantage is that it does minimal update to the original PDF, unlike other re-rendering approaches. 

Ref:
[IEEE Xplore Compliance Online Verification](https://www.ieee.org/publications/authors/pdf_checker.html)

