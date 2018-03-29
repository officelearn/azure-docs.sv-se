---
title: Utvecklingsverktyg för data - vetenskap virtuella Azure | Microsoft Docs
description: Datavetenskap virtuella datorn utvecklingsverktyg.
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
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 6f141fc03b64d0ca922d003f6352b7751ab9967d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Utvecklingsverktyg på datavetenskap virtuell dator

Den virtuella datorn på vetenskap (DSVM) ger en produktiv miljö för dina utveckling genom paketering flera populära verktyg och IDE. Här följer några verktyg som finns på DSVM. 

## <a name="visual-studio-2017"></a>Visual Studio 2017  
|    |           |
| ------------- | ------------- |
| Vad är det?   | Generella IDE      |
| Stöds DSVM versioner      | Windows      |
| Vanliga användningsområden      | Programutveckling    |
| Hur är det konfigurerade / installerad på DSVM?      | Data vetenskap arbetsbelastningen (Python och R verktyg), arbetsbelastning i Azure (Hadoop, Data Lake), Node.js, SQL Server-verktyg [Visual Studio Tools för AI](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hur du använder / kör den?      | Genväg på skrivbordet (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Relaterade verktyg på DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio-koden 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Generella IDE      |
| Stöds DSVM versioner      | Windows, Linux     |
| Vanliga användningsområden      | Redigerare och Git-integrering   |
| Hur du använder / kör den?      | Genväg på skrivbordet (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) i Windows, genväg på skrivbordet eller terminal (`code`) i Linux    |
| Relaterade verktyg på DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för R    |
| Stöds DSVM versioner      | Windows, Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Hur du använder / kör den?      | Genväg på skrivbordet (`C:\Program Files\RStudio\bin\rstudio.exe`) i Windows, genväg på skrivbordet (`/usr/bin/rstudio`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio  Server 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Webbaserade IDE för R    |
| Stöds DSVM versioner      | Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Hur du använder / kör den?      | Aktivera tjänsten med _systemctl aktivera rstudio server_, starta tjänsten med _systemctl starta rstudio-server_. Du kan sedan logga in till RStudio servern på http://your-vm-ip:8787.       |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Julia språk   |
| Stöds DSVM versioner      | Windows, Linux      |
| Vanliga användningsområden      |  Julia utveckling     |
| Hur du använder / kör den?      | Genväg på skrivbordet (`C:\JuliaPro-0.5.1.1\Juno.bat`) i Windows, genväg på skrivbordet (`/opt/JuliaPro-VERSION/Juno`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm
|    |           |
| ------------- | ------------- |
| Vad är det?   | Klienten IDE för Python-språk    |
| Stöds DSVM versioner      | Linux      |
| Vanliga användningsområden      |  R-utveckling     |
| Hur du använder / kör den?      | Genväg på skrivbordet (`/usr/bin/pycharm`) på Linux      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiv datavisualisering och BI-verktyg    |
| Stöds DSVM versioner      | Windows  |
| Vanliga användningsområden      |  Visualisering av data och skapa instrumentpaneler   |
| Hur du använder / kör den?      | Genväg på skrivbordet (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Relaterade verktyg på DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

