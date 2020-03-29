---
title: Vad är Azure Cloud Services | Microsoft-dokument
description: Läs mer om vad Azure Cloud Services är.
services: cloud-services
author: tgore03
ms.service: multiple
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: c531e02656c9f6342670024b2220386e789a2d98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386858"
---
# <a name="overview-of-azure-cloud-services"></a>Översikt över Azure Cloud Services
Azure Cloud Services är ett exempel på en [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Precis som [Azure App Service](../app-service/overview.md)är den här tekniken utformad för att stödja program som är skalbara, tillförlitliga och billiga att använda. På samma sätt som App Service finns på virtuella datorer ( virtuella datorer), så är också Azure Cloud Services. Du har dock mer kontroll över de virtuella datorerna. Du kan installera din egen programvara på virtuella datorer som använder Azure Cloud Services och du kan komma åt dem på distans.

![Azure Cloud Services-diagram](./media/cloud-services-choose-me/diagram.png)

Mer kontroll innebär också mindre användarvänlighet. Om du inte behöver ytterligare kontrollalternativ är det vanligtvis snabbare och enklare att få igång ett webbprogram i funktionen Webbappar i App-tjänsten jämfört med Azure Cloud Services.

Det finns två typer av Azure Cloud Services-roller. Den enda skillnaden mellan de två är hur din roll är värd på de virtuella datorerna:

* **Webbroll**: Distribuerar och är värd för din app automatiskt via IIS.

* **Arbetarroll**: Använder inte IIS och kör din app fristående.

Ett enkelt program kan till exempel bara använda en enda webbroll som betjänar en webbplats. Ett mer komplext program kan använda en webbroll för att hantera inkommande begäranden från användare och sedan vidarebefordra dessa begäranden till en arbetsroll för bearbetning. (Den här kommunikationen kan använda [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) eller Azure Queue [storage](../storage/common/storage-introduction.md).)

Som föregående bild antyder körs alla virtuella datorer i ett enda program i samma molntjänst. Användare kommer åt programmet via en enda offentlig IP-adress, med begäranden automatiskt belastningsbalanserade över programmets virtuella datorer. Plattformen [skalar och distribuerar](cloud-services-how-to-scale-portal.md) de virtuella datorerna i ett Azure Cloud Services-program på ett sätt som undviker en enda maskinvarupunkt.

Även om program körs i virtuella datorer är det viktigt att förstå att Azure Cloud Services tillhandahåller PaaS, inte infrastruktur som en tjänst (IaaS). Här är ett sätt att tänka på det. Med IaaS, till exempel Virtuella Azure-datorer, skapar och konfigurerar du först den miljö som ditt program körs i. Sedan distribuerar du ditt program i den här miljön. Du är ansvarig för att hantera stora delar av den här världen genom att göra saker som att distribuera nya patchade versioner av operativsystemet i varje virtuell dator. I PaaS är det däremot som om miljön redan finns. Allt du behöver göra är att distribuera ditt program. Hantering av plattformen som körs på, inklusive distribution av nya versioner av operativsystemet, hanteras åt dig.

## <a name="scaling-and-management"></a>Skalning och hantering
Med Azure Cloud Services skapar du inte virtuella datorer. I stället anger du en konfigurationsfil som talar om för Azure hur många av varje du vill, till exempel "tre webbrollinstanser" och "två arbetsrollinstanser". Plattformen skapar dem sedan för dig. Du väljer fortfarande [vilken storlek](cloud-services-sizes-specs.md) de bakomda virtuella datorerna ska vara, men du skapar dem inte uttryckligen själv. Om ditt program behöver hantera en större belastning kan du be om fler virtuella datorer och Azure skapar dessa instanser. Om belastningen minskar kan du stänga av dessa instanser och sluta betala för dem.

Ett Azure Cloud Services-program görs vanligtvis tillgängligt för användare via en tvåstegsprocess. En utvecklare laddar först [upp programmet](cloud-services-how-to-create-deploy-portal.md) till plattformens mellanlagringsområde. När utvecklaren är redo att göra programmet live använder de Azure-portalen för att byta mellanlagring med produktion. Den här [växeln mellan mellanlagring och produktion](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) kan göras utan driftstopp, vilket gör att ett program som körs kan uppgraderas till en ny version utan att störa användarna.

## <a name="monitoring"></a>Övervakning
Azure Cloud Services tillhandahåller också övervakning. Precis som virtuella datorer identifieras en misslyckad fysisk server och de virtuella datorerna som kördes på servern på en ny dator startas om. Men Azure Cloud Services identifierar också misslyckade virtuella datorer och program, inte bara maskinvarufel. Till skillnad från virtuella datorer har den en agent i varje webb- och arbetsroll, så det kan starta nya virtuella datorer och programinstanser när fel inträffar.

PaaS-karaktären hos Azure Cloud Services har också andra konsekvenser. En av de viktigaste är att program som bygger på den här tekniken ska skrivas för att köras korrekt när en webb- eller arbetsrollinstans misslyckas. För att uppnå detta bör ett Azure Cloud Services-program inte behålla tillståndet i filsystemet för sina egna virtuella datorer. Till skillnad från virtuella datorer som skapats med virtuella datorer är skrivningar som gjorts till virtuella Azure Cloud Services-datorer inte beständiga. Det finns inget som en virtuell dator datadisk. I stället bör ett Azure Cloud Services-program uttryckligen skriva alla tillstånd till Azure SQL Database, blobbar, tabeller eller någon annan extern lagring. Att skapa program på det här sättet gör dem enklare att skala och mer motståndskraftiga mot fel, vilket båda är viktiga mål för Azure Cloud Services.

## <a name="next-steps"></a>Nästa steg
* [Skapa en molntjänstapp i .NET](cloud-services-dotnet-get-started.md) 
* [Skapa en molntjänstapp i Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Skapa en molntjänstapp i PHP](../cloud-services-php-create-web-role.md) 
* [Skapa en molntjänstapp i Python](cloud-services-python-ptvs.md)






