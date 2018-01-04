---
title: "Prestanda och skalning i varaktiga funktioner – Azure"
description: "Introduktion till tillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: cc4c643b8d0e8de1b5c38ca7bb1b0193d6b0f05b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestanda och skalning i varaktiga funktioner (Azure-funktioner)

För att optimera prestanda och skalbarhet, är det viktigt att förstå de unika skalning egenskaperna för [varaktiga funktioner](durable-functions-overview.md).

Du måste förstå några av information om den underliggande Azure Storage-providern som används av beständiga funktioner för att förstå beteendet skala.

## <a name="history-table"></a>Historiktabellen

Historiktabellen är en Azure Storage-tabell som innehåller Händelsehistorik för alla orchestration-instanser. Eftersom instanser kör läggs nya rader till den här tabellen. Partitionsnyckeln för den här tabellen är härledd från instans-ID för orchestration. Dessa värden är slumpmässig i de flesta fall, vilket säkerställer att optimala distribution av interna partitioner i Azure Storage.

## <a name="internal-queue-triggers"></a>Intern Kölängd utlösare

Orchestrator-funktioner och aktivitet funktioner initieras både av interna köer i appen funktionen standardkontot för lagring. Det finns två typer av köer i varaktiga funktioner: den **kontroll kön** och **arbetsobjekt kön**.

### <a name="the-work-item-queue"></a>Arbetsobjekt kön

Det finns en kö för arbetsobjekt per aktivitet hubben i varaktiga funktioner. Det här är en enkel kö och fungerar på samma sätt som andra `queueTrigger` kön i Azure Functions. Den här kön används för att utlösa tillståndslös *aktivitet funktioner*. När ett program för beständig funktioner skalas ut till flera virtuella datorer kan dessa virtuella datorer som alla konkurrerar att förvärva arbete från arbetsobjekt kön.

### <a name="control-queues"></a>Kontrollen kö(er)

Den *kontroll kön* är mer avancerad än enklare arbetsobjekt kön. Den används för att utlösa tillståndskänslig orchestrator-funktioner. Eftersom instanserna för orchestrator-funktionen är tillståndskänslig singletons, går det inte att använda en konkurrerande konsument-modell för att fördela belastningen över virtuella datorer. I stället orchestrator meddelanden är belastningsutjämning i flera kontrollen köer. Mer information om detta i följande avsnitt.

Kontrollen köer innehåller en mängd olika orchestration livscykel meddelandetyper. Exempel är [orchestrator kontrollmeddelanden](durable-functions-instance-management.md), aktivitet funktionen *svar* meddelanden och meddelanden som timer.

## <a name="orchestrator-scale-out"></a>Orchestrator skalbar

Funktioner för aktiviteten är tillståndslösa och skala ut automatiskt genom att lägga till virtuella datorer. Orchestrator-funktioner, å andra sidan är *partitionerade* över en eller fler köer för kontrollen. Antalet köer för kontrollen är fast och kan inte ändras när du börjar skapa belastningen.

När skala ut till flera funktionen värden instanser (vanligtvis på olika virtuella datorer) hämtar varje instans ett lås på en kontroll köer. Låset innebär att en orchestration-instans kan endast köras på en enda virtuell dator i taget. Detta innebär att om en aktivitet hubb konfigureras med tre kontrollen köer, orchestration-instanser kan vara belastningsutjämnad över upp till tre virtuella datorer. Ytterligare virtuella datorer kan läggas till öka kapaciteten för funktionen aktivitetskörningen.  Men ytterligare resurser används inte för att köra orchestrator-funktioner.

Följande diagram illustrerar hur Azure Functions värden samverkar med lagring entiteter i en miljö som skalats ut.

![Skala diagram](media/durable-functions-perf-and-scale/scale-diagram.png)

Som du kan se kan alla virtuella datorer konkurrerar om meddelanden i kön arbetsobjekt. Dock endast tre virtuella datorer kan hämta meddelanden från kontrollen köer och varje virtuell dator låser en enskild kontroll-kö.

Orchestration-instanser som är fördelade på kontrollen kön instanser genom att köra en intern hash-funktionen mot den orchestration-instansens ID. Instans-ID: N är genereras automatiskt och slumpmässiga som standard som säkerställer att instanser är balanserad över alla tillgängliga kontrollen köer. Aktuella standardantalet stöds kontrollen kön partitioner är **4**.

> [!NOTE]
> Det går för närvarande inte att konfigurera antalet kontrollen kön partitioner i Azure Functions. [För att stödja det här konfigurationsalternativet spåras](https://github.com/Azure/azure-functions-durable-extension/issues/73).

I allmänhet är avsedda att lightweight orchestrator-funktioner och bör mycket datorkraft. Därför är det inte nödvändigt att skapa ett stort antal kontrollen kön partitioner för att hämta mycket bra genomströmning. I stället mesta av tunga arbetet görs i tillståndslös aktivitet funktioner som kan skalas ut oändligt.

## <a name="auto-scale"></a>Autoskala

Som med alla Azure Functions körs i förbrukning planen varaktiga funktioner stöder Autoskala via den [Azure Functions skala controller](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling). Skala Controller övervakar längden på köns arbetsobjekt och alla kontroll-köer att lägga till eller ta bort VM-instanser i enlighet med detta. Om kontrollen kölängder ökar med tiden, fortsätter skala controller att lägga till VM-instanser tills antalet partitioner kontrollen kön. Om arbetsobjektet kölängder ökar med tiden, fortsätter skala controller lägga till VM-instanser tills load, oavsett antalet partitioner kontrollen kön kan matchas.

## <a name="thread-usage"></a>Tråden användning

Orchestrator-funktioner utförs på en enkel tråd. Detta krävs så att körningen av orchestrator-funktionen är deterministisk. Med detta i åtanke är det viktigt att aldrig keep orchestrator fungerar tråd i onödan upptagen med aktiviteter, till exempel i/o (som tillåts för en mängd olika skäl), blockera eller centrifugering åtgärder. Allt arbete som kan kräva att i/o, blockera, eller flera trådar ska flyttas till aktiviteten funktioner.

Aktiviteten har samma funktioner som vanlig kö-utlösta funktioner. Det innebär att de kan på ett säkert sätt göra i/o, utföra CPU-intensiv åtgärder och använder flera trådar. Eftersom aktiviteten utlösare är tillståndslös, kan de fritt skala ut till ett obegränsat antal virtuella datorer.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera tillägget beständiga funktioner och exempel](durable-functions-install.md)
