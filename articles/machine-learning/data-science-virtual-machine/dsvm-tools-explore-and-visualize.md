---
title: Verktyg för utforskning och visualisering av data
titleSuffix: Azure Data Science Virtual Machine
description: Data utforsknings-och visualiserings verktyg för Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: a18c374df0cad7994af855089f1096d52fe30ce2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330710"
---
# <a name="data-exploration-and-visualization-tools-on-the-azure-data-science-virtual-machine"></a>Verktyg för data utforskning och visualisering på Azure Data Science Virtual Machine

I data vetenskap är nyckeln till att förstå data. Visualiserings-och data utforsknings verktyg hjälper till att påskynda data förståelsen. Följande verktyg, som finns på Data Science Virtual Machine (DSVM), gör det här viktiga steget enklare.

## <a name="apache-drill"></a>Apache-granskning
|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL-frågemotor med öppen källkod på Big data    |
| DSVM-versioner som stöds      | Windows, Linux  |
| Hur konfigureras den och installeras på DSVM?      |  Installeras i `/dsvm/tools/drill*` endast i inbäddat läge   |
| Vanliga användnings områden      |  För data utforskning på plats utan att behöva extrahera, transformera, läsa in (ETL). Fråga olika data källor och format, inklusive CSV, JSON, Relations tabeller och Hadoop.     |
| Använda och köra den      | Skriv bords gen väg  <br/> [Kom igång med att detaljgranska på 10 minuter](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Relaterade verktyg på DSVM      |   Rattle, Wekas, SQL Server Management Studio      |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiva data visualiseringar och BI-verktyg    |
| DSVM-versioner som stöds      | Windows  |
| Vanliga användnings områden      |  Data visualisering och skapa instrument paneler   |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`) eller kör bara från **Start** -menyn.      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

> [!NOTE]
> Du behöver ett Microsoft Office 365-konto för att få åtkomst till Power BI.


## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användar gränssnitt (GUI) för Data utvinning med R   |
| DSVM-versioner som stöds     | Windows, Linux     |
| Vanliga användnings områden      | Allmänt GRÄNSSNITTs data utvinnings verktyg för R    |
| Använda och köra den      | UI-verktyget. Öppna en kommando tolk i Windows, kör R och sedan i R, kör `rattle()`. I Linux ansluter du till X2Go, startar en Terminal, kör R och sedan i R, kör `rattle()`. |
| Länkar till exempel      | [Rattle](https://togaware.com/onepager/) |
| Relaterade verktyg på DSVM      |LightGBM, Wekas, Xgboost   |


## <a name="weka"></a>Wekas
|    |           |
| ------------- | ------------- |
| Vad är det?   |  En samling Machine Learning-algoritmer för data utvinnings aktiviteter. Dessa algoritmer kan antingen tillämpas direkt på en data uppsättning eller anropas från din egen Java-kod. Wekas innehåller verktyg för data förbehandling, klassificering, regression, klustring, associerings regler och visualisering. |
| DSVM-versioner som stöds     | Windows, Linux     |
| Vanliga användnings områden      | Allmänt verktyg för maskin inlärning     |
| Använda och köra den      | I Windows söker du efter Wekas på Start menyn. Logga in med X2Go i Linux och gå sedan till program > utveckling > Wekas. |
| Länkar till exempel      | [Wekas-exempel](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg på DSVM      |LightGBM, Rattle, Xgboost   |




