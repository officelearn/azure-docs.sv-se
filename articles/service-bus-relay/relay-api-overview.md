---
title: Översikt över Azure Relay API | Microsoft Docs
description: 'Översikt över tillgängliga Azure Relay API: er'
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: sethm
ms.openlocfilehash: 00496ca6c0138a840322c053d7d20944df228e9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="available-relay-apis"></a>Tillgängliga Relay-API: er

## <a name="runtime-apis"></a>Runtime-API: er

I följande tabell visas alla tillgängliga Relay runtime-klienter.

Den [ytterligare information](#additional-information) avsnitt innehåller mer information om statusen för varje runtime-bibliotek.

| Språk-plattformen | Tillgänglig funktion | Klientpaketet | Lagringsplats |
| --- | --- | --- | --- |
| Standard för .NET | Hybridanslutningar | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET framework | WCF-relä | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Gäller inte |
| Node | Hybridanslutningar | [Websockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-begäranden: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET

.NET-ekosystemet har flera körningar, så det finns flera .NET-bibliotek för vidarebefordran. .NET standardbibliotek kan köras med .NET Core eller .NET Framework, medan biblioteket för .NET Framework kan endast köras i en miljö med .NET Framework. Läs mer på .NET Frameworks [framework-versioner](/dotnet/articles/standard/frameworks#framework-versions).

.NET Framework-biblioteket bara stöder programmeringsmodellen WCF och bygger på en binär protokoll baserat på WCF `net.tcp` transport. Det här protokollet och biblioteket bevaras för bakåtkompatibilitet kompatibilitet med befintliga program.

Standardbiblioteket för .NET är baserat på Öppna protokolldefinitionen för Hybrid anslutningar Relay som bygger på http- och WebSockets. Biblioteket stöder en dataström abstraktion över Websockets och en enkel begäran och svar API gest för svarande HTTP-begäranden. Den [Web API](https://github.com/Azure/azure-relay-dotnet) exempel visas hur du integrerar Hybridanslutningar med ASP.NET Core för webbtjänster.

#### <a name="nodejs"></a>Node.js

Hybridanslutningar-moduler som anges i tabellen ovan ersätta eller ändra befintliga Node.js-moduler med alternativa implementeringar som lyssnar på tjänsten Azure Relay i stället för den lokala nätverksstacken.

Den `hyco-https` modul ändras och delvis åsidosätter Node.js-moduler core `http` och `https`, förutsatt att en HTTPS-lyssnare implementering som är kompatibel med många befintliga Node.js-moduler och program som förlitar sig på dessa core moduler.

Den `hyco-ws` och `hyco-websocket` moduler ändra den populära `ws` och `websocket` moduler för Node.js, att tillhandahålla alternativa lyssnare implementeringar som möjliggör moduler och program som förlitar sig på antingen modulen arbetar bakom Hybrid Anslutningar Relay.

Information om dessa moduler kan hittas i den [azure-relay-nod](https://github.com/Azure/azure-relay-node) GitHub-lagringsplatsen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Relay finns i följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)