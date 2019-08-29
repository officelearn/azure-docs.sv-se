---
title: Utvecklingsverktyg för data Science Virtual Machine – Azure | Microsoft Docs
description: Lär dig mer om de verktyg och integrerade utvecklings miljöer som är förinstallerade på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074305"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Utvecklingsverktyg på den virtuella datorn för datavetenskap

Data Science Virtual Machine (DSVM) paketerar flera populära verktyg i ett mycket produktivt Integrated Development Environment (IDE). Här är några verktyg som finns på DSVM.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användningsområden      | Program varu utveckling    |
| Hur konfigureras den och installeras på DSVM?      | Data Science arbetsbelastning (Python- och R-verktyg), Azure-arbetsbelastningen (Hadoop, Data Lake), Node.js, SQL Server-verktyg, [Azure Machine Learning för Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Använda och köra den      | Skriv bords`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`gen väg ()    |
| Relaterade verktyg på DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio-koden 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows, Linux     |
| Vanliga användningsområden      | Kodredigeraren och Git-integrering   |
| Använda och köra den      | Skriv bords`C:\Program Files (x86)\Microsoft VS Code\Code.exe`gen väg () i Windows, skriv bords gen väg eller Terminal (`code`) i Linux    |
| Relaterade verktyg på DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för R-språk   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Använda och köra den      | Skriv bords`C:\Program Files\RStudio\bin\rstudio.exe`gen väg () på Windows,`/usr/bin/rstudio`Skriv bords gen väg () på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för R-språk   |
| Vad är det?   | Webbaserad IDE för R    |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Använda och köra den      | Aktivera tjänsten med _systemctl aktivera RStudio-Server_och starta sedan tjänsten med _systemctl start RStudio-Server_. Logga sedan in på RStudio-servern på http\/:/Your-VM-IP: 8787.       |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Julia-språket   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användningsområden      |  Julia-utveckling     |
| Använda och köra den      | Skriv bords`C:\JuliaPro-0.5.1.1\Juno.bat`gen väg () på Windows,`/opt/JuliaPro-VERSION/Juno`Skriv bords gen väg () på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Python-språk    |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användningsområden      |  Python-utveckling     |
| Använda och köra den      | Skriv bords`/usr/bin/pycharm`gen väg () på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiva data visualiseringar och BI-verktyg    |
| DSVM-versioner som stöds      | Windows  |
| Vanliga användningsområden      |  Data visualisering och skapa instrument paneler   |
| Använda och köra den      | Skriv bords`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`gen väg ()      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

