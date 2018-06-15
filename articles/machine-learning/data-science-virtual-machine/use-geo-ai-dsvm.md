---
title: Med Geo artificiell Intelligence datavetenskap Virtual Machine - Azure | Microsoft Docs
description: Hur du använder en Geo AI virtuell dator på Azure.
keywords: Djup learning AI datavetenskap verktyg, datavetenskap virtuell dator, geospatiala analytics
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: f346b086a0269f247d64edf9346b01849ba3d0ee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31408045"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Använda Geo artificiell Intelligence datavetenskap virtuell dator

Använd Geo AI datavetenskap VM för att hämta data för analys, utföra wrangling data och skapa modeller för AI-program som använder geospatial information. När du har etablerat din Geo AI datavetenskap VM och inloggad ArcGIS Pro med ArcGIS-konto, kan du starta interagerar med ArcGIS desktop och ArcGis online. Du kan också komma åt ArcGIS från Python gränssnitt och ett R språk bridge förkonfigurerade på den virtuella datorn vetenskap Geo-Data. För att bygga rika AI-program kan du kombinera det med datorn inlärning och djupt learning ramverk och andra data vetenskaplig programvara som finns på den virtuella datorn vetenskapliga Data.  


## <a name="configuration-details"></a>Konfigurationsinformation

Python-bibliotek [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), som används för att gränssnittet med ArcGIS är installerat i globala rot conda miljö av den virtuella datorn vetenskapliga Data som finns på ```c:\anaconda```. 

- Om du använder Python i en kommandotolk, köra ```activate``` att aktivera i conda rot Python-miljön. 
- Om du använder en IDE- eller Jupyter-anteckningsbok, kan du välja miljö eller kernel så du är i rätt conda-miljö. 

R-bryggan till ArcGIS installeras som en R-biblioteket med namnet [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) i huvudsakliga Microsoft R fristående serverinstansen finns på ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio och Jupyter redan är förkonfigurerad att använda den här R-miljön och har åtkomst till den ```arcgisbinding``` R-biblioteket. 


## <a name="geo-ai-data-science-vm-samples"></a>GEO AI datavetenskap VM-exempel

Förutom ML och djup learning framework-baserade grundläggande datavetenskap VM-exempel finns också en uppsättning geospatiala exempel som en del av Geo AI datavetenskap VM. De här exemplen kan hjälpa dig aldrig utvecklingen av AI-program som använder geospatiala data och ArcGIS programvara. 


1. [Hämta anges med Geospatial analytics med Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ett grundläggande exempel som visar hur du arbetar med geospatiala data med hjälp av gränssnittet Python till ArcGIS som tillhandahålls av den [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) bibliotek. Den visar även hur du kan kombinera traditionella maskininlärning med geospatiala data och visualisera resultat på en karta i ArcGIS. 

2. [Hämta anges med Geospatial analytics med R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ett grundläggande exempel som visar hur du arbetar med geospatiala data med hjälp av R-gränssnitt för ArcGIS som tillhandahålls av den [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) bibliotek. 

3. [Bildpunkter mark använda klassificering](https://github.com/Azure/pixel_level_land_classification): en självstudiekurs som illustrerar hur du skapar en modell för djupa neurala nätverket som accepterar en Flygfoto avbildning som indata och returnerar en mark omfattar etikett. Exempel på mark omfattar etiketter är ”forested” eller ”vattenstämplar”. Modellen returnerar sådana en etikett för varje pixel i bilden. Modellen skapas med Microsofts öppen källkod [kognitiva Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) djup learning framework. Exemplet visar även hur du skalar upp utbildning om [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) och använda modellen förutsägelser ArcGIS Pro-programvaran. 


## <a name="next-steps"></a>Nästa steg

Ytterligare exempel som använder datavetenskap VM finns här:

* [Exempel](dsvm-samples-and-walkthroughs.md)

