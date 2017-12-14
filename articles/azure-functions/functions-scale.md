---
title: "Azure Functions skala och vara värd för | Microsoft Docs"
description: "Lär dig mer om att välja mellan Azure Functions förbrukning plan och App Service-plan."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure funktioner, funktioner, förbrukning plan, apptjänstplan, händelsebearbetning, webhooks, dynamiska beräkning, serverlösa arkitektur"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 38499fd1e27cf6e8253ad1172701fd18b338abad
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions skala och vara värd för

Du kan köra Azure Functions i två olika lägen: förbrukning planerings- och Azure App Service-plan. Förbrukning planen tilldelar automatiskt datorkraft när koden körs skalas ut som behövs för att hantera belastningen och skalas när koden inte körs. Du behöver inte betala för inaktiv virtuella datorer och behöver inte reserverad kapacitet i förväg. Den här artikeln fokuserar på förbrukning-plan en [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) appmodell. Mer information om hur programtjänstplanen fungerar finns i [Azure App Service-planer djupgående översikt över](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

>[!NOTE]  
> [Linux-värd](functions-create-first-azure-function-azure-cli-linux.md) finns för närvarande endast i en apptjänstplan.

Om du inte är bekant med Azure Functions finns i [översikt över Azure Functions](functions-overview.md).

När du skapar en funktionsapp, måste du konfigurera en värd plan för funktioner som innehåller appen. I båda lägena, en instans av den *Azure Functions värden* kör funktionerna. Typ av planen kontroller:

* Hur värdinstanser skalas ut.
* De resurser som är tillgängliga för varje värd.

För närvarande måste du välja vilken typ av värd plan under genereringen av funktionen appen. Du kan inte ändra efteråt. 

Du kan skala mellan nivåer att tilldela olika mängden resurser på en App Service-plan. Planen förbrukning hanterar Azure Functions automatiskt alla resursallokering.

## <a name="consumption-plan"></a>Förbrukningsplan

När du använder en plan för förbrukning läggs instanser av Azure Functions värden dynamiskt och tas bort baserat på antalet inkommande händelser. Den här planen skalar automatiskt och du debiteras för beräkningsresurser bara när din funktion körs. På en plan för förbrukning köra en funktion för högst 10 minuter. 

> [!NOTE]
> Standardvärdet för timeout för funktioner på en plan för förbrukning är 5 minuter. Värdet kan ökas till 10 minuter för funktionen appen genom att ändra egenskapen `functionTimeout` i den [host.json](functions-host-json.md#functiontimeout) projektfilen.

Fakturering baseras på antalet körningar, körningstid och minne som används. Fakturering sammanställs över alla funktioner i en funktionsapp. Mer information finns i [Azure Functions sida med priser].

Förbrukning planen är standard värd plan och har följande fördelar:
- Betala endast när din funktion körs.
- Skala ut automatiskt, även under perioder med hög att läsa in.

## <a name="app-service-plan"></a>App Service-plan

I App Service-plan körs funktionen apparna på dedikerade virtuella datorer på Basic, Standard, Premium och isolerade SKU: er, liknande Web Apps, API Apps och Mobilappar. Dedikerade virtuella datorer är allokerade till din Apptjänst-appar, vilket innebär att funktioner värden körs alltid. Programtjänstplaner stöd för Linux.

Överväg att en apptjänstplan i följande fall:
- Du har befintliga, underutnyttjade virtuella datorer som redan kör andra Apptjänst-instanser.
- Du förväntar dig att dina appar funktionen kör kontinuerligt eller nästan kontinuerligt. I det här fallet blir en Apptjänstplan mer kostnadseffektiv.
- Du behöver fler alternativ för CPU eller minne än vad som tillhandahålls av förbrukning planen.
- Du behöver köra längre än den maximala körningstiden tillåtna förbrukning planen (i 10 minuter).
- Du behöver funktioner som bara är tillgängligt på en apptjänstplan, till exempel stöd för Apptjänst-miljön, VNET/VPN-anslutning och större VM-storlekar. 
- Du vill köra appen funktionen i Linux eller vill du ange en anpassad avbildning som ska köra dina funktioner.

En virtuell dator frikopplar kostnaden från antal körningar, körningstid och minne som används. Därför kan betalar du inte mer än kostnaden för VM-instans som du allokera. Mer information om hur programtjänstplanen fungerar finns i [Azure App Service-planer djupgående översikt över](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Du kan skala ut manuellt genom att lägga till flera VM-instanser med en App Service-plan eller du kan aktivera Autoskala. Mer information finns i [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json). Du kan även skala upp genom att välja en annan programtjänstplan. Mer information finns i [skala upp en app i Azure](../app-service/web-sites-scale.md). 

Om du planerar att köra JavaScript-funktioner på en apptjänstplan, bör du välja en plan som har färre vCPUs. Mer information finns i [Välj enkel kärna programtjänstplaner](functions-reference-node.md#considerations-for-javascript-functions).  

<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 
<a name="always-on"></a>
###Always On

Om du kör på en apptjänstplan, bör du aktivera den **alltid på** inställningen så att funktionen appen körs korrekt. På en apptjänstplan går functions-runtime inaktiv efter några minuter av inaktivitet, så att endast HTTP-utlösare kommer ”väcka” dina funktioner. Detta liknar hur WebJobs måste ha alltid aktiverat. 

Always On är bara tillgängliga på en App Service-plan. På en plan för förbrukning aktiverar plattformen funktionen appar automatiskt.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

På en plan för användning eller i en apptjänstplan kräver en funktionsapp en allmän Azure Storage-konto som stöder Azure Blob, köer, filer och tabellen lagring. Azure Functions används internt, Azure Storage för åtgärder som hanterar utlösare och loggning funktionen körningar. Vissa storage-konton stöder inte köer och tabeller, t.ex endast blob storage-konton (inklusive premium-lagring) och allmänna lagringskonton med zonredundant lagringsreplikering. De här kontona filtrerat från den **Lagringskonto** bladet när du skapar en funktionsapp.

<!-- JH: Does using a PRemium Storage account improve perf? -->

Läs mer om lagringskontotyper i [introduktion till Azure Storage-tjänster](../storage/common/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="how-the-consumption-plan-works"></a>Så här fungerar förbrukning planen

I planen förbrukning skalas skala domänkontrollant automatiskt Processortid och minnesresurser genom att lägga till ytterligare instanser av funktioner värden baserat på antalet händelser som utlöses på dess funktioner. Varje instans av funktioner värden är begränsad till 1,5 GB minne.  En instans av värden är funktionen appen, vilket innebär alla funktioner i en funciton app dela resurser i en annan instans och skala på samma gång.

När du använder förbrukningen värd plan som funktionen kodfiler lagras på Azure-filresurser för den funktionen main storage-konto. När du tar bort huvudsakliga lagringskontot för funktionen appen kodfiler funktionen tas bort och kan inte återställas.

> [!NOTE]
> När du använder en blob-utlösare på en plan för förbrukning, kan det finnas upp till en 10 minuters fördröjning vid bearbetningen av nya blobbar om en funktionsapp är inaktiv. När funktionen appen körs bearbetas blobbar omedelbart. Överväg att något av följande alternativ för att undvika den här första fördröjningen:
> - Värd för appen funktionen på en apptjänstplan med alltid på aktiverad.
> - Använd en annan funktion för att utlösa blob bearbetning, till exempel en händelse rutnätet prenumeration eller ett meddelande i kön som innehåller blob-namnet. Ett exempel finns i [C#-skript och JavaScript-exempel för blobben indata och utdata bindningar](functions-bindings-storage-blob.md#input--output---example).

### <a name="runtime-scaling"></a>Runtime skalning

Azure Functions använder en komponent som kallas den *skala controller* att övervaka antalet händelser och avgöra om du vill skala upp eller skala. Skala domänkontrollanten använder heuristik för varje typ av utlösare. Till exempel när du använder en Azure Queue storage-utlösare skalningen utifrån kölängden och ålder äldsta meddelandet i kön.

Skala är funktionen appen. När appen funktionen skalas ut resurser ytterligare för att köra flera instanser av Azure Functions-värden. Däremot bort skala controller funktionen värddatorinstanser som compute begäran minskas. Antalet instanser skalas till slut ned till noll när inga funktioner som körs inom en funktionsapp.

![Skala controller händelseövervakning och skapa instanser](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Förstå skalningen beteenden

Skalning kan variera på ett antal faktorer och skala på olika sätt beroende på utlösare och språket som valts. Men det finns några aspekter av skalning som finns i systemet idag:
* En enda funktionsapp skalar endast till högst 200 instanser. En enda instans kan bearbeta mer än ett meddelande eller begäran i taget, så att det inte finns en uppsättning gränsen för antalet samtidiga körningar.
* Nya instanser allokeras endast högst en gång var 10: e sekund.

Olika utlösare kan också ha olika skala gränser som dokumenterade nedan:

* [Händelsehubb](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Bästa praxis och mönster för skalbara appar

Det finns många aspekter av en funktionsapp som påverkar hur väl den skalar, inklusive Värdkonfiguration, runtime storleken och resursen effeciency.  Visa den [skalbarhet avsnitt i artikeln prestanda överväganden](functions-best-practices.md#scalability-best-practices) för mer information.

### <a name="billing-model"></a>Faktureringsmodell

Faktureringen för förbrukning planen beskrivs i detalj på den [Azure Functions sida med priser]. Användning sammanställs på funktionen app-nivå och räknar den tid som funktionskod körs. Följande är enheter för fakturering: 
* **Resursförbrukning i GB-sekunder (GB-s)**. Beräknad som en kombination av minnesstorlek och körningstiden för alla funktioner i en funktionsapp. 
* **Körningar**. Räknas varje gång en funktion körs som svar på en händelseutlösare.

[Azure Functions sida med priser]: https://azure.microsoft.com/pricing/details/functions
