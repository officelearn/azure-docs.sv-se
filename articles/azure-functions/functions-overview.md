---
title: "Översikt över Azure Functions| Microsoft Docs"
description: "Förstå hur du använder Azure Functions för att optimera asynkrona arbetsbelastningar på några få minuter."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 41f26a4b03a6431aaad21bda6336b8840d2d923f
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="an-introduction-to-azure-functions"></a>En introduktion till Azure Functions  
Azure Functions är en lösning för att enkelt köra små delar av kod eller "funktioner," i molnet. Du kan skriva enbart koden som du behöver för det aktuella problemet, utan att bekymra dig över hela programmet eller infrastrukturen för att köra den. Funktioner kan göra utvecklingen ännu mer produktiv och du kan använda programmeringsspråk du föredrar, till exempel C#, F #, Node.js, Java eller PHP. Betala endast för tiden koden körs och lita på att Azure skalar enligt behov. Azure Functions kan du utveckla [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) program i Microsoft Azure.

Det här ämnet innehåller en översikt över Azure Functions. Om du vill komma igång snabbt och kom igång med funktionerna som börjar med [skapa din första Azure-funktion](functions-create-first-azure-function.md). Mer teknisk information om Azure Functions finns i [Info för utvecklare](functions-reference.md).

## <a name="features"></a>Funktioner
Här följer några funktioner i funktioner:

* **Val av språk** – Skriv funktioner med hjälp av C#, F #, Node.js, Java, PHP, batch, bash eller körbara filer.
* **Prismodell – Betala per användning** – Betala endast för tiden som använts för att köra koden. Se alternativet för förbrukningsvärdplan i [prissättningsavsnittet](#pricing).  
* **Ta med dina egna beroenden** – Functions stöder NuGet och NPM, så du kan använda dina favoritbibliotek.  
* **Integrerad säkerhet** – Skydda HTTP-utlösta funktioner med OAuth-providrar, till exempel Azure Active Directory, Facebook, Google, Twitter och Microsoft Account.  
* **Förenklad integration** – Utnyttja Azure-tjänster och SaaS-erbjudanden (programvara som en tjänst) enkelt. I [avsnittet Integreringar](#integrations) finns några exempel.  
* **Flexibel utveckling** – koda dina funktioner direkt i portalen eller Ställ in kontinuerlig integration och distribuera din kod via [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md), och andra [utvecklingsverktyg som stöds](../app-service/app-service-deploy-local-git.md).  
* **Öppen källkod** – Functions-runtime är öppen källkod och [tillgänglig på GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Vad kan jag göra med Functions?
Functions är en bra lösning för databearbetning, integrera system, arbeta med-sakernas internet (IoT) och skapa enkla API: er och mikrotjänster. Functions kan användas för uppgifter som avbildnings- eller orderbearbetning, filunderhåll eller för alla aktiviteter som du vill ska köras enligt ett schema. 

Functions innehåller mallar för att komma igång med viktiga scenarier, inklusive följande:

* **HTTPTrigger** – Utlösa körning av din kod med hjälp av en HTTP-begäran. Ett exempel finns [skapa din första funktion](functions-create-first-azure-function.md).
* **TimerTrigger** – Köra rensning eller andra batchaktiviteter enligt ett fördefinierat schema. Ett exempel finns [skapa en funktion som utlöses av en timer](functions-create-scheduled-function.md).
* **GitHub-webhook** – Svara på händelser i dina GitHub-databaser. Ett exempel finns [skapa en funktion som utlöses av en GitHub-webhook](functions-create-github-webhook-triggered-function.md).
* **Allmän webhook** – Bearbeta webhook-HTTP-begäranden från alla tjänster som stöder webhooks. Ett exempel finns [skapa en funktion som utlöses av en allmän webhook](functions-create-generic-webhook-triggered-function.md).
* **CosmosDBTrigger** -processen Azure Cosmos DB dokument när de läggs till eller uppdateras i samlingar i en NoSQL-databas. Ett exempel finns [skapa en funktion som utlöses av Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md).
* **BlobTrigger** – Bearbeta Azure Storage-blobbar när de läggs till i behållare. Du kan använda den här funktionen för storleksändring. Mer information finns i [Blob storage-bindningar](functions-bindings-storage-blob.md).
* **QueueTrigger** – Svara på meddelanden som tas emot i en Azure Storage-kö. Ett exempel finns [skapa en funktion som utlöses av Azure Queue storage](functions-create-storage-queue-triggered-function.md).
* **EventHubTrigger** – Svara på händelser som levereras till en Azure-händelsehubb. Särskilt användbart i programinstrumentation, bearbetning av användarupplevelser och arbetsflöden samt scenarier i Sakernas Internet (IoT). Mer information finns i [Händelsehubbar bindningar](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** – Ansluta din kod till andra Azure-tjänster eller lokala tjänster genom att lyssna på meddelandeköer. Mer information finns i [Service Bus-bindningar](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger** – Ansluta din kod till andra Azure-tjänster eller lokala tjänster genom att prenumerera på ämnen. Mer information finns i [Service Bus-bindningar](functions-bindings-service-bus.md).

Azure Functions stöder *utlösare*, som kan användas för att starta körning av din kod, och *bindningar*, som kan användas för att förenkla kodning för in- och utdata. En detaljerad beskrivning av utlösare och bindningar i Azure Functions finns i [Azure Functions-utlösare och -bindningar, info för utvecklare](functions-triggers-bindings.md).

## <a name="integrations"></a>Integreringar
Azure Functions kan integreras med en mängd Azure- och tredjepartstjänster. Du kan använda tjänsterna för att utlösa funktionen och starta körning, eller så kan de fungera som indata och utdata för din kod. Följande tjänsteintegreringar stöds av Azure Functions:

* Azure Cosmos DB
* Azure Event Hubs 
* Azure Event Grid
* Azure Mobile Apps (tabeller)
* Azure Notification Hubs
* Azure Service Bus (köer och ämnen)
* Azure Storage (blob, köer och tabeller) 
* GitHub (webhooks)
* Lokal (med hjälp av Service Bus)
* Twilio (SMS-meddelanden)

## <a name="pricing"></a>Hur mycket kostar Functions?
Azure Functions har två typer av prissättningar. Välj det alternativ som bäst passar dina behov: 

* **Förbrukningsplan** – När din funktion körs tillhandahåller Azure alla nödvändiga beräkningsresurser. Du behöver inte bry dig om resurshantering, och du betalar endast för den tid då din kod körs. 
* **Apptjänstplan** – Kör dina funktioner precis som dina webb-, mobil- och API Apps. När du redan använder App Service för dina andra program, kan du köra dina funktioner på samma plan utan extra kostnad. 

Mer information om planer finns [Azure Functions värd plan jämförelse](functions-scale.md). Fullständig prisinformation är tillgänglig på sidan [Prissättning för Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Nästa steg
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)  
  Kom igång snabbt och skapa din första funktion med hjälp av Azure Functions-snabbstart. 
* [Azure Functions, info för utvecklare](functions-reference.md)  
  Ger mer teknisk information om Azure Functions-runtime och en referens för kodning av funktioner och definiering av utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive värdplanen för förbrukning, och hur du väljer rätt plan. 
* [Läs mer om Azure App Service](../app-service/app-service-web-overview.md)  
  Azure Functions utnyttjar Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik. 

