---
title: Komma igång med HTTP-begäranden för Azure Relay-hybridanslutningar i Node | Microsoft Docs
description: Skriva ett Node.js-konsolprogram för HTTP-begäranden för Azure Relay-hybridanslutningar i Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: a25ca0c10b66f6881f2423306564d7d37124c33a
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616188"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Komma igång med HTTP-begäranden för Relay-hybridanslutningar i Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

I den här snabbstarten skapar du ett sändar- och mottagarprogram i Node.js som skickar och tar emot meddelanden med HTTP-protokollet. Programmen använder funktionen Hybridanslutningar i Azure Relay. Läs mer om Azure Relay i allmänhet i [Azure Relay](relay-what-is-it.md). 

I den här snabbstarten gör du följande:

1. Skapa ett Relay-namnområde med Azure Portal.
2. Skapa en hybridanslutning i det namnområdet med Azure Portal.
3. Skriva ett serverkonsolprogram (lyssnare) för att ta emot meddelanden.
4. Skriva ett klientkonsolprogram (avsändare) för att ta emot meddelanden.
5. Kör program.

## <a name="prerequisites"></a>Nödvändiga komponenter
- [Node.js](https://nodejs.org/en/).
- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-namespace-using-the-azure-portal"></a>Skapa ett namnområde med Azure Portal
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Skapa en hybridanslutning med Azure Portal
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Skapa ett serverprogram (lyssnare)
För att lyssna på och ta emot meddelanden från Relay skriver du ett Node.js-konsolprogram.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Skapa ett klientprogram (avsändare)

För att skicka meddelanden till Relay kan du använda en HTTP-klient eller skriva ett Node.js-konsolprogram.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>Köra programmen

1. Kör serverprogrammet: i en Node.js-kommandotolk skriver du in `node listener.js`.
2. Kör klientprogrammet: från en Node.js-kommandotolken skriver du in `node sender.js` och anger lite text.
3. Se till att serverprogramkonsolen matar ut den text som angavs i klientprogrammet.

Grattis, du har skapat ett hybridanslutningsprogram från slutpunkt till slutpunkt med hjälp av Node.js!

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du Node.js-klient- och -serverprogram som använde HTTP för att skicka och ta emot meddelanden. Funktionen Hybridanslutningar i Azure Relay stöder också användningen av WebSockets för att skicka och ta emot meddelanden. Läs om hur du använder WebSockets med Azure Relay-hybridanslutningar i [WebSockets-snabbstarten](relay-hybrid-connections-node-get-started.md).

I den här snabbstarten använde du Node.js för att skapa klient- och serverprogram. Om du vill läsa om hur du skriver klient- och serverprogram med hjälp av .NET Framework läser du [.NET WebSockets-snabbstarten](relay-hybrid-connections-dotnet-get-started.md) eller [.NET HTTP-snabbstarten](relay-hybrid-connections-http-requests-dotnet-get-started.md).
