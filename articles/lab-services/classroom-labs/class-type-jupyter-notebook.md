---
title: Inrätta ett labb för att lära ut datavetenskap med Python och Jupyter bärbara datorer | Microsoft-dokument
description: Lär dig hur du konfigurerar ett labb för att lära ut datavetenskap med hjälp av Python och Jupyter-anteckningsböcker.
services: lab-services
documentationcenter: na
author: emaher
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2019
ms.author: enewman
ms.openlocfilehash: dfb133f9aa3dd9b76f8b4ea4c6188cfaf9a67b75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444119"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Skapa ett labb för att lära ut datavetenskap med Python- och Jupyter-anteckningsböcker

I den här artikeln beskrivs hur du konfigurerar en mallmaskin i Lab Services med de verktyg som behövs för att lära eleverna hur du använder [Jupyter-anteckningsböcker](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks är ett projekt med öppen källkod som gör att du enkelt kan kombinera RTF och körbar [Python-källkod](https://www.python.org/) på en enda duk som kallas en anteckningsbok.  Om du kör en anteckningsbok visas en linjär post med indata och utdata.  Dessa utdata kan innehålla text, tabeller med information, punktdiagram med mera.

## <a name="lab-configuration"></a>Labbkonfiguration

För att konfigurera det här labbet behöver du en Azure-prenumeration och labbkonto för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har fått en Azure-prenumeration kan du skapa ett nytt labbkonto i Azure Lab Services. Mer information om hur du skapar ett nytt labbkonto finns i [självstudiekursen för att konfigurera ett labbkonto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labbkonto.

### <a name="lab-account-settings"></a>Inställningar för labbkonto

Aktivera inställningarna som beskrivs i tabellen nedan för labbkontot. Mer information om hur du aktiverar marketplace-avbildningar finns i [ange Marketplace-avbildningar som är tillgängliga för labbskapare](specify-marketplace-images.md).

| Inställning av labbkonto | Instruktioner |
| ------------------- | ------------ |
| Marketplace-bild | Aktivera [data science-datorn – Windows 2016-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) för användning i ditt labbkonto. |

>[!TIP]
>Den här artikeln fokuserar på att konfigurera en malldator som använder operativsystemet Windows Server.  Det är också möjligt att konfigurera en datavetenskapsklass med Python- och Jupyter-anteckningsböcker med [datavetenskap virtuell dator för Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) eller [Data Science Virtual Machine för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) bilder från Azure Marketplace.

### <a name="lab-settings"></a>Labbinställningar

Använd inställningarna i tabellen nedan när du konfigurerar ett klassrumslabb.  Mer information om hur du skapar ett klassrumslabb finns i [konfigurera en självstudiekurs för klassrumslabb](tutorial-setup-classroom-lab.md).

| Labbinställningar | Värde/instruktioner |
| ------------ | ------------------ |
|Storlek på virtuell dator| Liten GPU (Beräkning). Den här storleken passar bäst för beräkningsintensiva och nätverksintensiva program som artificiell intelligens och djupinlärning. |
|Bild av virtuell dator| Virtuell dator för datavetenskap – Windows 2016|

## <a name="template-machine"></a>Mallmaskin

Data [Science Virtual Machine - Windows 2016-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) innehåller de ramverk och verktyg för djupinlärning som krävs för den här typen av klass.  Bilden innehåller Jupyter notebooks och Visual Studio Code.  [Jupyter Bärbara datorer](http://jupyter-notebook.readthedocs.io) är ett webbprogram som gör det möjligt för dataforskare att ta rådata, köra beräkningar och se resultaten alla i samma miljö.  För vår mallmaskin kommer webbapplikationen att köras lokalt.  [Visual Studio Code](https://code.visualstudio.com/) är en IDE som ger en rik interaktiv upplevelse när du skriver och testar en anteckningsbok.  Mer information finns i [Arbeta med Jupyter-anteckningsböcker i Visual Studio-kod](https://code.visualstudio.com/docs/python/jupyter-support).

Den återstående uppgiften att ställa in klassen är att tillhandahålla lokala anteckningsböcker.  Instruktioner om hur du använder azure machine learning-exemplen finns i [hur du konfigurerar en miljö med Jupyter-anteckningsböcker](../../machine-learning/how-to-configure-environment.md#jupyter).  Du kan också ange egna anteckningsböcker på mallen.  Anteckningsböckerna kopieras till alla elevdatorer när mallen publiceras.

## <a name="cost-estimate"></a>Kostnadsuppskattning

Låt oss täcka en möjlig kostnadsuppskattning för den här klassen.  Vi använder en klass på 25 elever.  Det finns 20 timmars schemalagd lektionstid.  Dessutom får varje elev 10 timmars kvot för läxor eller uppgifter utanför schemalagd lektionstid.  Den virtuella maskinen storlek vi valde var liten GPU (beräkning), vilket är 139 lab enheter.

Här är ett exempel på en möjlig kostnadsuppskattning för den här klassen:

25 \* studenter (20 schemalagda timmar + \* 10 kvottimmar) 139 labbenheter \* 0,01 USD per timme = 1042,5 USD

Mer information om prissättning finns i [Azure Lab Services Prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats

I den här artikeln gick vi igenom stegen för att skapa ett labb för en Jupyter Notebooks klass. Du kan använda en liknande inställning för andra maskininlärningsklasser.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att konfigurera ett labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Länkar till e-postregistrering till studenter](how-to-configure-student-usage.md#send-invitations-to-users)
