---
title: Översikt över Azure Functions
description: Förstå hur du använder Azure Functions för att optimera asynkrona arbetsbelastningar på några få minuter.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621010"
---
# <a name="an-introduction-to-azure-functions"></a>En introduktion till Azure Functions

Med Azure Functions kan du köra små bitar av kod (så kallade "funktioner") utan att behöva oroa dig för programinfrastruktur. Med Azure Functions tillhandahåller molninfrastrukturen alla uppdaterade servrar som du behöver för att hålla ditt program igång i stor skala.

En funktion "utlöses" av en viss typ av händelse. [Utlösare som stöds](./functions-triggers-bindings.md) omfattar att svara på ändringar i data, svara på meddelanden, köras enligt ett schema eller som ett resultat av en HTTP-begäran.

Även om du alltid kan koda direkt mot en myriad av tjänster, är integrering med andra tjänster strömlinjeformad med hjälp av bindningar. Bindningar ger dig [deklarativ åtkomst till en mängd olika Azure- och tredjepartstjänster](./functions-triggers-bindings.md).

## <a name="features"></a>Funktioner

Några viktiga funktioner i Azure Functions är:

- **Serverlösa program**: Med funktioner kan du utveckla [serverlösa](https://azure.microsoft.com/solutions/serverless/) program på Microsoft Azure.

- **Språkval**: Skriv funktioner med ditt val av [C#, Java, JavaScript, Python och PowerShell](supported-languages.md).

- **Prismodell för användning per tillfälle**: Betala endast för den tid som går åt till att köra koden. Se alternativet för förbrukningsvärdplan i [prissättningsavsnittet](#pricing).  

- **Ta med egna beroenden:** Funktioner stöder NuGet och NPM, vilket ger dig tillgång till dina favoritbibliotek.

- **Integrerad säkerhet**: Skydda HTTP-utlösta funktioner med OAuth-leverantörer som Azure Active Directory, Facebook, Google, Twitter och Microsoft-konto.

- **Förenklad integrering**: Integrera enkelt med Azure-tjänster och SaaS-erbjudanden (Software-as-a-service).

- **Flexibel utveckling**: Konfigurera kontinuerlig integrering och distribuera din kod via [GitHub,](../app-service/scripts/cli-continuous-deployment-github.md) [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)och andra [utvecklingsverktyg som stöds](../app-service/deploy-local-git.md).

- **Tillståndskänslig serverlös arkitektur:** Orchestrate serverless program med [varaktiga funktioner](durable/durable-functions-overview.md).

- **Öppen källkod:** Funktionskörningen är öppen källkod och [tillgänglig på GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Vad kan jag göra med Functions?

Funktioner är en bra lösning för bearbetning av bulkdata, integrering av system, arbete med sakernas internet (IoT) och att bygga enkla API:er och mikrotjänster.

Det finns en serie mallar som kan komma igång med viktiga scenarier, bland annat:

- **HTTP**: Kör kod baserat på [HTTP-begäranden](functions-create-first-azure-function.md)

- **Timer**: Schemakod som ska [köras vid fördefinierade tider](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: Bearbeta [nya och ändrade Azure Cosmos DB-dokument](./functions-create-cosmos-db-triggered-function.md)

- **Blob-lagring:** Bearbeta [nya och ändrade Azure Storage-blobbar](./functions-create-storage-blob-triggered-function.md)

- **Kölagring:** Svara på [Azure Storage-kömeddelanden](./functions-create-storage-queue-triggered-function.md)

- **Event Grid**: Svara på [Azure Event Grid-händelser via prenumerationer och filter](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Event Hub**: Svara på [stora volymer av Azure Event Hub-händelser](./functions-bindings-event-hubs.md)

- **Service Bus Kö:** Anslut till andra Azure eller lokala tjänster genom [att svara Service Bus kömeddelanden](./functions-bindings-service-bus.md)

- **Service Bus Topic**: Anslut andra Azure-tjänster eller lokala tjänster genom [att svara på Service Bus-ämnesmeddelanden](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Hur mycket kostar Functions?

Azure Functions har tre typer av prisplaner. Välj den som passar dina behov bäst:

- **Förbrukningsplan**: Azure tillhandahåller alla nödvändiga beräkningsresurser. Du behöver inte oroa dig för resurshantering och bara betala för den tid som koden körs.

- **Premium-plan**: Du anger ett antal förvärmda instanser som alltid är online och redo att omedelbart svara. När din funktion körs tillhandahåller Azure eventuella ytterligare beräkningsresurser som behövs. Du betalar för de förvärmda instanser som körs kontinuerligt och eventuella ytterligare instanser som du använder som Azure skalar din app in och ut.

- **App Service plan**: Kör dina funktioner precis som dina webbappar. Om du använder App Service för dina andra program kan dina funktioner köras på samma plan utan extra kostnad.

Mer information om värdplaner finns i [Azure Functions hosting plan comparison](functions-scale.md) (Jämförelse av värdplaner för Azure Functions). Fullständig prisinformation är tillgänglig på sidan [Prissättning för Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Efterföljande moment

- [Skapa din första Azure-funktion](functions-create-first-function-vs-code.md)  
  Kom igång med [Visual Studio Code,](functions-create-first-function-vs-code.md) [kommandoraden](functions-create-first-azure-function-azure-cli.md)eller använd [Azure-portalen](functions-create-first-azure-function.md).

- [Azure Functions, info för utvecklare](functions-reference.md)  
  Ger mer teknisk information om Azure Functions-runtime och en referens för kodning av funktioner och definiering av utlösare och bindningar.

- [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive värdplanen för förbrukning, och hur du väljer rätt plan.

- [Läs mer om Azure App Service](../app-service/overview.md)  
  Azure Functions använder Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik.
