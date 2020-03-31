---
title: Översikt över Azure Relay API | Microsoft-dokument
description: Den här artikeln innehåller en översikt över tillgängliga Azure Relay API:er (.NET Standard, .NET Framework, Node.js, etc.)
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 41d9e2026c19c959dc6fe2546b0ef699571ec7cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513974"
---
# <a name="available-relay-apis"></a>Tillgängliga relay-API:er

## <a name="runtime-apis"></a>Körnings-API:er

I följande tabell visas alla tillgängliga Relay runtime-klienter.

Avsnittet [med ytterligare information](#additional-information) innehåller mer information om status för varje körningsbibliotek.

| Språk/plattform | Tillgänglig funktion | Klientpaketet | Lagringsplats |
| --- | --- | --- | --- |
| .NET Standard | Hybridanslutningar | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF-relä | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Ej tillämpligt |
| Node | Hybridanslutningar | [Websockets:`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets:`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-begäranden:`hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET

.NET-ekosystemet har flera körningar, därför finns det flera .NET-bibliotek för relayen. .NET Standard-biblioteket kan köras med antingen .NET Core eller .NET Framework, medan .NET Framework-biblioteket bara kan köras i en .NET Framework-miljö. Mer information om .NET Frameworks finns i [ramversioner](/dotnet/articles/standard/frameworks).

.NET Framework-biblioteket stöder endast WCF-programmeringsmodellen och förlitar sig på `net.tcp` ett eget binärt protokoll baserat på WCF-transporten. Det här protokollet och biblioteket underhålls för bakåtkompatibilitet med befintliga program.

.NET Standard-biblioteket baseras på den öppna protokolldefinitionen för hybridanslutningsreläet som bygger på HTTP och WebSockets. Biblioteket stöder en stream abstraktion över Websockets och en enkel begäran-svar API gest för att svara http-förfrågningar. Exemplet [webb-API](https://github.com/Azure/azure-relay-dotnet) visar hur du integrerar hybridanslutningar med ASP.NET Core för webbtjänster.

#### <a name="nodejs"></a>Node.js

Hybridanslutningsmodulerna som anges i tabellen ovan ersätter eller ändrar befintliga Node.js-moduler med alternativa implementeringar som lyssnar på Azure Relay-tjänsten i stället för den lokala nätverksstacken.

Modulen `hyco-https` ändrar och åsidosätter delvis core Node.js-modulerna `http` och `https`tillhandahåller en HTTPS-lyssnarimplementering som är kompatibel med många befintliga Node.js-moduler och program som är beroende av dessa kärnmoduler.

`hyco-ws` Modulerna `hyco-websocket` och ändrar `ws` `websocket` populära moduler och moduler för Node.js, vilket ger alternativa lyssnarimplementeringar som gör det möjligt för moduler och program som förlitar sig på någon av modulerna att fungera bakom Hybrid Connections Relay.

Information om dessa moduler finns i [azure-relay-noden](https://github.com/Azure/azure-relay-node) GitHub-databasen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Relay finns i följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)
