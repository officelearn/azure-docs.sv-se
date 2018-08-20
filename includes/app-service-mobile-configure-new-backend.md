---
title: ta med fil
description: ta med fil
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/25/2018
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 1d3bfb7bc8a5432392dba3b0c5019902b3e59773
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513800"
---
1. Klicka på knappen **App Services**, välj din Mobile Apps-serverdel, välj **Snabbstart** och sedan din klientplattform (iOS, Android, Xamarin, Cordova).

    ![Azure Portal med snabbstarten för Mobile Apps markerad][quickstart]

1. Om en databasanslutning inte konfigureras skapar du en genom att göra följande:

    ![Azure Portal med Mobile Apps Connect till databas][connect]

    a. Skapa en ny SQL-databas och server. Du kanske måste lämna anslutningssträngens namnfält på standardvärdet MS_TableConnectionString för att kunna slutföra steg 3 nedan.

    ![Azure Portal med Mobile Apps skapa ny databas och server][server]

    b. Vänta tills anslutningen har skapats.

    ![Azure Portal-meddelande om att dataanslutningen har skapats][notification]

    c. Dataanslutningen måste lyckas.

    ![Azure Portal-meddelande, "Du har redan en dataanslutning"][already-connection]

1. Under **2. Skapa ett tabell-API**, Välj Node.js för **Språk för serverdel**.

1. Godkänn bekräftelsen och välj **Skapa TodoItem-tabell**.
    En ny att göra-post-tabell skapas i din databas.

    >[!IMPORTANT]
    > Om du ändrar en befintlig serverdel till Node.js skrivs allt innehåll över. Om du istället vill skapa en .NET-serverdel läser du [Work with the .NET back-end server SDK for Mobile Apps][instructions] (Arbeta med SDK för .NET-serverdelen för Mobile Apps).

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
