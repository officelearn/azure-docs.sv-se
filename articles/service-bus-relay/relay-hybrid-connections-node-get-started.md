---
title: "Kom igång med Relay hybridanslutningar | Microsoft Docs"
description: "Så här skriver du ett Node-konsolprogram för hybridanslutningar"
services: service-bus-relay
documentationcenter: node
author: jtaubensee
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub,sethm
translationtype: Human Translation
ms.sourcegitcommit: e26b6005116c7aacdf42afea6ea39e100a9d8080
ms.openlocfilehash: 218082e4cedba2c11871d47bcfb7b92688d97f04


---
# <a name="get-started-with-relay-hybrid-connections"></a>Kom igång med Relay hybridanslutningar
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Detta kommer att utföras
Eftersom hybridanslutningar kräver både en klient och en serverkomponent kommer vi att skapa två konsolprogram i den här självstudien. Här är stegen:

1. Skapa ett Relay-namnområde med Azure Portal.
2. Skapa en hybridanslutning med Azure Portal.
3. Skriv ett serverkonsolprogram för att ta emot meddelanden.
4. Skriv ett klientkonsolprogram för att ta emot meddelanden.

## <a name="prerequisites"></a>Krav
1. [Node.js](https://nodejs.org/en/) (I det här exemplet används Node 7.0).
2. En Azure-prenumeration.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Skapa ett namnområde med Azure Portal
Om du redan har skapat ett Relay-namnområde går du vidare till avsnittet [Skapa en hybridanslutning med Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Skapa en hybridanslutning med Azure Portal
Om du redan har skapat en hybridanslutning går du vidare till avsnittet [Skapa ett serverprogram](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Skapa ett serverprogram (lyssnare)
För att lyssna på och ta emot meddelanden från Relay skriver du ett Node.js-konsolprogram.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Skapa ett klientprogram (avsändare)
För att skicka meddelanden till Relay skriver du ett Node.js-konsolprogram.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Köra programmen
1. Kör serverprogrammet.
2. Kör klientprogrammet och skriv in lite text.
3. Se till att serverprogramkonsolen matar ut den text som angavs i klientprogrammet.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Grattis, du har skapat ett end-to-end hybridanslutningsprogram.

## <a name="next-steps"></a>Nästa steg:
* [Vanliga frågor och svar om Relay](relay-faq.md)
* [Skapa ett namnområde](relay-create-namespace-portal.md)
* [Kom igång med .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO3-->


