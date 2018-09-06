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
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/15/2017
ms.author: spelluru
ms.openlocfilehash: 1ed401f6175d7ebea83a888898221d345791bc34
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697966"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Komma igång med Azure Relay-hybridanslutningar med Websockets i .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Den här självstudien innehåller en introduktion till [Azure Relay-hybridanslutningar](relay-what-is-it.md#hybrid-connections). Lär dig hur du skapar ett klientprogram med .NET som skickar meddelanden till ett motsvarande lyssnarprogram. 

## <a name="what-will-be-accomplished"></a>Detta kommer att utföras
Hybridanslutningar kräver både en klient- och en serverkomponent. I den här självstudien har du slutfört de här stegen för att skapa två konsolprogram:

1. Skapa ett Relay-namnområde med Azure Portal.
2. Skapa en hybridanslutning i det namnområdet med Azure Portal.
3. Skriva ett serverkonsolprogram (lyssnare) för att ta emot meddelanden.
4. Skriva ett klientkonsolprogram (avsändare) för att ta emot meddelanden.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien, finns följande förhandskrav:

* [Visual Studio 2015 eller senare](http://www.visualstudio.com). I exemplen i den här självstudiekursen används Visual Studio 2017.
* En Azure-prenumeration.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Skapa ett namnområde med Azure Portal
Om du redan har skapat ett Relay-namnområde går du till [Skapa en hybridanslutning med Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Skapa en hybridanslutning med Azure Portal
Om du redan har skapat en hybridanslutning går du till [Skapa ett serverprogram](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Skapa ett serverprogram (lyssnare)
För att lyssna på och ta emot meddelanden från Relay skriver du ett C#-konsolprogram i Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Skapa ett klientprogram (avsändare)
För att skicka meddelanden till Relay skriver du ett C#-konsolprogram i Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Köra programmen
1. Kör serverprogrammet.
2. Kör klientprogrammet och skriv in lite text.
3. Se till att serverprogramkonsolen visar den text som angavs i klientprogrammet.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Grattis, du har skapat ett end-to-end hybridanslutningsprogram!

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)

