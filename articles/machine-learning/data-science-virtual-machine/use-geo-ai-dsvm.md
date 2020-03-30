---
title: Använd Geo AI
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du använder virtual machine för geo AI-datavetenskap för att analysera data och skapa modeller baserat på geospatiala data.
keywords: djupinlärning, AI, datavetenskapsverktyg, virtuell datavetenskap, geospatial analys
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278419"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Använda virtual machine för geo artificiell intelligens datavetenskap

Använd den virtuella datorn för Geo AI Data Science för att hämta data för analys, utföra datakäbbel och skapa modeller för AI-program som använder geospatial information. När du har etablerat din virtuella geo AI-datavetenskap och loggat in på ArcGIS Pro via ditt ArcGIS-konto kan du börja interagera med ArcGIS-skrivbordet och ArcGIs online. Du kan också komma åt ArcGIS från Python-gränssnitt och en R-språkbrygga som är förkonfigurerad på den virtuella datorn för geodatavetenskap. Om du vill skapa omfattande AI-program kombinerar du den virtuella datorn med Geo-Data Science med ramverken för maskininlärning och djupinlärning och andra datavetenskapsprogram som finns tillgängliga på den.  


## <a name="configuration-details"></a>Konfigurationsinformation

Python-biblioteket, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), som används för att samverka med ArcGIS, installeras i den globala ```c:\anaconda```rotkonda-miljön i den virtuella datorn för datavetenskap som finns på .

- Om du kör Python i en ```activate``` kommandotolk kör du för att aktivera i conda-roten Python-miljön.
- Om du använder en IDE- eller Jupyter-anteckningsbok kan du välja miljö eller kärna för att se till att du är i rätt conda-miljö.

R-bryggan till ArcGIS installeras som ett R-bibliotek med namnet [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) i den ```C:\Program Files\Microsoft\ML Server\R_SERVER```fristående huvudinstansen Microsoft Machine Learning Server som finns på . Visual Studio, RStudio och Jupyter är redan förkonfigurerade för att ```arcgisbinding``` använda den här R-miljön och kommer att ha tillgång till R-biblioteket. 


## <a name="geo-ai-data-science-vm-samples"></a>Geo AI Data Science VM-exempel

Förutom rambaserade exempel på maskininlärning och djupinlärning från basen Data Science VM tillhandahålls också en uppsättning geospatiala exempel som en del av den virtuella datorn för Geo AI-datavetenskap. Dessa exempel kan hjälpa dig att få fart på din utveckling av AI-program med hjälp av geospatiala data och ArcGIS-programvaran:


1. [Komma igång med geospatial analys med Python:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)Ett inledande exempel som visar hur man arbetar med geospatiala data via Python-gränssnittet till ArcGIS tillhandahålls av [arcpy-biblioteket.](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) Den visar också hur du kombinerar traditionell maskininlärning med geospatiala data och sedan visualisera resultatet på en karta i ArcGIS.

2. [Komma igång med geospatial analys med R:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)Ett inledande exempel som visar hur du arbetar med geospatiala data med hjälp av R-gränssnittet till ArcGIS som tillhandahålls av [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) biblioteket. 

3. [Klassificering på markanvändning på pixelnivå](https://github.com/Azure/pixel_level_land_classification): En självstudiekurs som illustrerar hur du skapar en djup neural nätverksmodell som accepterar en flygbild som indata och returnerar en marktäckesetikett. Exempel på marktäckesetiketter är *skogsklädda* och *vatten.* Modellen returnerar en sådan etikett för varje pixel i bilden. 


## <a name="next-steps"></a>Nästa steg

Ytterligare exempel som använder den virtuella datorn för datavetenskap finns här:

* [Prover](dsvm-samples-and-walkthroughs.md)