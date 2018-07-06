---
title: Spara dina meddelanden från IoT hub datalagring för Azure | Microsoft Docs
description: Använd IoT Hub meddelanderoutning för att spara din IoT hub-meddelanden till Azure blob storage. Meddelanden från IoT hub innehåller information, till exempel sensordata som skickas från din IoT-enhet.
author: rangv
manager: ''
keywords: IOT-datalagring, datalagring för iot-sensorn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856298"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Spara meddelanden från IoT hub som innehåller sensordata till Azure blob storage

![Slutpunkt till slutpunkt-diagram](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du skapar ett Azure storage-konto och Azure Functions-app för att lagra meddelanden från IoT hub i Azure Blob storage.

## <a name="what-you-do"></a>Vad du gör

- Skapa ett Azure-lagringskonto.
- Ställ in din IoT-hubb för att dirigera meddelanden till lagring.

## <a name="what-you-need"></a>Vad du behöver

- [Konfigurera din enhet](iot-hub-raspberry-pi-kit-node-get-started.md) som täcker följande krav:
  - En aktiv Azure-prenumeration
  - En IoT-hubb i din prenumeration 
  - Ett program som körs som skickar meddelanden till din IoT-hubb

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapa en resurs** > **Storage** > **lagringskonto**.

2. Ange informationen som krävs för lagringskontot:

   ![Skapa ett lagringskonto i Azure portal](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **Namn på**: namnet på lagringskontot. Namnet måste vara globalt unikt.

   * **Typ av konto**: Välj `Storage (general purpose v1)`.

   * **Plats**: väljer du samma plats som använder din IoT-hubb.

   * **Replikering**: Välj `Locally-redundant storage (LRS)`.

   * **Prestanda**: Välj `Standard`.

   * **Säker överföring krävs**: Välj `Disabled`.

   * **Prenumeration**: Välj din Azure-prenumeration.

   * **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   * **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

3. Klicka på **Skapa**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Förbereda din IoT-hubb för att dirigera meddelanden till storage

IoT Hub stöd har inbyggt vidarebefordra meddelanden till Azure storage som blobar. Om du vill veta mer om Azure IoT Hub anpassade slutpunkter kan du referera till [lista över inbyggda slutpunkter i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints).

### <a name="add-storage-as-a-custom-endpoint"></a>Lägga till lagring som en anpassad slutpunkt

1. Gå till din IoT-hubb i Azure-portalen. 

2. Klicka på **slutpunkter** > **lägga till**. 

3. Namnge slutpunkten och välj **Azure Storage-behållare** som slutpunktstyp av. 

4. Använd väljaren för att välja det lagringskonto du skapade i föregående avsnitt. Skapa en lagringsbehållare och markera den och sedan klicka på **OK**.

   ![Skapa en anpassad slutpunkt i IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Lägg till en väg att vidarebefordra data till lagring

1. Klicka på **vägar** > **Lägg till** och ange ett namn för flödet. 

2. Välj **enhetsmeddelanden** som datakälla, och välj den slutpunkt för lagring som du precis har skapat som slutpunkten i flödet. 

3. Ange `true` som frågesträng, klicka sedan på **spara**.

   ![Skapa en väg i IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Lägg till en väg för heta sökvägen telemetri (valfritt)

Som standard dirigerar alla meddelanden som inte matchar andra vägar till den inbyggda slutpunkten i IoT Hub. Eftersom alla telemetrimeddelanden matchar nu den regel som dirigerar meddelanden till lagring, behöver du lägga till en annan väg för meddelanden som ska skrivas till den inbyggda slutpunkten. Det finns ingen ytterligare avgift för att skicka meddelanden till flera slutpunkter.

> [!NOTE]
> Du kan hoppa över det här steget om du inte gör ytterligare bearbetning på din telemetrimeddelanden.

1. Klicka på **Lägg till** från fönstret vägar och ange ett namn för flödet. 

2. Välj **enhetsmeddelanden** som datakälla och **händelser** som slutpunkt. 

3. Ange `true` som frågesträng, klicka sedan på **spara**.

  ![Skapa en väg för frekvent sökväg i IoT Hub](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Kontrollera ditt meddelande i lagringsbehållaren

1. Kör exempelprogrammet på enheten för att skicka meddelanden till din IoT-hubb.

2. [Ladda ned och installera Azure Storage Explorer](http://storageexplorer.com/).

3. Öppna Storage Explorer, klicka på **Lägg till ett Azure-konto** > **logga in**, och sedan logga in på ditt Azure-konto.

4. Klicka på din Azure-prenumeration > **Lagringskonton** > ditt lagringskonto > **Blobbehållare** > din behållare.

   Du bör se meddelanden som skickas från enheten till din IoT-hubb som loggas i blob-behållaren.

## <a name="clean-up-resources"></a>Rensa resurser 

I de här självstudierna har lagts till ett lagringskonto och sedan läggs routning för meddelanden från IoT Hub som ska skrivas till lagringskontot. Om du vill rensa de resurser som du skapade, ta bort information om Routning och ta bort lagringskontot. 

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Klicka på **resursgrupper** och välj den resursgrupp som du använde. I listan över resurser i gruppen visas. 

   > [!NOTE]
   > Om du vill ta bort alla resurser i resursgruppen klickar du på **ta bort** om du vill ta bort resursgruppen, följ instruktionerna. Detta tar bort allt i den resursgruppen, så att du är klar med att rensa resurserna och kan gå vidare till nästa avsnitt.

3. Klicka på IoT-hubben som du använde för den här självstudien. 

4. I IoT Hub-fönstret klickar du på **vägar**. Klicka på kryssrutan bredvid en routningsregel för du lagt till och klicka sedan på **ta bort**. När du tillfrågas om du är säker på att du vill ta bort det flödet, klickar du på **Ja**.

   ![Ta bort regel för vidarebefordran](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   Stäng fönstret routning. Du kommer tillbaka till fönstret resursgrupp.

5. Klicka på IoT-hubben igen. 

6. I IoT Hub-fönstret klickar du på **slutpunkter**. Klicka på kryssrutan bredvid den slutpunkt som du har lagt till för storage-behållare och klicka sedan på **ta bort**. När du tillfrågas om du är säker på att du vill ta bort den valda slutpunkten klickar du på **Ja**.

    ![Ta bort slutpunkten](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    Stäng fönstret slutpunkter. Du kommer tillbaka till fönstret resursgrupp. 

7.  Klicka på det lagringskonto som du har konfigurerat för den här självstudien. 

8.  Klicka på rutan för Storage **ta bort** att ta bort lagringskontot. Du kommer till den **ta bort lagringskonto** fönstret.

   ![Ta bort lagringskonto](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  Ange lagringskontonamn och klicka sedan på **ta bort** längst ned i fönstret. 

## <a name="next-steps"></a>Nästa steg

Du har skapat dina Azure storage-konto och routade meddelanden från IoT Hub på en blobbehållare i det lagringskontot.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
