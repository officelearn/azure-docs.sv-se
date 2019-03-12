---
title: Komma igång med Azure Relay-hybridanslutningar med Websockets i .NET | Microsoft Docs
description: Skriv ett C#-konsolprogram för Azure Relay-hybridanslutningar med Websockets.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 16b1ad580ce9d2a6c34da7c243ab6dd9ab810078
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763984"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Komma igång med Azure Relay-hybridanslutningar med Websockets i .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

I den här snabbstarten skapar du ett sändar- och mottagarprogram i .NET som skickar och tar emot meddelanden med hybridanslutningar med Websockets i Azure Relay. Läs mer om Azure Relay i allmänhet i [Azure Relay](relay-what-is-it.md). 

I den här snabbstarten gör du följande:

1. Skapa ett Relay-namnområde med Azure Portal.
2. Skapa en hybridanslutning i det namnområdet med Azure Portal.
3. Skriva ett serverkonsolprogram (lyssnare) för att ta emot meddelanden.
4. Skriva ett klientkonsolprogram (avsändare) för att ta emot meddelanden.
5. Kör program. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* [Visual Studio 2015 eller senare](http://www.visualstudio.com). I exemplen i den här självstudiekursen används Visual Studio 2017.
* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-namespace"></a>Skapa ett namnområde
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Skapa en hybridanslutning
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Skapa ett serverprogram (lyssnare)
För att lyssna på och ta emot meddelanden från Relay skriver du ett C#-konsolprogram i Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Skapa ett klientprogram (avsändare)
För att skicka meddelanden till Relay skriver du ett C#-konsolprogram i Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Köra programmen
1. Kör serverprogrammet.
2. Kör klientprogrammet och skriv in lite text.
3. Se till att serverprogramkonsolen visar den text som angavs i klientprogrammet.

    ![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Grattis, du har skapat ett end-to-end hybridanslutningsprogram!

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du .NET-klient- och -serverprogram som använde WebSockets för att skicka och ta emot meddelanden. Funktionen Hybridanslutningar i Azure Relay stöder också användningen av HTTP för att skicka och ta emot meddelanden. Läs om hur du använder HTTP med Azure Relay-hybridanslutningar i [HTTP-snabbstarten](relay-hybrid-connections-http-requests-dotnet-get-started.md).

I den här snabbstarten använde du .NET Framework för att skapa klient- och serverprogram. Om du vill läsa om hur du skriver klient- och serverprogram med hjälp av Node.js läser du [Node.js-snabbstarten](relay-hybrid-connections-node-get-started.md) eller [Node.jsT HTTP-snabbstarten](relay-hybrid-connections-http-requests-dotnet-get-started.md).

