---
title: Utvecklingsverktyg
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de verktyg och integrerade utvecklings miljöer som är tillgängliga på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390615"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Utvecklingsverktyg på Azure-Data Science Virtual Machine

Data Science Virtual Machine (DSVM) paketerar flera populära verktyg i ett mycket produktivt Integrated Development Environment (IDE). Här är några verktyg som finns på DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows: Visual Studio 2017, Windows 2019 (för hands version): Visual Studio 2019      |
| Vanliga användningsområden      | Program varu utveckling    |
| Hur konfigureras den och installeras på DSVM?      | Arbets belastningen data vetenskap (python och R-verktyg), Azure-arbetsbelastningar (Hadoop, Data Lake), Node. js, SQL Server verktyg [Azure Machine Learning för Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Du kan grafiskt öppna Visual Studio med hjälp av Skriv bords ikonen eller **Start** -menyn. Sök efter program (Windows-tangenten + S) följt av **Visual Studio**. Därifrån kan du skapa projekt på språk som C#, python, R och Node. js.   |
| Relaterade verktyg på DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Du kan få ett meddelande om att utvärderingsperioden har upphört att gälla. Ange autentiseringsuppgifterna för ditt Microsoft-konto. Eller skapa ett nytt kostnadsfritt konto för att få åtkomst till Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows, Linux     |
| Vanliga användningsområden      | Kodredigeraren och Git-integrering   |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) i Windows, skriv bords gen väg eller Terminal (`code`) i Linux    |
| Relaterade verktyg på DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för R-språk   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files\RStudio\bin\rstudio.exe`) på Windows, skriv bords gen väg (`/usr/bin/rstudio`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio-Server

|    |           |
| ------------- | ------------- |
| Vad är det?   | Client IDE för R-språk   |
| Vad är det?   | Webbaserad IDE för R    |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Använda och köra den      | Aktivera tjänsten med _systemctl aktivera RStudio-Server_och starta sedan tjänsten med _systemctl start RStudio-Server_. Logga sedan in på RStudio-servern på http:\//Your-VM-IP: 8787.       |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Julia-språket   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användningsområden      |  Julia-utveckling     |
| Använda och köra den      | Skriv bords gen väg (`C:\JuliaPro-0.5.1.1\Juno.bat`) på Windows, skriv bords gen väg (`/opt/JuliaPro-VERSION/Juno`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Python-språk    |
| DSVM-versioner som stöds      | Windows 2019 (för hands version), Linux      |
| Vanliga användningsområden      |  Python-utveckling     |
| Använda och köra den      | Skriv bords gen väg (`C:\Program Files\tk`) i Windows. Skriv bords gen väg (`/usr/bin/pycharm`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
