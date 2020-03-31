---
title: Utvecklingsverktyg
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om de verktyg och integrerade utvecklingsmiljöer som finns på Virtual Machine för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282689"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Utvecklingsverktyg på Virtual Machine för Azure Data Science

Data Science Virtual Machine (DSVM) innehåller flera populära verktyg i en mycket produktiv integrerad utvecklingsmiljö (IDE). Här är några verktyg som finns på DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Allmänt ändamål IDE      |
| DSVM-versioner som stöds      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Typiska användningsområden      | Utveckling av programvara    |
| Hur är det konfigurerat och installerat på DSVM?      | Data Science Workload (Python och R-verktyg), Azure-arbetsbelastning (Hadoop, DataSjö), Node.js, SQL Server-verktyg, [Azure Machine Learning för Visual Studio-kod](https://github.com/Microsoft/vs-tools-for-ai)    |
| Så här använder och kör du den      | Genväg på`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`skrivbordet ( ). Öppna Visual Studio grafiskt med hjälp av skrivbordsikonen eller **Start-menyn.** Sök efter program (Windows-tangenten+S), följt av **Visual Studio**. Därifrån kan du skapa projekt på språk som C#, Python, R och Node.js.   |
| Relaterade verktyg på DSVM      |     Visual Studio-kod, RStudio, Juno  |

> [!NOTE]
> Du kan få ett meddelande om att utvärderingsperioden har gått ut. Ange dina Microsoft-kontouppgifter. Eller skapa ett nytt kostnadsfritt konto för att få tillgång till Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio-koden 

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Allmänt ändamål IDE      |
| DSVM-versioner som stöds      | Windows, Linux     |
| Typiska användningsområden      | Kodredigerare och Git-integrering   |
| Så här använder och kör du den      | Genväg på`C:\Program Files (x86)\Microsoft VS Code\Code.exe`skrivbordet ( ) i`code`Windows, genväg på skrivbordet eller terminal ( ) i Linux    |
| Relaterade verktyg på DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Klient-IDE för R-språk   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Typiska användningsområden      |  R utveckling     |
| Så här använder och kör du den      | Genväg till`C:\Program Files\RStudio\bin\rstudio.exe`skrivbordet ( )`/usr/bin/rstudio`på Windows, genväg på skrivbordet ( ) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio-kod, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Klient-IDE för R-språk   |
| Vad är detta?   | Webbaserad IDE för R    |
| DSVM-versioner som stöds      | Linux      |
| Typiska användningsområden      |  R utveckling     |
| Så här använder och kör du den      | Aktivera tjänsten med _systemctl aktivera rstudio-server_och starta sedan tjänsten med _systemctl start rstudio-server_. Logga sedan in på RStudio\/Server på http: /your-vm-ip:8787.       |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio-kod, RStudio-skrivbord      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Klient-IDE för Julia   |
| DSVM-versioner som stöds      | Windows, Linux      |
| Typiska användningsområden      |  Julia utveckling     |
| Så här använder och kör du den      | Genväg till`C:\JuliaPro-0.5.1.1\Juno.bat`skrivbordet ( )`/opt/JuliaPro-VERSION/Juno`på Windows, genväg på skrivbordet ( ) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio-kod, RStudio      |

## <a name="pycharm"></a>Pycharm (pycharm)

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Klient-IDE för Python-språk    |
| DSVM-versioner som stöds      | Windows 2019, Linux      |
| Typiska användningsområden      |  Python utveckling     |
| Så här använder och kör du den      | Genväg till`C:\Program Files\tk`skrivbordet ( ) i Windows. Genväg till`/usr/bin/pycharm`skrivbordet ( ) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio, Visual Studio-kod, RStudio      |
