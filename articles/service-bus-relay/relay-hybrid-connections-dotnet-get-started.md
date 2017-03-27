---
title: "Komma igång med Azure Relay-hybridanslutningar i .NET | Microsoft Docs"
description: "Så här skriver du ett C#-konsolprogram för hybridanslutningar"
services: service-bus-relay
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/27/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: d27016559ede5d810d7efcec2a3abc78334f0f0a
ms.lasthandoff: 02/28/2017


---
# <a name="get-started-with-relay-hybrid-connections"></a>Kom igång med Relay hybridanslutningar
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

I den här självstudien får du en introduktion till [Azure Relay-hybridanslutningar](relay-what-is-it.md#hybrid-connections), och du får se hur du skapar ett klientprogram som skickar meddelanden till ett motsvarande lyssnarprogram. 

## <a name="what-will-be-accomplished"></a>Detta kommer att utföras
Eftersom hybridanslutningar kräver både en klient- och en serverkomponent kommer vi att skapa två konsolprogram i den här självstudien. Stegen är:

1. Skapa ett Relay-namnområde med Azure Portal.
2. Skapa en hybridanslutning med Azure Portal.
3. Skriva ett serverkonsolprogram (lyssnare) för att ta emot meddelanden.
4. Skriva ett klientkonsolprogram (avsändare) för att ta emot meddelanden.

## <a name="prerequisites"></a>Krav
1. [Visual Studio 2015 eller senare](http://www.visualstudio.com). Exemplen i de här självstudierna använder Visual Studio 2015.
2. En Azure-prenumeration.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Skapa ett namnområde med Azure Portal
Om du redan har skapat ett Relay-namnområde går du vidare till avsnittet [Skapa en hybridanslutning med Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Skapa en hybridanslutning med Azure Portal
Om du redan har skapat en hybridanslutning går du vidare till avsnittet [Skapa ett serverprogram](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Skapa ett serverprogram (lyssnare)
För att lyssna på och ta emot meddelanden från Relay skriver du ett C#-konsolprogram med Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Skapa ett klientprogram (avsändare)
För att skicka meddelanden till Relay skriver du ett C#-konsolprogram med Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Köra programmen
1. Kör serverprogrammet.
2. Kör klientprogrammet och skriv in lite text.
3. Se till att serverprogramkonsolen matar ut den text som angavs i klientprogrammet.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Grattis, du har skapat ett end-to-end hybridanslutningsprogram.

## <a name="next-steps"></a>Nästa steg:
* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)


