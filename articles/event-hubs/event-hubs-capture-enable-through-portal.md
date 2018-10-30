---
title: Aktivera avbildningsfunktionen för Azure Event Hubs via portalen | Microsoft Docs
description: Använd Azure Portal för att aktivera avbildningsfunktionen i Event Hubs.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 5cb3fee975e99af3d86affcbc18d89277dde2dc1
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409126"
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Aktivera avbildningsfunktionen i Event Hubs med Azure Portal

Med Azure [Event Hubs Capture][capture-overview] kan du automatiskt leverera strömmande data i Event Hubs till valfritt [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)- eller [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-konto.

Du kan konfigurera avbildningsfunktionen med [Azure Portal](https://portal.azure.com) när du skapar händelsehubben. Du kan antingen samla in data till en Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)-container eller till ett [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-konto.

Mer information finns i [Översikt över Event Hubs Capture][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Samla in data till ett Azure Storage-konto  

När du skapar en händelsehubb kan du aktivera avbildningsfunktionen genom att klicka på knappen **På** på portalskärmen **Skapa händelsehubb**. Sedan anger du ett lagringskonto och en container genom att klicka på **Azure Storage** i rutan **Capture Provider** (Avbildningsprovider). Eftersom tjänst-till-tjänst-autentisering används för lagring i avbildningsfunktionen i Event Hubs behöver du inte ange någon anslutningssträng för lagringen. Resursväljaren väljer automatiskt rätt resurs-URI för ditt lagringskonto. Om du använder Azure Resource Manager måste du ange den här URI:n uttryckligen som en sträng.

Standardvärdet för Tidsperiod är 5 minuter. Lägsta värde är 1 och högsta är 15. Du kan ange intervallet 10–500 MB för **Storlek**.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Samla in data till ett Azure Data Lake Store-konto

Om du vill samla in data till Azure Data Lake Store skapar du ett Data Lake Store-konto och en händelsehubb:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Skapa ett Azure Data Lake Store-konto och mappar

1. Skapa ett Data Lake Store-konto enligt anvisningarna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md).
2. Följ anvisningarna i avsnittet [Tilldela behörigheter till Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) om du vill skapa en mapp i det Data Lake Store-konto där du vill samla in data från Event Hubs och vill tilldela behörigheter till Event Hubs så att det kan skriva data till ditt Data Lake Store-konto.  

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. Observera att händelsehubben måste vara i samma Azure-prenumeration som den Azure Data Lake Store som du precis skapade. Skapa en händelsehubb genom att klicka på knappen **På** under **Capture** (Avbilda) på portalsidan **Skapa händelsehubb**. 
2. Välj **Azure Data Lake Store** i rutan **Capture Provider** (Avbildningsprovider) på portalsidan **Skapa händelsehubb**.
3. I **Välj Data Lake Store** anger du det Data Lake Store-konto som du skapade tidigare och i fältet **Data Lake Path** (Sökväg till Data Lake) anger du sökvägen till den datamapp som du skapade.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Lägga till eller konfigurera avbildningsfunktionen i en befintlig händelsehubb

Du kan konfigurera avbildningsfunktionen i befintliga händelsehubbar som finns i namnområden för Event Hubs. Om du vill aktivera avbildningsfunktionen i en befintlig händelsehubb eller om du vill ändra inställningarna för avbildningsfunktionen, klickar du på namnområdet för att läsa in översiktsskärmen. Klicka sedan på den händelsehubb som du vill aktivera eller ändra avbildningsinställningen för. Klicka slutligen på alternativet **Avbilda** på vänster sida på sidan som är öppen och redigera inställningarna enligt följande bilder:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Nästa steg

- Läs mer om Event Hubs Capture i [Översikt över Event Hubs Capture][capture-overview].
- Du kan också konfigurera avbildningsfunktionen i Event Hubs med hjälp av Azure Resource Manager-mallar. Mer information finns i [Skapa ett namnområde för Event Hubs med en händelsehubb och aktivera avbildning med hjälp av en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Lär dig hur du skapar en Azure Event Grid-prenumeration med en Event Hubs-namnrymd som dess källa](store-captured-data-data-warehouse.md)
- [Kom igång med Azure Data Lake Store med Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md