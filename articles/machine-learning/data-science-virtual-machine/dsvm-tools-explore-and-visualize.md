---
title: Verktyg för data utforskning och visualisering – Azure | Microsoft Docs
description: Data utforsknings-och visualiserings verktyg för Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 31b05ec4fa68c3d4804000caee94b62432bdaed9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557758"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Verktyg för data utforskning och visualisering på Data Science Virtual Machine

Ett viktigt steg i data vetenskap är att förstå data. Visualiserings-och data utforsknings verktyg hjälper till att påskynda data förståelsen. Här följer några verktyg på DSVM som gör det här viktiga steget enklare. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL-frågemotor med öppen källkod på Big data    |
| Stöds DSVM-versioner      | Windows, Linux  |
| Hur är det konfigurerade / installerad på DSVM?      |  Endast installerat `/dsvm/tools/drill*` i inbäddat läge   |
| Vanliga användningsområden      |  In-situ-data utforskning utan att kräva ETL. Fråga olika data källor och format, inklusive CSV, JSON, Relations tabeller, Hadoop     |
| Hur du använder / köra den?      | Skriv bords gen väg  <br/> [Kom igång med att detaljgranska på 10 minuter](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Relaterade verktyg på DSVM      |   Rattle, Wekas, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  Wekas är en samling Machine Learning-algoritmer för data utvinnings aktiviteter. Algoritmerna kan antingen tillämpas direkt på en data uppsättning eller anropas från din egen Java-kod. Weka innehåller verktyg för förbearbetning av data, klassificering, regression, klustring, association regler och visualisering. |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Verktyg för allmän ML     |
| Hur du använder / köra den?      | I Windows söker du efter Wekas på Start menyn. Logga in med X2Go i Linux och gå sedan till program-> Development-> Wekas. |
| Innehåller länkar till exempel      | [Weka-exempel](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg på DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användar gränssnitt för Data utvinning med R   |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänt GRÄNSSNITTs data utvinnings verktyg för R    |
| Hur du använder / köra den?      | UI-verktyget. Starta en kommando tolk i Windows, kör R och sedan i R-körning `rattle()`. I Linux ansluter du med X2Go, startar en Terminal, kör R och sedan i R- `rattle()`körning. |
| Innehåller länkar till exempel      | [Spännen](https://togaware.com/onepager/) |
| Relaterade verktyg på DSVM      |LightGBM, Wekas, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiv datavisualisering och BI-verktyg    |
| Stöds DSVM-versioner      | Windows  |
| Vanliga användningsområden      |  Datavisualisering och skapa instrumentpaneler   |
| Hur du använder / köra den?      | Genväg på skrivbordet (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

