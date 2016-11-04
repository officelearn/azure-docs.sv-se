---
title: Översikt över Azure Functions| Microsoft Docs
description: Förstå hur du använder Azure Functions för att optimera asynkrona arbetsbelastningar på några få minuter.
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: ''
tags: ''
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur

ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/29/2016
ms.author: cfowler;mahender;glenga

---
# Översikt över Azure Functions
Azure Functions är en lösning för att enkelt köra små delar av kod eller "funktioner," i molnet. Du kan skriva enbart koden som du behöver för det aktuella problemet, utan att bekymra dig över hela programmet eller infrastrukturen för att köra den. Detta kan göra utvecklingen ännu mer produktiv och du kan använda det programmeringsspråk du föredrar, till exempel C#, Node.js, Python eller PHP. Betala endast för tiden koden körs och lita på att Azure skalar enligt behov.

Det här ämnet innehåller en översikt över Azure Functions. Om du vill komma igång snabbt med Azure Functions, börja med att [Skapa din första Azure-funktion](functions-create-first-azure-function.md). Mer teknisk information om Azure Functions finns i [Info för utvecklare](functions-reference.md).

## Funktioner
Här följer några funktioner i Azure Functions:

* **Val av språk** – Skriv funktioner med hjälp av C#, F#, Node.js, Python, PHP, batch, bash, Java eller andra körbara filer.
* **Prismodell – Betala per användning** – Betala endast för tiden som använts för att köra koden. Se alternativet dynamisk apptjänstplan i [prissättningsavsnittet](#pricing) nedan.  
* **Ta med dina egna beroenden** – Functions stöder NuGet och NPM, så du kan använda dina favoritbibliotek.  
* **Integrerad säkerhet** – Skydda HTTP-utlösta funktioner med OAuth-providrar, till exempel Azure Active Directory, Facebook, Google, Twitter och Microsoft Account.  
* **Förenklad integration** – Utnyttja Azure-tjänster och SaaS-erbjudanden (programvara som en tjänst) enkelt. Se [avsnittet Integreringar](#integrations) nedan för några exempel.  
* **Flexibel utveckling** – Koda dina funktioner direkt i portalen eller ställ in kontinuerlig integration och distribuera din kod via GitHub, Visual Studio Team Services och andra [utvecklingsverktyg som stöds](../app-service-web/web-sites-deploy.md#deploy-using-an-ide).  
* **Öppen källkod** – Functions-runtime är öppen källkod och [tillgänglig på GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## Vad kan jag göra med Functions?
Azure Functions är en bra lösning för att behandla data, integrera system, arbeta med Sakernas Internet (IoT) och skapa enkla API:er och mikrotjänster. Functions kan användas för uppgifter som bild- eller orderbearbetning, filunderhåll, långvariga uppgifter som du vill köra i en bakgrundstråd eller för alla aktiviteter som du vill ska köras enligt ett schema. 

Functions innehåller mallar för att komma igång med viktiga scenarier, inklusive följande:

* **BlobTrigger** – Bearbeta Azure Storage-blobbar när de läggs till i behållare. Du kan använda detta för storleksändring.
* **EventHubTrigger** – Svara på händelser som levereras till en Azure-händelsehubb. Särskilt användbart i programinstrumentation, bearbetning av användarupplevelser och arbetsflöden samt scenarier i Sakernas Internet (IoT).
* **Allmän webhook** – Bearbeta webhook-HTTP-begäranden från alla tjänster som stöder webhooks.
* **GitHub-webhook** – Svara på händelser i dina GitHub-databaser. Ett exempel finns i [Skapa en webhook eller API-funktion](functions-create-a-web-hook-or-api-function.md).
* **HTTPTrigger** – Utlösa körning av din kod med hjälp av en HTTP-begäran.
* **QueueTrigger** – Svara på meddelanden som tas emot i en Azure Storage-kö. Ett exempel finns i [Skapa en Azure-funktion som binder till en Azure-tjänst](functions-create-an-azure-connected-function.md).
* **ServiceBusQueueTrigger** – Ansluta din kod till andra Azure-tjänster eller lokala tjänster genom att lyssna på meddelandeköer. 
* **ServiceBusTopicTrigger** – Ansluta din kod till andra Azure-tjänster eller lokala tjänster genom att prenumerera på ämnen. 
* **TimerTrigger** – Köra rensning eller andra batchaktiviteter enligt ett fördefinierat schema. Ett exempel finns i [Skapa en funktion för händelsebearbetning](functions-create-an-event-processing-function.md).

Azure Functions stöder *utlösare*, som kan användas för att starta körning av din kod, och *bindningar*, som kan användas för att förenkla kodning för in- och utdata. En detaljerad beskrivning av utlösare och bindningar i Azure Functions finns i [Azure Functions-utlösare och -bindningar, info för utvecklare](functions-triggers-bindings.md).

## <a name="integrations"></a>Integreringar
Azure Functions kan integreras med en mängd Azure- och tredjepartstjänster. Du kan använda dessa för att utlösa funktionen och starta körning eller för att fungera som indata och utdata för din kod. Följande tjänsteintegreringar stöds av Azure Functions. 

* Azure DocumentDB
* Azure Event Hubs 
* Azure Mobile Apps (tabeller)
* Azure Notification Hubs
* Azure Service Bus (köer och ämnen)
* Azure Storage (blob, köer och tabeller) 
* GitHub (webhooks)
* Lokal (med hjälp av Service Bus)

## <a name="pricing"></a>Hur mycket kostar Functions?
Azure Functions har två typer av prissättningar, välj den som bäst passar dina behov: 

* **Dynamisk värdplan** – När din funktion körs, tillhandahåller Azure alla nödvändiga beräkningsresurser. Du behöver inte bry dig om resurshantering, och du betalar endast för den tid då din kod körs. Fullständig prisinformation är tillgänglig på sidan [Prissättning för Functions](/pricing/details/functions). 
* **Apptjänstplan** – Kör dina funktioner precis som dina webb-, mobil- och API Apps. När du redan använder App Service för dina andra program, kan du köra dina funktioner på samma plan utan extra kostnad. Fullständig information finns på sidan [Prissättning för App Service](/pricing/details/app-service/).

Mer information om att skala dina funktioner finns i [Så här skalar du Azure Functions](functions-scale.md).

## Nästa steg
* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)  
  Kom igång snabbt och skapa din första funktion med hjälp av Azure Functions-snabbstart. 
* [Azure Functions, info för utvecklare](functions-reference.md)  
  Ger mer teknisk information om Azure Functions-runtime och en referens för kodning av funktioner och definiering av utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive den dynamiska serviceplanen, och hur du väljer rätt plan. 
* [Läs mer om Azure App Service](../app-service/app-service-value-prop-what-is.md)  
  Azure Functions utnyttjar plattformen Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik. 

<!--HONumber=Sep16_HO3-->


