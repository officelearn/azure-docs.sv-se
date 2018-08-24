---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 42c961b81a254adef5e42c3c8916c9c081f548c8
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811621"
---
1. I vyn lösning (eller **Solution Explorer** i Visual Studio), högerklicka på den **komponenter** mapp, klickar du på **få fler komponenter...** , Sök efter den **Google Cloud Messaging-klienten** komponenten och Lägg till den i projektet.
2. Öppna filen ToDoActivity.cs projektet och Lägg till följande användningsinstruktion klassen:

    ```csharp
    using Gcm.Client;
    ```

3. I den **ToDoActivity** klass, Lägg till följande nya kod: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    På så sätt kan du komma åt mobilklient-instansen från tjänstprocessen för push-hanteraren.
4. Lägg till följande kod till den **OnCreate** metod, efter den **MobileServiceClient** skapas:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Din **ToDoActivity** förbereds nu för att lägga till push-meddelanden.
