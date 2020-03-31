---
title: Event Hubs – Samla in direktuppspelningshändelser med Azure-portal
description: Den här artikeln beskriver hur du aktiverar avbildning av händelser som strömmas via Azure Event Hubs med hjälp av Azure-portalen.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 8a6d9456b00e5520e6f4fbb9ccb77b0260731ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187420"
---
# <a name="enable-capturing-of-events-streaming-through-azure-event-hubs"></a>Aktivera avbildning av händelser som strömmas via Azure Event Hubs

Med Azure [Event Hubs Capture][capture-overview] kan du automatiskt leverera strömmande data i eventhubbar till ett [Azure Blob-lagrings-](https://azure.microsoft.com/services/storage/blobs/) eller [Azure Data Lake Storage Gen1- eller Gen 2-konto](https://azure.microsoft.com/services/data-lake-store/) som du väljer.

Du kan konfigurera avbildningsfunktionen med [Azure Portal](https://portal.azure.com) när du skapar händelsehubben. Du kan antingen samla in data till en Azure [Blob-lagringsbehållare](https://azure.microsoft.com/services/storage/blobs/) eller till ett [Azure Data Lake Storage Gen 1- eller Gen 2-konto.](https://azure.microsoft.com/services/data-lake-store/)

Mer information finns i [Översikt över Event Hubs Capture][capture-overview].

## <a name="capture-data-to-azure-storage"></a>Samla in data till Azure Storage

När du skapar en händelsehubb kan du aktivera avbildningsfunktionen genom att klicka på knappen **På** på portalskärmen **Skapa händelsehubb**. Sedan anger du ett lagringskonto och en container genom att klicka på **Azure Storage** i rutan **Capture Provider** (Avbildningsprovider). Eftersom tjänst-till-tjänst-autentisering används för lagring i avbildningsfunktionen i Event Hubs behöver du inte ange någon anslutningssträng för lagringen. Resursväljaren väljer automatiskt rätt resurs-URI för ditt lagringskonto. Om du använder Azure Resource Manager måste du ange den här URI:n uttryckligen som en sträng.

Standardvärdet för Tidsperiod är 5 minuter. Lägsta värde är 1 och högsta är 15. Du kan ange intervallet 10–500 MB för **Storlek**.

![Tidsperiod för avbildning][1]

> [!NOTE]
> Du kan aktivera eller inaktivera tomma filer om inga händelser inträffar i fönstret Hämta. 

## <a name="capture-data-to-azure-data-lake-storage-gen-2"></a>Samla in data till Azure Data Lake Storage Gen 2 

1. Följ [Skapa en lagringskontoartikel](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) för att skapa ett Azure Storage-konto. Ange **hierarkiskt namnområde** aktiverat på fliken **Avancerat** för att göra det till ett Azure Data Lake Storage Gen 2-konto. **Advanced**
2. När du skapar en händelsehubb gör du följande: 

    1. Välj **På** för **hämtning**. 
    2. Välj **Azure Storage** som insamlingsprovider. Alternativet **Azure Data Lake Store** som du ser för **capture-providern** är för gen 1 i Azure Data Lake Storage. Om du vill använda en gen 2 i Azure Data Lake Storage väljer du **Azure Storage**.
    2. Välj knappen **Välj behållare.** 

        ![Aktivera insamling till Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/data-lake-storage-gen2.png)
3. Välj **Azure Data Lake Storage Gen 2-kontot** i listan. 

    ![Välj Lagringsgenom 2 för datasjö](./media/event-hubs-capture-enable-through-portal/select-data-lake-storage-gen2.png)
4. Markera **behållaren** (filsystemet i Data Lake Storage Gen 2).

    ![Välj filsystem i lagringen](./media/event-hubs-capture-enable-through-portal/select-file-system-data-lake-storage.png)
5. På sidan **Skapa händelsehubb** väljer du **Skapa**. 

    ![Knappen Välj Skapa](./media/event-hubs-capture-enable-through-portal/create-event-hub-data-lake-storage.png)

    > [!NOTE]
    > Behållaren som du skapar i en Azure Data Lake Storage Gen 2 med det här användargränssnittet visas under **Filsystem** i **Storage Explorer**. På samma sätt visas det filsystem som du skapar i ett Data Lake Storage Gen 2-konto som en behållare i det här användargränssnittet. 


## <a name="capture-data-to-azure-data-lake-storage-gen-1"></a>Samla in data till Azure Data Lake Storage Gen 1 

Om du vill samla in data till Azure Data Lake Storage Gen 1 skapar du ett Data Lake Storage Gen 1-konto och en händelsehubb:

### <a name="create-an-azure-data-lake-storage-gen-1-account-and-folders"></a>Skapa ett Azure Data Lake Storage Gen 1-konto och mappar

1. Skapa ett DataSjölagringskonto enligt instruktionerna i [Komma igång med Azure Data Lake Storage Gen 1 med Azure-portalen](../data-lake-store/data-lake-store-get-started-portal.md).
2. Följ instruktionerna i avsnittet [Tilldela behörigheter till händelsehubbar](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) för att skapa en mapp i kontot DataSjölagring gen 1 där du vill samla in data från Event Hubs och tilldela behörigheter till eventhubbar så att den kan skriva data till ditt Data Lake Storage Gen 1-konto.  


### <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. Händelsehubben måste finnas i samma Azure-prenumeration som Azure Data Lake Storage Gen 1-konto som du skapade. Skapa en händelsehubb genom att klicka på knappen **På** under **Capture** (Avbilda) på portalsidan **Skapa händelsehubb**. 
2. Välj **Azure Data Lake Store** i rutan **Capture Provider** (Avbildningsprovider) på portalsidan **Skapa händelsehubb**.
3. I **Välj butik** bredvid listrutan **Datasjölagring** anger du det DataSjölagringsgend 1-konto som du skapade tidigare och anger sökvägen till den datamapp som du skapade i fältet **Datasjösökväg.**

    ![Välja Data Lake Storage-konto][3]


## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Lägga till eller konfigurera avbildningsfunktionen i en befintlig händelsehubb

Du kan konfigurera avbildningsfunktionen i befintliga händelsehubbar som finns i namnområden för Event Hubs. Om du vill aktivera avbildningsfunktionen i en befintlig händelsehubb eller om du vill ändra inställningarna för avbildningsfunktionen, klickar du på namnområdet för att läsa in översiktsskärmen. Klicka sedan på den händelsehubb som du vill aktivera eller ändra avbildningsinställningen för. Klicka slutligen på alternativet **Avbilda** på vänster sida på sidan som är öppen och redigera inställningarna enligt följande bilder:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![Konfigurera Azure Blob Storage][2]

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

![Konfigurera Azure Data Lake Storage Gen 2](./media/event-hubs-capture-enable-through-portal/configure-data-lake-storage-gen2.png)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1 

![Konfigurera Azure Data Lake Storage][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Nästa steg

- Läs mer om Event Hubs Capture i [Översikt över Event Hubs Capture][capture-overview].
- Du kan också konfigurera avbildningsfunktionen i Event Hubs med hjälp av Azure Resource Manager-mallar. Mer information finns i [Skapa ett namnområde för Event Hubs med en händelsehubb och aktivera avbildning med hjälp av en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Lär dig hur du skapar en Azure Event Grid-prenumeration med en Event Hubs-namnrymd som dess källa](store-captured-data-data-warehouse.md)
- [Komma igång med Azure Data Lake Store med Azure-portalen](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md
