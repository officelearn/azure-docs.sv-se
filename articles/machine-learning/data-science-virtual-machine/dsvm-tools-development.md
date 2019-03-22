---
title: Utvecklingsverktyg för data Science Virtual Machine – Azure | Microsoft Docs
description: Läs mer om de verktyg och integrated development environment som har förinstallerats på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
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
ms.author: gokuma
ms.openlocfilehash: 7983169c2b1123c57a48471e3f4d9ad6f19c84dc
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314730"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Utvecklingsverktyg på den virtuella datorn för datavetenskap

Den virtuella datorn på datavetenskap (DSVM) tillhandahåller en produktiv miljö för utveckling genom att paketera flera populära verktyg och IDE. Här är några verktyg som finns på DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| Stöds DSVM-versioner      | Windows      |
| Vanliga användningsområden      | Programutveckling    |
| Hur är det konfigurerade / installerad på DSVM?      | Data Science arbetsbelastning (Python- och R-verktyg), Azure-arbetsbelastningen (Hadoop, Data Lake), Node.js, SQL Server-verktyg, [Azure Machine Learning för Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hur du använder / köra den?      | Genväg på skrivbordet (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Relaterade verktyg på DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio-koden 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Generell användning IDE      |
| Stöds DSVM-versioner      | Windows, Linux     |
| Vanliga användningsområden      | Kodredigeraren och Git-integrering   |
| Hur du använder / köra den?      | Genväg på skrivbordet (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) i Windows, genvägen på skrivbordet eller terminal (`code`) i Linux    |
| Relaterade verktyg på DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för R    |
| Stöds DSVM-versioner      | Windows, Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Hur du använder / köra den?      | Genväg på skrivbordet (`C:\Program Files\RStudio\bin\rstudio.exe`) på Windows, genvägen på skrivbordet (`/usr/bin/rstudio`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Webbaserad IDE för R    |
| Stöds DSVM-versioner      | Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Hur du använder / köra den?      | Aktivera tjänsten med _systemctl aktivera rstudio server_, starta tjänsten med _systemctl starta rstudio server_. Du kan sedan logga in till RStudio Server http-:\// din-vm-ip:8787.       |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Julia-språket   |
| Stöds DSVM-versioner      | Windows, Linux      |
| Vanliga användningsområden      |  Julia-utveckling     |
| Hur du använder / köra den?      | Genväg på skrivbordet (`C:\JuliaPro-0.5.1.1\Juno.bat`) på Windows, genvägen på skrivbordet (`/opt/JuliaPro-VERSION/Juno`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Python-språk    |
| Stöds DSVM-versioner      | Linux      |
| Vanliga användningsområden      |  Python-utveckling     |
| Hur du använder / köra den?      | Genväg på skrivbordet (`/usr/bin/pycharm`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 

|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiv datavisualisering och BI-verktyg    |
| Stöds DSVM-versioner      | Windows  |
| Vanliga användningsområden      |  Datavisualisering och skapa instrumentpaneler   |
| Hur du använder / köra den?      | Genväg på skrivbordet (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

