---
title: Med hjälp av Geo AI Data Science Virtual Machine – Azure | Microsoft Docs
description: Lär dig hur du använder Geo AI Data Science Virtual Machine för att analysera data och skapa modeller som baseras på geospatiala data.
keywords: djupinlärning, AI, verktyg för datavetenskap, virtuell dator för datavetenskap, Geospatial analys
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 6e6737e928ece820ea9119d8dfe1d7cf22477646
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880117"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Med hjälp av Geo AI Data Science Virtual Machine

Använd Geo AI Data Science VM för att hämta data för analys, utföra Datatransformering och skapa modeller för AI-program som förbrukar geospatial information. När du har etablerat din Geo AI Data Science VM och loggat in på ArcGIS Pro med ArcGIS-konto kan börja du interagera med ArcGIS desktop och ArcGis online. Du kan även använda ArcGIS från Python-gränssnitt och ett bridge för R-språket som redan har konfigurerats på Geo-Data Science VM. Om du vill skapa funktionsrika AI-program måste du kombinera det med machine learning och deep learning-ramverk och andra data science programvara som finns på den virtuella datorn för datavetenskap.  


## <a name="configuration-details"></a>Konfigurationsinformation

Python-bibliotek [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), som används för att gränssnittet med ArcGIS är installerad i global rot conda-miljö av den virtuella datorn för datavetenskap som finns på ```c:\anaconda```. 

- Om du använder Python i en kommandotolk, kör ```activate``` att aktivera till conda rot Python-miljö. 
- Om du använder en IDE- eller Jupyter notebook kan välja du miljön eller kerneln för att kontrollera att du har rätt conda-miljö. 

R-brygga och ArcGIS installeras som ett R-bibliotek med namnet [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) i den huvudsakliga Microsoft R server fristående instans finns på ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio och Jupyter har förkonfigurerats att använda den här R-miljön och har åtkomst till den ```arcgisbinding``` R-biblioteket. 


## <a name="geo-ai-data-science-vm-samples"></a>GEO-AI Data Science VM-exempel

Förutom ML och djupinlärning framework-baserade exempel från grundläggande Data Science VM finns också en uppsättning geospatiala exempel som en del av Geo AI Data Science VM. Med hjälp av dessa exempel kan du ge din utvecklingen av AI-program med hjälp av geospatiala data och ArcGIS-programvara. 


1. [Hämta anges med Geospatial analys med Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Ett grundläggande exempel som visar hur du arbetar med geospatiala data med hjälp av Python-gränssnitt för ArcGIS som tillhandahålls av den [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) biblioteket. Den visar även hur du kan kombinera traditionella machine learning med geospatiala data och visualisera resultat på en karta i ArcGIS. 

2. [Hämta anges med Geospatial analys med R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Ett grundläggande exempel som visar hur du arbetar med geospatiala data med hjälp av R-gränssnitt för ArcGIS som tillhandahålls av den [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) biblioteket. 

3. [Bildpunkter mark använda klassificering](https://github.com/Azure/pixel_level_land_classification): En självstudiekurs som illustrerar hur du skapar en modell för djupa neurala nätverk som tar emot en Flygfoto som indata och returnerar en mark-cover-etikett. Exempel på mark-cover etiketter är ”forested” eller ”water”. Modellen returnerar sådana en etikett för varje bildpunkt i avbildningen. Modellen har skapats med öppen källkod för Microsofts [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) ramverk för djupinlärning. 


## <a name="next-steps"></a>Nästa steg

Ytterligare exempel som använder den virtuella datorn för datavetenskap finns här:

* [Exempel](dsvm-samples-and-walkthroughs.md)

