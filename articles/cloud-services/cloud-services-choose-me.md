---
title: "Alternativ för Azure compute - molntjänster | Microsoft Docs"
description: "Lär dig mer om Azure compute värd alternativ och hur de fungerar: Apptjänst molntjänster och virtuella datorer"
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
ms.openlocfilehash: 17ecf39128994dad93f017f87f105254f3017230
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="should-i-choose-cloud-services-or-something-else"></a>Ska jag välja molntjänster eller något annat?
Är Azure Cloud Services valet för dig? Azure tillhandahåller olika modeller som värd för program som körs. Var och en innehåller en annan uppsättning tjänster, så att du väljer en beroende exakt vad du försöker göra.

[!INCLUDE [compute-table](../../includes/compute-options-table.md)]

<a name="tellmecs"></a>

## <a name="tell-me-about-cloud-services"></a>Berätta om molntjänster
Molntjänster är ett exempel på [plattform som en tjänst](https://azure.microsoft.com/overview/what-is-paas/) (PaaS). Som [Apptjänst](../app-service/app-service-web-overview.md), den här tekniken är utformad för att stödja program som är skalbara, tillförlitliga och billig ska fungera. Precis som en Apptjänst är värd för virtuella datorer, så är för molntjänster, men har du större kontroll över de virtuella datorerna. Du kan installera en egen programvara på virtuella datorer för molnet tjänsten och du kan fjärranslutna i dem.

![cs_diagram](./media/cloud-services-choose-me/diagram.png)

Mer kontroll innebär också mindre användarvänlighet. Om du inte behöver ytterligare kontrollalternativ, är det normalt snabbare och enklare att komma ett webbprogram och körs i Web Apps i Apptjänst jämfört med molntjänster.

Det finns två typer av Molntjänsten roller. Den enda skillnaden mellan två är hur din roll finns på den virtuella datorn.

* **Webbroll**  
Distribuerar och är värd för din app via IIS automatiskt.

* **Arbetsroll**  
Använder inte IIS och kör din app fristående.

Ett enkelt program kan till exempel använda bara en enda webbrollen, betjänar en webbplats. Ett mer komplext program kanske använder en webbroll för att hantera inkommande begäranden från användare och sedan skicka dessa begäranden till en arbetsroll för bearbetning. (Den här kommunikationen kan använda [Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md) eller [Azure köer](../storage/common/storage-introduction.md).)

Som i föregående bild föreslår köra alla virtuella datorer i ett enda program i samma molntjänst. Den belastningsutjämnade användare åtkomst program via en offentlig IP-adress, med begäranden laddar automatiskt över programmets virtuella datorer. Plattformen [skalas och distribuerar](cloud-services-how-to-scale-portal.md) de virtuella datorerna i ett Cloud Services-program på ett sätt som förhindrar en enskild felpunkt maskinvara.

Trots att program körs i virtuella datorer, är det viktigt att förstå att molntjänster tillhandahåller PaaS, inte IaaS. Här är ett sätt att göra det: med IaaS, till exempel Azure virtuella datorer du först skapa och konfigurera ditt program körs i miljön och sedan distribuera programmet till den här miljön. Du är ansvarig för att hantera mycket världen, gör saker, till exempel distribuera nya korrigeringsfil versioner av operativsystemet på varje virtuell dator. I PaaS är det däremot som om det redan finns i miljön. Allt du behöver göra är att distribuera ditt program. Hantering av den plattform som den körs på, inklusive distribution av nya versioner av operativsystemet, hanteras åt dig.

## <a name="scaling-and-management"></a>Skalning och hantering
Med Cloud Services, kan du inte skapa virtuella datorer. I stället kan du ange en konfigurationsfil som talar om Azure hur många av var du vill, t.ex **tre webbrollsinstanser** och **två worker rollinstanser**, och plattformen som skapar dem åt dig.  Du fortfarande välja [hur stor](cloud-services-sizes-specs.md) de säkerhetskopiera virtuella datorer bör vara, men du inte uttryckligen skapa dem själv. Om ditt program behöver hantera ett större belastning kan du be för flera virtuella datorer och Azure skapar dessa instanser. Du kan stänga av dessa instanser och stoppa betalar för dem om belastningen minskar.

Ett program för molntjänster görs vanligtvis tillgängligt för användare via en tvåstegsprocess. En utvecklare första [överför programmet](cloud-services-how-to-create-deploy-portal.md) plattformens mellanlagringsområdet. När utvecklare kan se programmet live använder de Azure-portalen för att växla mellanlagring till produktionen. Detta [växla mellan mellanlagrings- och](cloud-services-how-to-manage-portal.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production) kan göras utan avbrott, vilket gör att ett program som körs uppgraderas till en ny version utan att störa sina användare.

## <a name="monitoring"></a>Övervakning
Cloud Services ger också övervakning. Som Azure Virtual Machines, den identifierar en misslyckad fysisk server och startar om de virtuella datorer som kördes på servern på en ny dator. Men molntjänster identifierar också misslyckade virtuella datorer och program, inte bara maskinvarufel. Det har en agent i rollerna webb- och arbetsroller till skillnad från virtuella datorer, och det är därför starta nya virtuella datorer och programinstanser när fel uppstår.

PaaS-typ av molntjänster har andra effekter för. En av de viktigaste är att program som bygger på den här tekniken ska skrivas till fungerar korrekt när alla webb eller arbetare rollinstansen misslyckas. För att uppnå bör inte ett Cloud Services-program upprätthålla tillstånd i filsystemet på sin egen virtuella datorer. Till skillnad från virtuella datorer som skapades med Azure Virtual Machines, skrivningar som gjorts i Cloud Services virtuella datorer inte är beständig; Det finns inget som en datadisk för virtuella datorer. I stället ett Cloud Services-program bör uttryckligen att skriva alla tillstånd till SQL-databas, blobbar, tabeller eller annan extern lagring. Skapa program sätt gör dem lättare att skala och mer motståndskraftiga mot fel, både viktiga mål av molntjänster.

## <a name="next-steps"></a>Nästa steg
[Skapa en cloud service-app i .NET](cloud-services-dotnet-get-started.md)  
[Skapa en cloud service-app i Node.js](cloud-services-nodejs-develop-deploy-app.md)  
[Skapa en cloud service-app i PHP](../cloud-services-php-create-web-role.md)  
[Skapa en cloud service-app i Python](cloud-services-python-ptvs.md)

