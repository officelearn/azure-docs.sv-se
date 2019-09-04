---
title: Använda geo AI
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du använder Geo AI Data Science Virtual Machine för att analysera data och skapa modeller som baseras på geospatiala data.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, geospatial analys
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278419"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Med hjälp av Geo AI Data Science Virtual Machine

Använd Geo AI Data Science VM för att hämta data för analys, utföra Datatransformering och skapa modeller för AI-program som förbrukar geospatial information. När du har etablerad geo AI-Data Science VM och loggat in på ArcGIS Pro genom ditt ArcGIS-konto kan du börja interagera med ArcGIS Desktop och ArcGIs online. Du kan också få åtkomst till ArcGIS från Python-gränssnitt och en R-språkbrygga som är förkonfigurerad på Geo-Data Science VM. Om du vill bygga rika AI-program kombinerar du geo-Data Science VM med ramverk för maskin inlärning och djup inlärning och annan data vetenskaps program vara som är tillgänglig på den.  


## <a name="configuration-details"></a>Konfigurationsinformation

Python-biblioteket, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), som används som gränssnitt med ArcGIS, installeras i den globala roten Conda-miljön för den data science VM som finns på ```c:\anaconda```.

- Om du kör python i en kommando tolk kör ```activate``` du för att aktivera i Conda-rotens python-miljö.
- Om du använder en IDE-eller Jupyter-anteckningsbok kan du välja miljön eller kärnan för att kontrol lera att du är i rätt Conda-miljö.

R-bryggan till ArcGIS installeras som ett R-bibliotek med namnet [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) i huvud Microsoft Machine Learning Server fristående instans som finns på ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio och Jupyter är redan förkonfigurerade för att använda den här R-miljön och har åtkomst till ```arcgisbinding``` r-biblioteket. 


## <a name="geo-ai-data-science-vm-samples"></a>GEO-AI Data Science VM-exempel

Förutom de ramverkbaserade exempel för maskin inlärning och djup inlärning från bas Data Science VM, tillhandahålls även en uppsättning geospatiala exempel som en del av Geo AI-Data Science VM. Dessa exempel kan hjälpa dig att komma igång med utveckling av AI-program genom att använda geospatiala data och ArcGIS-program varan:


1. [Komma igång med geospatial analys med python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Ett inledande exempel som visar hur du arbetar med geospatiala data via python-gränssnittet till ArcGIS finns i [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) -biblioteket. Det visar också hur du kombinerar traditionell Machine Learning med geospatiala data och sedan visualiserar resultatet på en karta i ArcGIS.

2. [Komma igång med geospatial analys med R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Ett inledande exempel som visar hur du arbetar med geospatiala data med hjälp av R-gränssnittet till ArcGIS som tillhandahålls av [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) -biblioteket. 

3. [Klassificering av mark användning på pixel nivå](https://github.com/Azure/pixel_level_land_classification): En själv studie kurs som illustrerar hur du skapar en djup neurala nätverks modell som accepterar en flyg-bild som indata och returnerar en etikett för land omslag. Exempel på etiketter för land omslag är *skogs* -och *vatten*. Modellen returnerar sådana en etikett för varje bildpunkt i avbildningen. 


## <a name="next-steps"></a>Nästa steg

Ytterligare exempel som använder den virtuella datorn för datavetenskap finns här:

* [Exempel](dsvm-samples-and-walkthroughs.md)