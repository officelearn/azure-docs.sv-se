---
title: Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker.
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
ms.openlocfilehash: 695b87540178fb5c1012a095c2a86937a189ef24
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133754"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker

Den här artikeln beskriver hur du konfigurerar en mallfil i labb tjänster med de verktyg som behövs för att lära eleverna hur de använder [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io).  Jupyter Notebooks är ett projekt med öppen källkod som gör att du enkelt kan kombinera text och körbara [python](https://www.python.org/) -källkod på en enda arbets yta som kallas för en bärbar dator.  Att köra en bärbar dator resulterar i en linjär inspelning av indata och utdata.  Dessa utdata kan innehålla text, tabeller med information, punkt diagram med mera.

## <a name="lab-configuration"></a>Labb konfiguration

För att kunna konfigurera det här labbet behöver du ett Azure-prenumerations-och labb konto för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).  Du kan också använda ett befintligt labb konto.

### <a name="lab-account-settings"></a>Labb konto inställningar

Aktivera inställningarna som beskrivs i tabellen nedan för labb kontot. Mer information om hur du aktiverar Marketplace-avbildningar finns i [Ange Marketplace-avbildningar som är tillgängliga för labb skapare](how-to-configure-lab-accounts.md#specify-marketplace-images-available-to-lab-creators).

| Inställning för labb konto | Instruktioner |
| ------------------- | ------------ |
| Marketplace-avbildning | Aktivera [data science Virtual Machine-Windows 2016-](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) avbildningen för användning i ditt labb konto. |

>[!TIP]
>Den här artikeln fokuserar på att konfigurera en mall som använder operativ systemet Windows Server.  Det är också möjligt att ställa in en data vetenskaps klass med python-och Jupyter-anteckningsböcker med [data science Virtual Machine för Linux (CentOS)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm) eller [data science Virtual Machine för Linux-avbildningar (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) från Azure Marketplace.

### <a name="lab-settings"></a>Labb inställningar

Använd inställningarna i tabellen nedan när du konfigurerar ett klass rums labb.  Mer information om hur du skapar ett klass rums labb finns i [Konfigurera en labb Guide för klass rummet](tutorial-setup-classroom-lab.md).

| Labb inställningar | Värde/anvisningar |
| ------------ | ------------------ |
|Storlek på virtuell dator| Liten GPU (Compute). Den här storleken passar bäst för beräknings intensiva och nätverks intensiva program som artificiell intelligens och djup inlärning. |
|Avbildning av virtuell dator| Data Science Virtual Machine-Windows 2016|

## <a name="template-machine"></a>Mall dator

[Data science Virtual Machine-Windows 2016-](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) avbildningen ger de nödvändiga ramverk för djup inlärning och de verktyg som krävs för den här typen av klass.  Avbildningen innehåller Jupyter-anteckningsböcker och Visual Studio Code.  [Jupyter-anteckningsböcker](http://jupyter-notebook.readthedocs.io) är ett webb program som gör det möjligt för data experter att ta rå data, köra beräkningar och se alla resultat i samma miljö.  Webb programmet kommer att köras lokalt för vår mall.  [Visual Studio Code](https://code.visualstudio.com/) är en IDE som ger en omfattande interaktiv upplevelse när du skriver och testar en bärbar dator.  Mer information finns i [arbeta med antecknings böcker i Jupyter i Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

Den återstående aktiviteten för att konfigurera klassen är att tillhandahålla lokala antecknings böcker.  Instruktioner för hur du använder Azure Machine Learning exempel finns i [så här konfigurerar du en miljö med Jupyter-anteckningsböcker](../../machine-learning/how-to-configure-environment.md#jupyter).  Du kan också ange egna antecknings böcker på den mall datorn.  Antecknings böckerna kommer att kopieras till alla elev maskiner när mallen publiceras.

## <a name="cost-estimate"></a>Kostnads uppskattning

Vi ska se en möjlig kostnads uppskattning för den här klassen.  Vi använder en klass av 25 studenter.  Det finns 20 timmar med den schemalagda klass tiden.  Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför schemalagda klass tider.  Storleken på den virtuella datorn som vi valde var liten GPU (Compute), vilket är 139 lab-enheter.

Här är ett exempel på en möjlig kostnads uppskattning för den här klassen:

25 studenter \* (20 schemalagda timmar + 10 kvot timmar) \* 139 lab-enheter \* 0,01 USD per timme = 1042,5 USD

Mer detaljerad information om priser finns [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sammanfattning

I den här artikeln gick vi igenom stegen för att skapa ett labb för en Jupyter Notebooks-klass. Du kan använda en liknande installation för andra maskin inlärnings klasser.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att ställa in alla labb.

- [Skapa och hantera en mall](how-to-create-manage-template.md)
- [Lägga till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users)
