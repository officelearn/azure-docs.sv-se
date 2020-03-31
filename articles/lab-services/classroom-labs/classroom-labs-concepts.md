---
title: Koncept för Klassrumslabb – Azure Lab Services | Microsoft-dokument
description: Lär dig de grundläggande begreppen i Lab Services och hur det kan göra det enkelt att skapa och hantera labb.
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
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526237"
---
# <a name="classroom-labs-concepts"></a>Classroom Labs-begrepp

Följande lista innehåller viktiga begrepp och definitioner av Lab Services:

## <a name="quota"></a>Kvot

Kvot är den tidsgräns (i timmar) som en lärare kan ange för en deltagare att använda en virtuell labbdator. Den kan ställas in på 0, eller ett visst antal timmar. Om kvoten är inställd på 0 kan en deltagare bara använda den virtuella datorn när ett schema körs eller när en lärare manuellt aktiverar den virtuella datorn för eleven.  

Kvottimmar räknas när deltagaren själva startar labbdass.  Om en lärare startar labbdassen för en elev manuellt används inte kvottimmar för den eleven.

## <a name="schedules"></a>Scheman

Scheman är de tidsluckor som en lärare kan skapa för klassen så att elevernas virtuella datorer är tillgängliga för klasstid.  Scheman kan vara en gång eller återkommande.  Kvottimmar används inte när ett schema körs.

Det finns tre typer av scheman: Standard, Endast Start och Stoppa.

- **Standard**.  Det här schemat startar alla virtuella deltagares virtuella datorer vid den angivna starttiden och stänger av alla virtuella deltagares virtuella datorer vid den angivna stopptiden.
- **Starta bara**.   Det här schemat startar alla virtuella deltagares virtuella datorer vid den angivna tidpunkten.  Virtuella datorer för deltagare stoppas inte förrän en deltagare stoppar den virtuella datorn via Azure Lab Services-portalen eller ett endast stoppschema inträffar.
- **Bara stanna**.  Det här schemat stoppar alla virtuella student-datorer vid den angivna tidpunkten.  

## <a name="template-virtual-machine"></a>Virtuell malldator

En virtuell malldator i ett labb är en grundläggande virtuell datoravbildning från vilken alla användares virtuella datorer skapas. Utbildare /lab skapare ställa in mallen virtuell dator och konfigurera den med den programvara som de vill ge till utbildning deltagare att göra labs. När du publicerar en virtuell mall skapar eller uppdaterar Azure Lab Services virtuella datorer baserat på mallen VM.

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
