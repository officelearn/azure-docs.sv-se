---
title: Spara dina IoT hub-meddelanden i Azure datalagring | Microsoft Docs
description: "Använd IoT-hubb meddelanderoutning för att spara din IoT-hubb-meddelanden till Azure blob storage. IoT-hubb meddelanden innehåller information, till exempel sensordata som skickas från IoT-enhet."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IOT-datalagring, datalagring för iot-temperatursensor"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: xshi
ms.openlocfilehash: f6b334dbc9903d0080b74052062de7564aa4a993
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Spara IoT hub-meddelanden som innehåller sensordata till Azure blob storage

![Diagram för slutpunkt till slutpunkt](media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du skapar ett Azure storage-konto och ett Azure funktionsapp att lagra IoT-hubb meddelanden i blob storage.

## <a name="what-you-do"></a>Vad du gör

- Skapa ett Azure-lagringskonto.
- Förbered din IoT-hubb för att vidarebefordra meddelanden till lagring.

## <a name="what-you-need"></a>Vad du behöver

- [Konfigurera din enhet](iot-hub-raspberry-pi-kit-node-get-started.md) som täcker följande krav:
  - En aktiv Azure-prenumeration
  - En IoT-hubb i din prenumeration 
  - Ett program som körs som skickar meddelanden till din IoT-hubb

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapar du en resurs** > **lagring** > **lagringskonto**  >  **Skapa**.

2. Ange informationen som krävs för lagringskontot:

   ![Skapa ett lagringskonto i Azure-portalen](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Namnet**: namnet på lagringskontot. Namnet måste vara globalt unikt.

   * **Resursgruppen**: använda samma resursgrupp som använder din IoT-hubb.

   * **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

3. Klicka på **Skapa**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Förbered din IoT-hubb för att vidarebefordra meddelanden till lagring

IoT-hubben har inbyggt stöd för vidarebefordra meddelanden till Azure-lagring som blobar.

### <a name="add-storage-as-a-custom-endpoint"></a>Lägga till lagringsenheter som en anpassad slutpunkt

Gå till din IoT-hubb i Azure-portalen. Klicka på **slutpunkter** > **lägga till**. Slutpunkten och välj **Azure Storage-behållare** typen av slutpunkt. Använd väljaren för att välja lagringskontot som du skapade i föregående avsnitt. Skapa en lagringsbehållare och markera den och sedan klicka på **OK**.

  ![Skapa en anpassad slutpunkt i IoT-hubb](media\iot-hub-store-data-in-azure-table-storage\2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Lägga till en väg att vidarebefordra data till lagring

Klicka på **vägar** > **Lägg till** och ange ett namn för vägen. Välj **meddelanden** som datakällan och välj lagring-slutpunkt som du precis skapade som slutpunkt i flödet. Ange `true` som frågesträngen Klicka **spara**.

  ![Skapa en väg i IoT-hubb](media\iot-hub-store-data-in-azure-table-storage\3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Lägga till en väg för varm sökväg telemetri (valfritt)

Som standard skickar IoT-hubb alla meddelanden som inte matchar andra vägar till inbyggda slutpunkten. Eftersom alla meddelanden som telemetri matchar nu den regel som skickar meddelanden till lagring, som du behöver lägga till en annan väg för meddelanden som ska skrivas till inbyggd slutpunkt. Det finns inga extra kostnad för att skicka meddelanden till flera slutpunkter.

> [!NOTE]
> Du kan hoppa över det här steget om du inte göra ytterligare bearbetning på meddelandena telemetri.

Klicka på **Lägg till** från fönstret vägar och ange ett namn för vägen. Välj **meddelanden** som datakälla och **händelser** som slutpunkt. Ange `true` som frågesträngen Klicka **spara**.

  ![Skapa en hot bana väg i IoT-hubb](media\iot-hub-store-data-in-azure-table-storage\4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Kontrollera meddelandet i storage-behållare

1. Kör exempelprogrammet på enheten för att skicka meddelanden till din IoT-hubb.

2. [Hämta och installera Azure Lagringsutforskaren](http://storageexplorer.com/).

3. Öppna Lagringsutforskaren, klicka på **Lägg till ett Azure-konto** > **logga in**, och sedan logga in på ditt Azure-konto.

4. Klicka på Azure-prenumerationen > **Lagringskonton** > ditt lagringskonto > **Blob-behållare** > din behållare.

   Du bör se meddelanden som skickas från enheten till din IoT-hubb som loggas i blob-behållaren.

## <a name="next-steps"></a>Nästa steg

Du har skapat ditt Azure storage-konto och routade meddelanden från IoT-hubb till en blobbbehållare i detta lagringskonto.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
