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
ms.openlocfilehash: 19191e84e9af140f78e529398471f0e950b36c8c
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694685"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Konfigurera ett labb som fokuserar på djup inlärning i naturlig språk bearbetning med hjälp av Azure Lab Services
Den här artikeln visar hur du konfigurerar ett labb som fokuserar på djup inlärning i naturlig språk bearbetning (NLP) med hjälp av Azure Lab Services. NLP (Natural Language Processing) är en form av artificiell intelligens (AI) som gör det möjligt för datorer med översättning, tal igenkänning och andra funktioner för språk förståelse.  

Studenter som tar en NLP-klass får en virtuell Linux-dator (VM) för att lära sig hur man använder neurala-nätverks algoritmer för att utveckla djup inlärnings modeller som används för att analysera skrivna mänskliga språk. 

## <a name="lab-configuration"></a>Labb konfiguration
Du måste ha en Azure-prenumeration för att komma igång för att kunna konfigurera det här labbet. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har en Azure-prenumeration kan du antingen skapa ett nytt labb konto i Azure Lab Services eller använda ett befintligt labb konto. Se följande självstudie för att skapa ett nytt labb konto: [Självstudie för att konfigurera ett labb konto](tutorial-setup-lab-account.md).
 
När du har skapat labb kontot aktiverar du följande inställningar i labb kontot: 

| Inställning för labb konto | Instruktioner |
| ----------- | ------------ |  
| Marketplace-avbildningar | Aktivera Data Science Virtual Machine för Linux-avbildningen (Ubuntu) för användning i ditt labb konto.  Läs följande artiklar om du vill lära dig mer: [Ange Marketplace-avbildningar som är tillgängliga för labb skapare](tutorial-setup-lab-account.md#specify-marketplace-images-available-to-lab-creators). | 

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och tillämpa följande inställningar:

| Labb inställningar | Värde/anvisningar | 
| ------------ | ------------------ |
| Storlek för virtuell dator (VM) | Liten GPU (Compute). Den här storleken passar bäst för beräknings intensiva och nätverks intensiva program som artificiell intelligens och djup inlärning. |
| Avbildning av virtuell dator | [Data science Virtual Machine för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Den här avbildningen innehåller ramverk för djup inlärning och verktyg för maskin inlärning och data vetenskap. Om du vill visa en fullständig lista över installerade verktyg på den här avbildningen kan du läsa följande artikel: [Vad ingår i DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Aktivera anslutning till fjärr skrivbord | Använd. <p>Om du aktiverar den här inställningen kan lärare och studenter ansluta till sina Virtual Machines (VM) med hjälp av fjärr skrivbord (RDP).</p><p>**Viktigt**: RDP är redan installerat och konfigurerat på Data Science Virtual Machine för Linux-avbildningen. Därför kan lärare/studenter ansluta till virtuella datorer via RDP utan ytterligare åtgärder. Om du behöver ansluta till det grafiska Skriv bordet har den här avbildningen redan [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) installerad på den virtuella datorn. Studenter måste installera X2Go-klienten på sina lokala datorer och måste använda klienten för att ansluta. Mer information finns i följande guider: <ul><li>[Så här får du åtkomst till Data Science Virtual Machine för Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Ansluta till den virtuella dator mal len för att installera RDP-och GUI-paket](how-to-enable-remote-desktop-linux.md#teachers-connecting-to-the-template-vm-using-rdp)</li></ul></p>   |

Data Science Virtual Machine för Linux-avbildningen ger de nödvändiga djup inlärnings ramverk och verktyg som krävs för den här typen av klass. När du har skapat mallen behöver du alltså inte anpassa den ytterligare. Det kan publiceras för studenter att använda. Klicka på knappen **publicera** på mallsida för att publicera mallen i labbet.  

## <a name="cost"></a>Kostnad
Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel: 

För en klass med 25 studenter med 20 timmar schemalagda klass tider och 10 timmars kvot för läxor eller tilldelningar, skulle priset för labbet vara-25 studenter * (20 + 10) timmar * 139 lab-enheter * 0,01 USD per timme = 1042,5 USD

Mer detaljerad information om priser finns [Azure Lab Services prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Sammanfattning
Den här artikeln gick dig genom stegen för att skapa ett labb för en bearbetnings klass för naturliga språk. Du kan använda en liknande installation för andra djup inlärnings klasser.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att ställa in labb:

- [Lägga till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](tutorial-setup-classroom-lab.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](tutorial-setup-classroom-lab.md#send-an-email-with-the-registration-link). 

