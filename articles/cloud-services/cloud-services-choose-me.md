---
title: "Alternativ för Azure compute - Azure Cloud Services | Microsoft Docs"
description: "Lär dig mer om Azure compute värd alternativ och hur de fungerar: App Service, Azure-molntjänster och virtuella datorer"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
ms.assetid: ed7ad348-6018-41bb-a27d-523accd90305
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 2871a8c02db0ffc6d9033724e7c9f4a454afef8e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="should-i-choose-azure-cloud-services-or-something-else"></a>Ska jag välja Azure Cloud Services eller något annat?
Är Azure Cloud Services valet för dig? Azure tillhandahåller olika modeller som värd för program som körs. Var och en innehåller en annan uppsättning tjänster. Vilket som du väljer beror på exakt vad du försöker göra.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-azure-cloud-services"></a>Berätta om Azure Cloud Services
Azure Cloud Services är ett exempel på en [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Som [Azure App Service](../app-service/app-service-web-overview.md), den här tekniken är utformad för att stödja program som är skalbar, tillförlitlig och kostnadseffektiv ska fungera. På samma sätt som att Apptjänst är värd för virtuella datorer (VM), så är för Azure Cloud Services. Du har dock mer kontroll över de virtuella datorerna. Du kan installera en egen programvara på virtuella datorer som använder Azure Cloud Services och du kan komma åt dem via fjärranslutning.

![Azure Cloud Services-diagram](./media/cloud-services-choose-me/diagram.png)

Mer kontroll innebär också mindre användarvänlighet. Om du inte behöver ytterligare kontrollalternativ, är det normalt snabbare och enklare att komma ett webbprogram och körs i Web Apps-funktionen i Apptjänst jämfört med Azure Cloud Services.

Det finns två typer av Azure Cloud Services roller. Den enda skillnaden mellan två är hur din roll finns på de virtuella datorerna:

* **Webbroll**: automatiskt distribuerar och appen via IIS-värdar.

* **Worker-rollen**: använder inte IIS och kör din app fristående.

Ett enkelt program kan till exempel använda bara en enda webbrollen, betjänar en webbplats. Ett mer komplext program kan använda en webbroll för att hantera inkommande begäranden från användare och sedan skicka dessa begäranden till en arbetsroll för bearbetning. (Den här kommunikationen kan använda [Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) eller [Azure Queue storage](../storage/common/storage-introduction.md).)

Som i föregående bild föreslår köra alla virtuella datorer i ett enda program i samma molntjänst. Den belastningsutjämnade användare åtkomst program via en offentlig IP-adress, med begäranden laddar automatiskt över programmets virtuella datorer. Plattformen [skalas och distribuerar](cloud-services-how-to-scale-portal.md) de virtuella datorerna i ett Azure Cloud Services-program på ett sätt som förhindrar en enskild felpunkt maskinvara.

Trots att program körs i virtuella datorer, är det viktigt att förstå att Azure Cloud Services tillhandahåller PaaS inte infrastruktur som en tjänst (IaaS). Här är ett sätt att göra det. Med IaaS, till exempel Azure virtuella datorer du först skapa och konfigurera ditt program körs i miljön. Sedan kan du distribuera programmet till den här miljön. Du är ansvarig för att hantera mycket världen, genom att göra saker, till exempel distribuera nya korrigeringsfil versioner av operativsystemet på varje virtuell dator. I PaaS är det däremot som om det redan finns i miljön. Allt du behöver göra är att distribuera ditt program. Hantering av den plattform som den körs på, inklusive distribution av nya versioner av operativsystemet, hanteras åt dig.

## <a name="scaling-and-management"></a>Skalning och hantering
Med Azure Cloud Services, kan du inte skapa virtuella datorer. I stället kan ange du en konfigurationsfil som talar om Azure hur många av var du vill, till exempel ”rollinstanser tre web” och ”två worker rollinstanser”. Plattformen skapar sedan dem åt dig. Du fortfarande välja [hur stor](cloud-services-sizes-specs.md) de säkerhetskopiera virtuella datorer bör vara, men du inte uttryckligen skapa dem själv. Om ditt program behöver hantera ett större belastning kan du be för flera virtuella datorer och Azure skapar dessa instanser. Du kan stänga av dessa instanser och stoppa betalar för dem om belastningen minskar.

Ett program med Azure Cloud Services görs vanligtvis tillgänglig för användare via en tvåstegsprocess. En utvecklare första [överför programmet](cloud-services-how-to-create-deploy-portal.md) plattformens mellanlagringsområdet. När utvecklare kan se programmet live använder de Azure-portalen för att växla mellanlagring till produktionen. Detta [växla mellan mellanlagrings- och](cloud-services-how-to-manage-portal.md#swap-deployments-to-promote-a-staged-deployment-to-production) kan göras utan avbrott, vilket gör att ett program som körs uppgraderas till en ny version utan att störa sina användare.

## <a name="monitoring"></a>Övervakning
Azure Cloud Services ger också övervakning. T.ex. virtuella datorer, identifierar en misslyckad fysisk server och startar om de virtuella datorer som kördes på servern på en ny dator. Men Azure Cloud Services identifierar också misslyckade virtuella datorer och program, inte bara maskinvarufel. Det har en agent i rollerna webb- och arbetsroller till skillnad från virtuella datorer, och det är därför starta nya virtuella datorer och programinstanser när fel uppstår.

PaaS uppbyggnad Azure Cloud Services har andra effekter för. En av de viktigaste är att program som bygger på den här tekniken ska skrivas till fungerar korrekt när alla webb eller arbetare rollinstansen misslyckas. För att uppnå bör inte ett Azure Cloud Services-program upprätthålla tillstånd i filsystemet på sin egen virtuella datorer. Till skillnad från virtuella datorer som skapats med virtuella datorer kan inte skrivningar som gjorts för virtuella datorer i Azure Cloud Services permanent. Det finns inget som en datadisk för virtuella datorer. I stället ett Azure Cloud Services-program bör uttryckligen att skriva alla tillstånd till Azure SQL Database, blobbar, tabeller eller annan extern lagring. Skapa program sätt blir det enklare att skala och mer motståndskraftiga mot fel som är både viktiga mål av Azure-molntjänster.

## <a name="next-steps"></a>Nästa steg
* [Skapa en cloud service-app i .NET](cloud-services-dotnet-get-started.md) 
* [Skapa en cloud service-app i Node.js](cloud-services-nodejs-develop-deploy-app.md) 
* [Skapa en cloud service-app i PHP](../cloud-services-php-create-web-role.md) 
* [Skapa en cloud service-app i Python](cloud-services-python-ptvs.md)



