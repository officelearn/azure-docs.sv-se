---
title: Konfigurera ett labb med fokus på djupinlärning med Azure Lab Services | Microsoft-dokument
description: Lär dig hur du konfigurerar ett labb för att lära ut skalskript på Linux.
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
ms.openlocfilehash: 49ef78ac5a7d58d86583d91bf072f0f3131796fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109399"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Konfigurera ett labb med fokus på djupinlärning i bearbetning av naturligt språk med Azure Lab Services
Den här artikeln visar hur du konfigurerar ett labb med fokus på djupinlärning i bearbetning av naturligt språk (NLP) med Hjälp av Azure Lab Services. Bearbetning av naturligt språk (NLP) är en form av artificiell intelligens (AI) som möjliggör datorer med översättning, taligenkänning och andra funktioner för språks förståelse.  

Studenter som tar en NLP-klass får en virtuell Linux-maskin (VM) för att lära sig att tillämpa neurala nätverksalgoritmer för att utveckla djupinlärningsmodeller som används för att analysera skriftligt mänskligt språk. 

## <a name="lab-configuration"></a>Labbkonfiguration
För att konfigurera det här labbet behöver du en Azure-prenumeration för att komma igång. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. När du har en Azure-prenumeration kan du antingen skapa ett nytt labbkonto i Azure Lab Services eller använda ett befintligt labbkonto. Se följande självstudie för att skapa ett nytt labbkonto: [Handledning för att konfigurera ett labbkonto](tutorial-setup-lab-account.md).
 
När du har skapat labbkontot aktiverar du följande inställningar i labbkontot: 

| Inställning av labbkonto | Instruktioner |
| ----------- | ------------ |  
| Marketplace-bilder | Aktivera avbildningen Data Science Virtual Machine for Linux (Ubuntu) för användning i ditt labbkonto.  Se följande artikel för instruktioner: [Ange marketplace-bilder som är tillgängliga för labbskapare](specify-marketplace-images.md). | 

Följ [den här självstudien](tutorial-setup-classroom-lab.md) för att skapa ett nytt labb och tillämpa följande inställningar:

| Labbinställningar | Värde/instruktioner | 
| ------------ | ------------------ |
| Storlek på virtuell dator (VM) | Liten GPU (Beräkning). Den här storleken passar bäst för beräkningsintensiva och nätverksintensiva program som artificiell intelligens och djupinlärning. |
| Vm-avbildning | [Data Science Virtual Machine för Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). Den här bilden ger ramverk och verktyg för djupinlärning för maskininlärning och datavetenskap. Om du vill visa hela listan över installerade verktyg på den här bilden läser du följande artikel: [Vad finns på DSVM?](../../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm). |
| Aktivera anslutning till fjärrskrivbord | Aktivera. <p>Om du aktiverar den här inställningen kan lärare och elever ansluta till sina virtuella datorer med fjärrskrivbord (RDP).</p><p>**Viktigt:** RDP är redan installerat och konfigurerat på Virtual Machine för Data Science för Linux-avbildningen. Därför kan lärare/elever ansluta till virtuella datorer via RDP utan några ytterligare steg. Om du behöver ansluta till det grafiska skrivbordet har den här avbildningen redan [X2Go Server](https://wiki.x2go.org/doku.php/doc:newtox2go) installerad på den virtuella datorn. Studenter måste installera X2Go-klienten på sina lokala datorer och måste använda klienten för anslutning. Mer information finns i följande guider: <ul><li>[Så här kommer du åt Virtual Machine för Data Science för Linux](../../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)</li><li>[Ansluta till mallen VM för att installera RDP- och GUI-paket](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)</li></ul></p>   |

Data Science Virtual Machine för Linux-avbildningen ger de nödvändiga ramverken och verktygen för djupinlärning som krävs för den här typen av klass. När mallens maskin har skapats behöver du därför inte anpassa den ytterligare. Den kan publiceras för studenter att använda. Välj knappen **Publicera** på mallsidan om du vill publicera mallen i labbet.  

## <a name="cost"></a>Kostnad
Om du vill uppskatta kostnaden för det här labbet kan du använda följande exempel: 

För en klass på 25 elever med 20 timmars schemalagd lektionstid och 10 timmars kvot för läxor eller uppgifter, skulle priset för labbet vara - 25 studenter * (20 + 10) timmar * 139 Lab Units * 0.01 USD per timme = 1042.5 USD

Mer information om prissättning finns i [Azure Lab Services Prissättning](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Slutsats
Den här artikeln gick igenom stegen för att skapa ett labb för bearbetning av naturligt språk klass. Du kan använda en liknande inställning för andra djupinlärningsklasser.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att konfigurera ett labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users). 

