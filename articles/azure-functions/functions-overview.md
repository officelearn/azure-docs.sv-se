---
title: Översikt över Azure Functions
description: Förstå hur du använder Azure Functions för att optimera asynkrona arbetsbelastningar på några få minuter.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621010"
---
# <a name="an-introduction-to-azure-functions"></a>En introduktion till Azure Functions

Med Azure Functions kan du köra små delar av kod (kallas "funktioner") utan att oroa dig för program infrastrukturen. Med Azure Functions tillhandahåller moln infrastrukturen alla uppdaterade servrar som du behöver för att hålla ditt program i stor skala.

En funktion är "utlöst" av en speciell typ av händelse. [Utlösare som stöds](./functions-triggers-bindings.md) innehåller svar på ändringar i data, svarar på meddelanden, körs enligt ett schema eller till följd av en http-begäran.

Även om du alltid kan koda direkt mot en myriaden av tjänster, effektiviseras integreringen med andra tjänster med hjälp av bindningar. Med bindningar får du [en deklarativ till gång till en mängd olika Azure-tjänster och tjänster från tredje part](./functions-triggers-bindings.md).

## <a name="features"></a>Funktioner

Några viktiga funktioner i Azure Functions är:

- **Program utan server**: med funktioner kan du utveckla program utan [Server](https://azure.microsoft.com/solutions/serverless/) på Microsoft Azure.

- **Val av språk**: Skriv funktioner med ditt val av [ C#, Java, Java Script, python och PowerShell](supported-languages.md).

- **Pris modell för betala per användning**: betala bara för den tid som krävs för att köra koden. Se alternativet för förbrukningsvärdplan i [prissättningsavsnittet](#pricing).  

- **Ta med dina egna beroenden**: Functions har stöd för NUGET och NPM, vilket ger dig till gång till dina favorit bibliotek.

- **Integrerad säkerhet**: skydda http-utlösta funktioner med OAuth-leverantörer som Azure Active Directory, Facebook, Google, Twitter och Microsoft-konto.

- **Förenklad integrering**: integrera enkelt med Azure-tjänster och SaaS-erbjudanden (Software-as-a-Service).

- **Flexibel utveckling**: Konfigurera kontinuerlig integrering och distribuera din kod via [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)och andra utvecklingsverktyg som [stöds](../app-service/deploy-local-git.md).

- **Tillstånds känslig arkitektur utan server**: dirigera program utan server med [Durable Functions](durable/durable-functions-overview.md).

- **Öppen källkod**: Functions runtime är öppen källkod och [finns på GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Vad kan jag göra med Functions?

Functions är en bra lösning för att bearbeta Mass data, integrera system, arbeta med sakernas internet (IoT) och skapa enkla API: er och Micro-tjänster.

Det finns en serie mallar som hjälper dig att komma igång med viktiga scenarier, inklusive:

- **Http**: kör kod baserat på [http-begäranden](functions-create-first-azure-function.md)

- **Timer**: schema kod som ska [köras vid fördefinierade tidpunkter](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: bearbeta [nya och ändrade Azure Cosmos DB dokument](./functions-create-cosmos-db-triggered-function.md)

- **Blob Storage**: bearbeta [nya och ändrade Azure Storage blobbar](./functions-create-storage-blob-triggered-function.md)

- **Queue Storage**: svara på [Azure Storage köa meddelanden](./functions-create-storage-queue-triggered-function.md)

- **Event Grid**: svara på [Azure Event Grid händelser via prenumerationer och filter](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Event Hub**: svara på [hög volym av Azure Event Hub-händelser](./functions-bindings-event-hubs.md)

- **Service Bus kö**: Anslut till andra Azure-tjänster eller lokala tjänster genom att [svara Service Bus Queue meddelanden](./functions-bindings-service-bus.md)

- **Service Bus ämnet**: Anslut andra Azure-tjänster eller lokala tjänster genom [att svara på Service Bus ämnes meddelanden](./functions-bindings-service-bus.md)

## <a name="pricing"></a>Hur mycket kostar Functions?

Azure Functions har tre typer av pris planer. Välj den som passar dina behov bäst:

- **Förbruknings plan**: Azure tillhandahåller alla nödvändiga beräknings resurser. Du behöver inte oroa dig för resurs hantering och bara betala för den tid som koden körs.

- **Premium plan**: du anger ett antal förvärmade instanser som alltid är online och som är redo att omedelbart svara. När din funktion körs tillhandahåller Azure eventuella ytterligare beräknings resurser som behövs. Du betalar för de förvärmade instanserna som körs kontinuerligt och eventuella ytterligare instanser som du använder som Azure skalar din app i och ut.

- **App Service plan**: Kör funktioner precis som dina webb program. Om du använder App Service för andra program kan funktionerna köras i samma plan utan extra kostnad.

Mer information om värdplaner finns i [Azure Functions hosting plan comparison](functions-scale.md) (Jämförelse av värdplaner för Azure Functions). Fullständig prisinformation är tillgänglig på sidan [Prissättning för Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Nästa steg

- [Skapa din första Azure-funktion](functions-create-first-function-vs-code.md)  
  Kom igång med [Visual Studio Code](functions-create-first-function-vs-code.md), [kommando raden](functions-create-first-azure-function-azure-cli.md)eller Använd [Azure Portal](functions-create-first-azure-function.md).

- [Azure Functions, info för utvecklare](functions-reference.md)  
  Ger mer teknisk information om Azure Functions-runtime och en referens för kodning av funktioner och definiering av utlösare och bindningar.

- [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive värdplanen för förbrukning, och hur du väljer rätt plan.

- [Läs mer om Azure App Service](../app-service/overview.md)  
  Azure Functions använder Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik.
