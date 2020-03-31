---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187849"
---
1. Högerklicka på mappen **Komponenter** i lösningsvyn (eller **Solution Explorer** i Visual Studio), klicka på Hämta **fler komponenter...**, sök efter komponenten **Google Cloud Messaging-klient** och lägg till den i projektet.
2. Öppna ToDoActivity.cs projektfilen och lägg till följande med hjälp av satsen i klassen:

    ```csharp
    using Gcm.Client;
    ```

3. Lägg till följande nya kod i klassen **ToDoActivity:** 

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

    På så sätt kan du komma åt den mobila klientinstansen från push-hanterartjänsten.
4. Lägg till följande kod i **Metoden OnCreate** när **MobileServiceClient** har skapats:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

**Din ToDoActivity** är nu förberedd för att lägga till push-meddelanden.
