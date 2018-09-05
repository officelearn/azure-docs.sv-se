---
title: Översikt över Azure Relay-API | Microsoft Docs
description: 'Översikt över tillgängliga Azure Relay API: er'
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
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 2c7aec700a1077bc1a1b56afb5d8d07f47f4c6e8
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700380"
---
# <a name="available-relay-apis"></a>Tillgängliga Relay API: er

## <a name="runtime-apis"></a>Runtime-API: er

I följande tabell visas alla tillgängliga Relay runtime-klienter.

Den [ytterligare information](#additional-information) innehåller mer information om status för varje runtime-bibliotek.

| Språk/plattform | Tillgänglig funktion | Klientpaketet | Lagringsplats |
| --- | --- | --- | --- |
| .NET Standard | Hybridanslutningar | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF-relä | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Gäller inte |
| Node | Hybridanslutningar | [Websockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-begäranden: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET

.NET-ekosystem har flera körningar, därför det finns flera .NET-bibliotek för Relay. .NET Standard-biblioteket kan köras med .NET Core eller .NET Framework, medan i .NET Framework-biblioteket kan bara köras i en miljö med .NET Framework. Läs mer på .NET Frameworks [ramverksversioner](/dotnet/articles/standard/frameworks#framework-versions).

.NET Framework-biblioteket som endast stöder WCF-programmeringsmodellen och förlitar sig på en binär protokoll baserat på WCF `net.tcp` transport. Det här protokollet och biblioteket bevaras för bakåtkompatibilitet kompatibilitet med befintliga program.

.NET Standard-biblioteket är baserad på öppen protokolldefinitionen för vidarebefordran för Hybrid-anslutningar som bygger på HTTP och WebSockets. Biblioteket stöder en stream-abstraktion över Websockets och en enkel begäran / svar-API-gest svarande HTTP-förfrågningar. Den [webb-API](https://github.com/Azure/azure-relay-dotnet) exemplet visar hur du integrerar Hybridanslutningar med ASP.NET Core för webbtjänster.

#### <a name="nodejs"></a>Node.js

Hybrid Connections-modulerna som anges i tabellen ovan ersätter eller ändra befintliga Node.js-moduler med alternativa implementeringar som lyssnar på Azure Relay-tjänsten i stället för den lokala nätverksstacken.

Den `hyco-https` modul ändras och delvis åsidosätter Node.js-moduler core `http` och `https`, tillhandahåller en HTTPS-lyssnare implementering som är kompatibel med många befintliga Node.js-moduler och program som förlitar sig på dessa core moduler.

Den `hyco-ws` och `hyco-websocket` moduler ändra populära `ws` och `websocket` moduler för Node.js, att tillhandahålla alternativa lyssnare implementeringar som möjliggör moduler och program som förlitar sig på antingen modulen ska fungera bakom Hybridmolnet Anslutningar Relay.

Information om dessa moduler kan hittas i den [azure-relay-nod](https://github.com/Azure/azure-relay-node) GitHub-lagringsplatsen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Relay finns i följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)