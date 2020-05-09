---
title: Konfigurera ett labb som fokuserar på djup inlärning med Azure Lab Services | Microsoft Docs
description: Lär dig hur du konfigurerar ett labb för att lära sig Shell-skript i Linux.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0dab5f3dcdfb6ddabbd94960dcf8a8bf2bce98af
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890775"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services
Den här artikeln visar hur du konfigurerar ett labb som fokuserar på djup inlärning i naturlig språk bearbetning (NLP) med hjälp av Azure Lab Services. NLP (Natural Language Processing) är en form av artificiell intelligens (AI) som gör det möjligt för datorer med översättning, tal igenkänning och andra funktioner för språk förståelse.  

Studenter som tar en NLP-klass får en virtuell Linux-dator (VM) för att lära sig hur man använder neurala-nätverks algoritmer för att utveckla djup inlärnings modeller som används för att analysera skrivna mänskliga språk. 

## <a name="lab-configuration"></a>Labb konfiguration
Du måste ha en Azure-prenumeration för att komma igång för att kunna konfigurera det här labbet. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har en Azure-prenumeration kan du antingen skapa ett nytt labb konto i Azure Lab Services eller använda ett befintligt labb konto. Se följande självstudie för att skapa ett nytt labb konto: [självstudier för att konfigurera ett labb konto](tutorial-setup-lab-account.md).
 
När du har skapat labb kontot aktiverar du följande inställningar i labb kontot: 

| Inställning för labb konto | Instruktioner |
| ----------- | ------------ |  
| Marketplace-avbildningar | Aktivera Data Science Virtual Machine för Linux-avbildningen (Ubuntu) för användning i ditt labb konto.  Se följande artikel för instruktioner: [Ange Marketplace-avbildningar som är tillgängliga för labb skapare](specify-marketplace-images.md). | 

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och tillämpa följande inställningar:

| Labb inställningar | Värde/anvisningar | 
| ------------ | ------------------ |
| Storlek för virtuell dator (VM) | Liten GPU (Compute). Den här storleken passar bäst för beräknings intensiva och nätverks intensiva program som artificiell intelligens och djup inlärning. |
| Avbildning av virtuell dator | [Data science Virtual Machine för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Den här avbildningen innehåller ramverk för djup inlärning och verktyg för maskin inlärning och data vetenskap. Om du vill visa en fullständig lista över installerade verktyg på den här avbildningen kan du läsa följande artikel: [Vad ingår i DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Aktivera anslutning till fjärr skrivbord | <p>Om du aktiverar den här inställningen kan lärare och studenter ansluta till sina Virtual Machines (VM) med hjälp av fjärr skrivbord (RDP).</p><p>**Viktigt**: om du aktiverar den här inställningen öppnas endast **RDP** -porten på Linux-datorer. Om RDP redan har installerats och kon figurer ATS på avbildningen av den virtuella datorn kan du/studenter ansluta till virtuella datorer via RDP utan att följa ytterligare steg. <p>Om den virtuella dator avbildningen inte har RDP installerat och konfigurerad, måste du ansluta till Linux-datorn med SSH för första gången och installera RDP-och GUI-paket så att du/studenter kan ansluta till Linux-datorn med RDP senare. Mer information finns i [Installera och konfigurera fjärr skrivbord för att ansluta till en virtuell Linux-dator i Azure](../../virtual-machines/linux/use-remote-desktop.md). Sedan publicerar du avbildningen så att eleverna kan RDP i de virtuella student Linux-datorerna.  |

Data Science Virtual Machine för Linux-avbildningen ger de nödvändiga djup inlärnings ramverk och verktyg som krävs för den här typen av klass. När du har skapat mallen behöver du alltså inte anpassa den ytterligare. Det kan publiceras för studenter att använda. Klicka på knappen **publicera** på mallsida för att publicera mallen i labbet.  

## <a name="cost"></a>Kostnad
Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel: 

För en klass med 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara-25 studenter * (20 + 10) timmar * 139 lab-enheter * 0,01 USD per timme = 1042,5 USD

Mer detaljerad information om priser finns [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats
Den här artikeln gick dig genom stegen för att skapa ett labb för en bearbetnings klass för naturliga språk. Du kan använda en liknande installation för andra djup inlärnings klasser.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att ställa in labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users). 

