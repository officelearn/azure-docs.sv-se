---
title: Verktyg för utforskning och visualisering av data
titleSuffix: Azure Data Science Virtual Machine
description: Data utforsknings-och visualiserings verktyg för Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 14c34a13440d50fcf42c2207f9933dc1de014a14
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191976"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Verktyg för data utforskning och visualisering på Azure Data Science Virtual Machine

I data vetenskap är nyckeln till att förstå data. Visualiserings-och data utforsknings verktyg hjälper till att påskynda data förståelsen. Följande verktyg, som finns på Data Science Virtual Machine (DSVM), gör det här viktiga steget enklare.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL-frågemotor med öppen källkod på Big data    |
| DSVM-versioner som stöds      | Windows, Linux  |
| Hur konfigureras den och installeras på DSVM?      |  Endast installerat `/dsvm/tools/drill*` i inbäddat läge   |
| Vanliga användningsområden      |  För data utforskning på plats utan att behöva extrahera, transformera, läsa in (ETL). Fråga olika data källor och format, inklusive CSV, JSON, Relations tabeller och Hadoop.     |
| Använda och köra den      | Skriv bords gen väg  <br/> [Kom igång med att detaljgranska på 10 minuter](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Relaterade verktyg på DSVM      |   Rattle, Wekas, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  En samling Machine Learning-algoritmer för data utvinnings aktiviteter. Dessa algoritmer kan antingen tillämpas direkt på en data uppsättning eller anropas från din egen Java-kod. Wekas innehåller verktyg för data förbehandling, klassificering, regression, klustring, associerings regler och visualisering. |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänt verktyg för maskin inlärning     |
| Använda och köra den      | På Windows, Sök efter Weka på Start-menyn. Logga in med X2Go i Linux och gå sedan till program > utveckling > Wekas. |
| Innehåller länkar till exempel      | [Weka-exempel](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg på DSVM      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användar gränssnitt (GUI) för Data utvinning med R   |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänt GRÄNSSNITTs data utvinnings verktyg för R    |
| Använda och köra den      | UI-verktyget. Öppna en kommando tolk i Windows, kör R och sedan i R, kör `rattle()`. I Linux ansluter du till X2Go, startar en Terminal, kör R och sedan i R, kör `rattle()`. |
| Innehåller länkar till exempel      | [Spännen](https://togaware.com/onepager/) |
| Relaterade verktyg på DSVM      |LightGBM, Wekas, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiva data visualiseringar och BI-verktyg    |
| DSVM-versioner som stöds      | Windows  |
| Vanliga användningsområden      |  Data visualisering och skapa instrument paneler   |
| Använda och köra den      | Skriv bords`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`gen väg ()      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

