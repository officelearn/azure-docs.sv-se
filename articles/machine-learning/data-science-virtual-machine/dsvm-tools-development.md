---
title: Utvecklingsverktyg
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de verktyg och integrerade utvecklings miljöer som är tillgängliga på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 76a550e95de24bf65b9b6097dd332e535da5b1c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330720"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Utvecklingsverktyg på Azure-Data Science Virtual Machine

Data Science Virtual Machine (DSVM) paketerar flera populära verktyg i ett mycket produktivt Integrated Development Environment (IDE). Här följer några verktyg som finns på DSVM.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows      |
| Vanliga användnings områden      | Program varu utveckling    |
| Hur konfigureras den och installeras på DSVM?      | Arbets belastningen data vetenskap (python och R-verktyg), Azure-arbetsbelastningar (Hadoop, Data Lake), Node. js, SQL Server verktyg [Azure Machine Learning för Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`). Du kan grafiskt öppna Visual Studio med hjälp av Skriv bords ikonen eller **Start** -menyn. Sök efter program (Windows-tangenten + S) följt av **Visual Studio**. Därifrån kan du skapa projekt på språk som C#, python, R och Node. js.   |
| Relaterade verktyg på DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Du kan få ett meddelande om att utvärderings perioden har upphört att gälla. Ange dina Microsoft-konto autentiseringsuppgifter. Eller skapa ett nytt kostnads fritt konto för att få åtkomst till Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio-kod 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows, Linux     |
| Vanliga användnings områden      | Kod redigerare och git-integrering   |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) i Windows, skriv bords gen väg eller Terminal (`code`) i Linux    |
| Relaterade verktyg på DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för R-språk   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användnings områden      |  R-utveckling     |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files\RStudio\bin\rstudio.exe`) i Windows, skriv bords gen väg (`/usr/bin/rstudio`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio-Server 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för R-språk   |
| Vad är det?   | Webbaserad IDE för R    |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användnings områden      |  R-utveckling     |
| Använda och köra den      | Aktivera tjänsten med _systemctl aktivera RStudio-Server_och starta sedan tjänsten med _systemctl start RStudio-Server_. Logga sedan in på RStudio-servern på http: \//Your-VM-IP: 8787.       |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för Julia-språk   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användnings områden      |  Julias utveckling     |
| Använda och köra den      | Skriv bords gen väg (`C:\JuliaPro-0.5.1.1\Juno.bat`) i Windows, skriv bords gen väg (`/opt/JuliaPro-VERSION/Juno`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm med

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för python-språk    |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användnings områden      |  Python-utveckling     |
| Använda och köra den      | Skriv bords gen väg (`/usr/bin/pycharm`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiva data visualiseringar och BI-verktyg    |
| DSVM-versioner som stöds      | Windows  |
| Vanliga användnings områden      |  Data visualisering och skapa instrument paneler   |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

