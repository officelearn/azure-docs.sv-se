---
title: Vad är Azure Cloud Services | Microsoft Docs
description: Veta mer om Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ce88dcaedf32f293fc121cda2a088388c99badee
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603835"
---
# <a name="overview-of-azure-cloud-services"></a>Översikt över Azure Cloud Services
Azure Cloud Services är ett exempel på en [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Som [Azure App Service](../app-service/overview.md), den här tekniken är utformad för att stödja program som är skalbara, tillförlitliga och billiga att hantera. På samma sätt som att App Service finns på virtuella datorer (VM), så är för Azure Cloud Services. Du kan dock mer kontroll över de virtuella datorerna. Du kan installera din egen programvara på virtuella datorer som använder Azure Cloud Services och du kan komma åt dem via en fjärranslutning.

![Azure Cloud Services-diagram](./media/cloud-services-choose-me/diagram.png)

Mer kontroll innebär även mindre användarvänlighet. Om du inte behöver alternativ för ytterligare kontroll, det är normalt snabbare och enklare att få ett webbprogram och som körs i Web Apps-funktionen i App Service jämfört med Azure molntjänster.

Det finns två typer av Azure Cloud Services-roller. Den enda skillnaden mellan två är hur din roll finns på de virtuella datorerna:

* **Webbroll**: Distribuerar och är värd för din app via IIS automatiskt.

* **Arbetsroll**: Använder inte IIS och kör din app fristående.

Ett enkelt program kan exempelvis använda bara en enda web-roll, som en webbplats. Ett mer komplext program kan använda en webbroll för att hantera inkommande begäranden från användare och skicka dessa begäranden till en arbetsroll för bearbetning. (Den här kommunikationen kan använda [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) eller [Azure Queue storage](../storage/common/storage-introduction.md).)

Som bilden ovan antyder kör alla virtuella datorer i ett enda program i samma molntjänst. Användare åtkomst till programmet via en enda offentlig IP-adress med begäranden läsa in balanserade över programmets virtuella datorer. Plattformen [kan skalas och distribuerar](cloud-services-how-to-scale-portal.md) de virtuella datorerna i ett Azure Cloud Services-program på ett sätt som förhindrar en enskild felpunkt maskinvara.

Trots att program körs i virtuella datorer, är det viktigt att förstå att Azure molntjänster erbjuder PaaS, inte infrastruktur som en tjänst (IaaS). Här är ett sätt att tänka på den. Med IaaS, till exempel Azure Virtual Machines kan du först skapa och konfigurera ditt program körs i miljön. Sedan distribuerar du programmet till den här miljön. Du är ansvarig för att hantera mycket av den här världen genom att göra sådant som att distribuera nya mest uppdaterade versioner av operativsystemet på varje virtuell dator. I PaaS är det däremot som om miljön redan finns. Allt du behöver göra är att distribuera ditt program. Hantering av plattformen den körs på, inklusive distribution av nya versioner av operativsystemet, hanteras åt dig.

## <a name="scaling-and-management"></a>Skalning och hantering
Med Azure Cloud Services, kan du inte skapa virtuella datorer. Istället tillhandahåller du en konfigurationsfil som meddelar Azure hur många av var du vill, till exempel ”den tre webbrollinstanserna” och ”två arbetsrollinstanser”. Plattformen skapar sedan dem åt dig. Du fortfarande välja [vilken storlek](cloud-services-sizes-specs.md) de säkerhetskopiering av virtuella datorer ska vara, men du inte uttryckligen skapa dem själv. Om programmet behöver för att hantera ett större belastning, kan du be fler virtuella datorer och Azure skapar dessa instanser. Om belastningen minskar, kan du stänga av dessa instanser och stoppa betala för dem.

Ett Azure Cloud Services-program är vanligtvis göras tillgängliga för användare via en tvåstegsprocess. En utvecklare första [laddar upp programmet](cloud-services-how-to-create-deploy-portal.md) till plattformens mellanlagringsområde. När utvecklaren kan se programmet live, de använder Azure-portalen för att växla mellan mellanlagring med produktion. Detta [växlar du mellan mellanlagring och produktion](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) kan göras utan avbrott, vilket gör att ett program som körs uppgraderas till en ny version utan att störa dess användare.

## <a name="monitoring"></a>Övervakning
Azure Cloud Services tillhandahåller också övervakning. Precis som virtuella datorer, den identifierar en fysisk server som inte och startar om de virtuella datorer som kördes på servern på en ny dator. Men Azure Cloud Services identifierar också misslyckade virtuella datorer och program, inte bara maskinvarufel. Den har en agent i varje webb- och worker-roll till skillnad från virtuella datorer, och det är därför starta nya virtuella datorer och instanser av programmet när fel uppstår.

PaaS-typen av Azure Cloud Services har för andra effekter. En av de viktigaste är att program som bygger på den här tekniken ska skrivas till fungerar korrekt när alla web- eller worker-rollinstans misslyckas. För att uppnå detta bör inte ett Azure Cloud Services-program har tillstånd i filsystemet för dess egna virtuella datorer. Till skillnad från virtuella datorer som skapas med virtuella datorer, inte skrivningar som görs till virtuella datorer i Azure Cloud Services är permanent. Det är något som en datadisk för virtuella datorer. I stället ett Azure Cloud Services-program bör uttryckligen att skriva alla tillstånd till Azure SQL Database, blobbar, tabeller eller andra extern lagring. Att skapa program på så sätt blir det enklare att skala och mer motståndskraftiga mot fel som är både viktiga mål för Azure Cloud Services.

## <a name="next-steps"></a>Nästa steg
* [Skapa en molntjänstapp i .NET](cloud-services-dotnet-get-started.md) 
* [Skapa en molntjänstapp i Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Skapa en molntjänstapp i PHP](../cloud-services-php-create-web-role.md) 
* [Skapa en molntjänstapp i Python](cloud-services-python-ptvs.md)



