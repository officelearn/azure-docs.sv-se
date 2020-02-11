---
title: Klass rum labb koncept – Azure Lab Services | Microsoft Docs
description: Lär dig grunderna i labb tjänster och hur det kan göra det enkelt att skapa och hantera labb.
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
ms.openlocfilehash: 348340516f9332f5492c7ce60c3d164da44a008c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120893"
---
# <a name="classroom-labs-concepts"></a>Classroom Labs-begrepp

Följande lista innehåller koncept och definitioner för nyckel labb tjänster:

## <a name="quota"></a>Resurser

Kvoten är den tids gräns (i timmar) som en lärare kan ange för en elev att använda en virtuell labb dator. Det kan ställas in på 0 eller ett angivet antal timmar. Om kvoten är inställd på 0, kan en student bara använda den virtuella datorn när ett schema körs eller när en lärare manuellt aktiverar den virtuella datorn för studenten.  

Kvot timmar räknas när studenten startar själva labb datorn.  Om en lärare manuellt startar den virtuella labb datorn för en student, används inte kvot timmar för den studenten.

## <a name="schedules"></a>Scheman

Scheman är de tids platser (en tid eller återkommande) som en lärare kan skapa för klassen. Alla virtuella datorer i labbet startas automatiskt i början av schemat och stoppas i slutet av schemat. Kvot timmar används inte när ett schema körs.

## <a name="template-virtual-machine"></a>Mall för virtuell dator

En virtuell mall i ett labb är en bas avbildning av virtuella datorer från vilken alla användares virtuella datorer skapas. Utvecklare/labb skapar skapare av den virtuella dator mal len och konfigurerar den med den program vara som de vill tillhandahålla för att utbilda deltagare till att göra labb. När du publicerar en mall för virtuella Azure Lab Services datorer skapar eller uppdaterar virtuella labb virtuella datorer baserat på mallen VM.

## <a name="user-profiles"></a>Användarprofiler

Den här artikeln beskriver olika användarprofiler i Azure Lab Services.

### <a name="lab-account-owner"></a>Labbkontoägare

Vanligtvis fungerar en IT-administratör för organisationens molnresurser som ansvarar för Azure-prenumerationen som labbkontoägare och utför följande uppgifter:

- Konfigurerar ett labbkonto för organisationen.
- Hanterar och konfigurerar principer för alla labb.
- Ger behörigheter till personer i organisationen för att skapa ett labb under labbkontot.

### <a name="professor"></a>Lärare

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
