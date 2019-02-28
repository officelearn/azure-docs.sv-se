---
title: Klassrum Labs-koncept - Azure Lab Services | Microsoft Docs
description: Lär dig de grundläggande principerna för Lab Services och hur det kan göra det enkelt att skapa och hantera labb.
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
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: f0f45e50fba4587e604add937ed45fc78228d44f
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960254"
---
# <a name="classroom-labs-concepts"></a>Classroom Labs-begrepp
I följande lista innehåller huvudbegrepp Lab Services och definitioner:

## <a name="quota"></a>Kvot
Kvoten är tidsgränsen (i timmar) som en lärare kan ställa in för en student att använda ett labb VM. Det kan anges till 0, obegränsat eller ett visst antal timmar. Om kvoten har angetts till 0, kan en student bara använda den virtuella datorn när ett schema körs eller när en lärare sätter manuellt på den virtuella datorn för studenter.
 
##<a name="schedules"></a>Scheman
Scheman är tid (en gång eller återkommande) som en lärare kan skapa för klassen. Alla virtuella datorer i labbet startas automatiskt i början till schemat och de stoppas i slutet av schemat. Kvot timmar används inte när ett schema körs.

## <a name="template-virtual-machine"></a>Mall för virtuell dator
En mall för virtuell dator i ett labb är en grundläggande VM-avbildning från vilken alla användares virtuella datorer skapas. Skapare av utbildare/testlabb konfigurera mallen för virtuell dator och konfigurera den med den programvara som de vill tillhandahålla till utbildning deltagare göra labs. När du publicerar en mall för virtuell dator, Azure Lab Services skapar eller uppdaterar lab virtuella datorer baserat på VM-mallen. 


## <a name="user-profiles"></a>Användarprofiler
Den här artikeln beskriver olika användarprofiler i Azure Lab Services. 

### <a name="lab-account-owner"></a>Labbkontoägare
Vanligtvis fungerar en IT-administratör för organisationens molnresurser som ansvarar för Azure-prenumerationen som labbkontoägare och utför följande uppgifter:   

- Konfigurerar ett labbkonto för organisationen.
- Hanterar och konfigurerar principer för alla labb.
- Ger behörigheter till personer i organisationen för att skapa ett labb under labbkontot.

### <a name="professor"></a>Professor
Normalt skapar användare till exempel en lärare eller en online-undervisare klassrumslabb under ett labbkonto. En utbildare utför följande uppgifter: 

- Skapar ett klassrumslabb.
- Skapar virtuella maskiner i labbet. 
- Installerar rätt programvara på virtuella datorer.
- Anger vem som kan få åtkomst till labbet.
- Tillhandahåller registreringslänken till labbet för studenter.

### <a name="student"></a>Student
En student utför följande uppgifter:

- Använder registreringslänken som denne får från labbskaparen för att registrera sig för labbet. 
- Ansluter till en virtuell dator i labbet och använder den för att göra klassarbete, uppgifter och projekt. 

## <a name="next-steps"></a>Nästa steg
Kom igång med att konfigurera ett labbkonto som krävs för att skapa ett klassrumslabb med Azure Lab Services:

- [Konfigurera ett labbkonto](tutorial-setup-lab-account.md)
