---
title: Data från kartläggning av naturresurser och visuella verktyg - Azure | Microsoft Docs
description: Data från kartläggning av naturresurser och visualisering verktyg för datavetenskap virtuell dator.
keywords: datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: a89c51ae5b4b0c9d58aed04515042ed8b08dcce6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Data undersökning och visuella verktyg på datavetenskap virtuell dator

Ett viktigt steg i datavetenskap är att förstå informationen. Visualisering och data från kartläggning av naturresurser Verktyg att påskynda data förstå. Här tillhandahålls vissa verktyg på DSVM att underlätta detta viktigt steg. 

## <a name="apache-drill"></a>Apache ökad
|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL-frågemotor för öppen källkod på stordata    |
| Stöds DSVM versioner      | Windows, Linux  |
| Hur är det konfigurerade / installerad på DSVM?      |  Installerad i `/dsvm/tools/drill*` i inbäddade läge   |
| Vanliga användningsområden      |  I situ datagranskning utan ETL. Fråga olika källor och format includign CSV, JSON, tabeller, Hadoop     |
| Hur du använder / kör den?      | Genväg på skrivbordet  <br/> [Kom igång med ökad 10 minuter](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Relaterade verktyg på DSVM      |   Spännen, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  Weka är en samling av maskininlärningsalgoritmer för uppgifter som rör utvinningsmodellen. Algoritmer som du kan antingen tillämpas direkt på en datamängd eller anropas från din egen Java-kod. Weka innehåller verktyg för före databearbetning, klassificering, regression, kluster, association regler och visualisering. |
| Stöds DSVM versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna ML-verktyget     |
| Hur du använder / kör den?      | På Windows, söka efter Weka i Start-menyn. Logga in med X2Go på Linux, och sedan gå till program -> Development Weka ->. |
| Länkar till exempel      | [Weka-exempel](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg på DSVM      |LightGBM spännen, Xgboost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användargränssnitt för datautvinning med R   |
| Stöds DSVM versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna UI Data Mining-verktyget för R    |
| Hur du använder / kör den?      | UI-verktyget. Starta en kommandotolk i Windows, kan köra R, sedan i R kör `rattle()`. På Linux, ansluta med X2Go, starta en terminal, kör R, sedan i R kör `rattle()`. |
| Länkar till exempel      | [Rattle](https://togaware.com/onepager/) |
| Relaterade verktyg på DSVM      |LightGBM Weka, Xgboost   |

## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiv datavisualisering och BI-verktyg    |
| Stöds DSVM versioner      | Windows  |
| Vanliga användningsområden      |  Visualisering av data och skapa instrumentpaneler   |
| Hur du använder / kör den?      | Genväg på skrivbordet (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

