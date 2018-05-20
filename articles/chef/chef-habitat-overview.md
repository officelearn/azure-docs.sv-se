---
title: Använda livsmiljö för att distribuera programmet till Azure
description: Lär dig hur du alltid distribuera appen till Azure virtuella datorer och behållare
keywords: Azure, chef, devops, virtuella datorer, översikt, automatisera livsmiljö
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 73c6834eb53c0496fa673dd25ab90abc18a6a139
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="use-habitat-to-deploy-your-application-to-azure"></a>Använda livsmiljö för att distribuera programmet till Azure
[Livsmiljö](https://www.habitat.sh/) är en första av dess kind projekt för öppen källkod som erbjuder en helt ny metod för programhantering. Livsmiljö gör programmet och dess automation enheten av distribution. När program placeras i en enkel ”livsmiljö” körningsmiljön, oavsett om det är en behållare, utan operativsystem eller PaaS, inte längre är i fokus och begränsa inte programmet. 

Den här artikeln beskriver fördelarna med att använda livsmiljö.

## <a name="support-for-the-modern-application"></a>Stöd för moderna program
Livsmiljö ingår allt som ett program måste köras under hela dess livscykel. Livsmiljöns kärnkomponenter är:
- Paketering format - program i ett paket med livsmiljö är atomic, ändras och granskningsbara.
- Livsmiljö handledarens körs programpaket med medvetenhet om de paket peer relationer, strategi för uppgradering och säkerhetsprinciper. Livsmiljö administratören konfigurerar och hanterar programmet för oavsett miljö finns.
- Livsmiljö ekosystemet innehåller också en build-tjänst som tar en livsmiljö byggplan skapar livsmiljö paketet och publicerar den till en anläggning.

## <a name="run-any-application-anywhere"></a>Kör alla program var som helst
Livsmiljöns kan program köra oförändrade i alla körningsmiljön. Detta omfattar allt från utan operativsystem och virtuella datorer av behållare (till exempel Docker), kluster-system (till exempel Mesosphere eller Kubernetes) och PaaS-system (till exempel spela en central molnet Foundry).

## <a name="easily-port-legacy-applications"></a>Enkelt port äldre program
Programmen är oberoende av miljö som de ursprungligen har utvecklats när äldre program som placeras i ett livsmiljö paket. Paket kan snabbt flyttas till mer modern miljöer, till exempel molnet eller behållare. Eftersom livsmiljö paket har ett standard passiv Internetriktade gränssnitt, blir även äldre program enklare att hantera.

## <a name="improve-the-container-experience"></a>Förbättra upplevelsen behållare
Livsmiljö minskar komplexiteten vid hantering av behållare i produktionsmiljöer. Genom att automatisera programkonfigurationen i en behållare adresser livsmiljö utmaningar utvecklare står inför när du flyttar behållare-baserade program från utvecklingsmiljöer till produktionen.

## <a name="integrate-into-the-chef-devops-workflow"></a>Integrera i Chef DevOps-arbetsflöde
Projektet livsmiljö sponsras av Chef. Livsmiljö utnyttjar chefs djup erfarenhet av infrastrukturen automation för att göra en oöverträffad automatiseringsfunktionerna program. Chef kommersiella stöder livsmiljö och säkerställa sömlös integrering mellan livsmiljö och leverans av Chef för att automatisera programmets version livscykel, från utvecklingsmiljö till distribution.

## <a name="next-steps"></a>Nästa steg
* [Skapa en virtuell Windows-dator på Azure med hjälp av Chef](/azure/virtual-machines/windows/chef-automation)