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
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282689"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Utvecklingsverktyg på Azure-Data Science Virtual Machine

Data Science Virtual Machine (DSVM) paketerar flera populära verktyg i ett mycket produktivt Integrated Development Environment (IDE). Här följer några verktyg som finns på DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows: Visual Studio 2017, Windows 2019: Visual Studio 2019      |
| Vanliga användnings områden      | Program varu utveckling    |
| Hur konfigureras den och installeras på DSVM?      | Arbets belastningen data vetenskap (python och R-verktyg), Azure-arbetsbelastningar (Hadoop, Data Lake), Node. js, SQL Server verktyg [Azure Machine Learning för Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Använda och köra den      | Skriv bords`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`gen väg (). Du kan grafiskt öppna Visual Studio med hjälp av Skriv bords ikonen eller **Start** -menyn. Sök efter program (Windows-tangenten + S) följt av **Visual Studio**. Därifrån kan du skapa projekt på språk som C#, python, R och Node. js.   |
| Relaterade verktyg på DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Du kan få ett meddelande om att utvärderings perioden har upphört att gälla. Ange dina Microsoft-konto autentiseringsuppgifter. Eller skapa ett nytt kostnads fritt konto för att få åtkomst till Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio-koden 

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Generell användning IDE      |
| DSVM-versioner som stöds      | Windows, Linux     |
| Vanliga användnings områden      | Kod redigerare och git-integrering   |
| Använda och köra den      | Skriv bords`C:\Program Files (x86)\Microsoft VS Code\Code.exe`gen väg () i Windows, skriv bords gen väg eller Terminal (`code`) i Linux    |
| Relaterade verktyg på DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Client IDE för R-språk   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användnings områden      |  R-utveckling     |
| Använda och köra den      | Skriv bords`C:\Program Files\RStudio\bin\rstudio.exe`gen väg () på Windows,`/usr/bin/rstudio`Skriv bords gen väg () på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Client IDE för R-språk   |
| Vad är detta?   | Webbaserad IDE för R    |
| DSVM-versioner som stöds      | Linux      |
| Vanliga användnings områden      |  R-utveckling     |
| Använda och köra den      | Aktivera tjänsten med _systemctl aktivera RStudio-Server_och starta sedan tjänsten med _systemctl start RStudio-Server_. Logga sedan in på RStudio-servern på http\/:/Your-VM-IP: 8787.       |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Client IDE för Julia-språk   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Vanliga användnings områden      |  Julias utveckling     |
| Använda och köra den      | Skriv bords`C:\JuliaPro-0.5.1.1\Juno.bat`gen väg () på Windows,`/opt/JuliaPro-VERSION/Juno`Skriv bords gen väg () på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm med

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Client IDE för python-språk    |
| DSVM-versioner som stöds      | Windows 2019, Linux      |
| Vanliga användnings områden      |  Python-utveckling     |
| Använda och köra den      | Skriv bords`C:\Program Files\tk`gen vägen () i Windows. Skriv bords`/usr/bin/pycharm`gen väg () på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
