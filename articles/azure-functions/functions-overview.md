---
title: Översikt över Azure Functions
description: Lär dig hur Azure Functions kan hjälpa dig att bygga skalbara appar utan server.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperfq2
ms.openlocfilehash: a312c0ff255df4545181755ae6accd0d7dfb13a2
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574861"
---
# <a name="introduction-to-azure-functions"></a>Introduktion till Azure Functions

Vi skapar ofta system för att reagera på en serie kritiska händelser. Oavsett om du skapar ett webb-API, svarar på databas ändringar, bearbetar IoT-dataströmmar eller till och med hanterar meddelande köer – varje program behöver ett sätt att köra viss kod när dessa händelser inträffar.

För att möta detta måste Azure Functions tillhandahålla "Compute on-demand"-och på två betydande sätt.

För det första, Azure Functions gör det möjligt att implementera systemets logik i snabbt tillgängliga kodblock. Kodblock kallas för "Functions". Olika funktioner kan köras när du behöver svara på kritiska händelser.

För det andra, som begär Anden ökar, Azure Functions uppfyller efter frågan med så många resurser och funktions instanser som behövs – men bara vid behov. När förfrågningarna blir borta, släpps alla extra resurser och program instanser automatiskt.

Var kommer alla beräknings resurser från? Azure Functions [tillhandahåller så många eller så få beräknings resurser som behövs](./functions-scale.md) för att uppfylla ditt programs behov.

Att tillhandahålla beräknings resurser på begäran är grunden för [data behandling utan server](https://azure.microsoft.com/solutions/serverless/) i Azure Functions.

## <a name="scenarios"></a>Scenarier

I många fall [integreras en funktion med en matris med moln tjänster](./functions-triggers-bindings.md) för att tillhandahålla funktions rika implementeringar.

Följande är ett vanligt, _men inte heller en uttömmande_ uppsättning scenarier för Azure Functions.

| Om du vill... | sedan... |
| --- | --- |
| **Bygg ett webb-API** | Implementera en slut punkt för dina webb program med hjälp av [http-utlösaren](./functions-bindings-http-webhook.md) |
| **Bearbeta fil överföringar** | Kör kod när en fil laddas upp eller ändras i [Blob Storage](./functions-bindings-storage-blob.md) |
| **Bygg ett Server lös arbets flöde** | Kedja en serie funktioner tillsammans med hjälp av [varaktiga funktioner](./durable/durable-functions-overview.md) |
| **Svara på databas ändringar** | Kör anpassad logik när ett dokument skapas eller uppdateras i [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Köra schemalagda aktiviteter** | Kör kod vid [angivna tider](./functions-bindings-timer.md) |
| **Skapa Reliable Message Queue-system** | Bearbeta meddelande köer med hjälp av [Queue Storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md)eller [Event Hubs](./functions-bindings-event-hubs.md) |
| **Analysera IoT-dataströmmar** | Samla in och bearbeta [data från IoT-enheter](./functions-bindings-event-iot.md) |
| **Bearbeta data i real tid** | Använd [Functions och signalerer](./functions-bindings-signalr-service.md) för att svara på data för tillfället |

När du skapar dina funktioner har du följande alternativ och tillgängliga resurser:

- **Använd önskat språk**: Skriv funktioner i [C#, Java, Java Script, PowerShell eller python](./supported-languages.md), eller Använd en [anpassad hanterare](./functions-custom-handlers.md) för att använda i stort sett alla andra språk.

- **Automatisera distribution**: från en verktyg-baserad metod till att använda externa pipeliner finns det en [myriaden tillgängliga distributions alternativ](./functions-deployment-technologies.md) .

- **Felsöka en funktion**: använda [övervaknings verktyg](./functions-monitoring.md) och [testnings strategier](./functions-test-a-function.md) för att få insikter om dina appar.

- **Flexibla pris alternativ**: med [förbruknings](./pricing.md) planen betalar du bara medan funktionerna körs, medan [Premium](./pricing.md) -och [App Service](./pricing.md) -planerna erbjuder funktioner för specialiserade behov.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång genom lektioner, exempel och interaktiva självstudier](./functions-get-started.md)
