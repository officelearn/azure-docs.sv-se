---
title: "Aktivera avbildningsfunktionen för Azure Event Hubs via portalen | Microsoft Docs"
description: "Använd Azure Portal för att aktivera avbildningsfunktionen i Event Hubs."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5420c1cdefa99ff30320dd757e04aa0cafc792bc
ms.contentlocale: sv-se
ms.lasthandoff: 08/29/2017

---

# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Aktivera avbildningsfunktionen i Event Hubs med Azure Portal

Med Azure [Event Hubs Capture][capture-overview] kan du automatiskt leverera strömmande data i Event Hubs till valfritt [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)- eller [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-konto.

Du kan konfigurera avbildningsfunktionen med [Azure Portal](https://portal.azure.com) när du skapar händelsehubben. Du kan antingen samla in data till en Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/)-behållare eller till ett [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)-konto.

Mer information finns i [Översikt över Event Hubs Capture][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Samla in data till ett Azure Storage-konto  

När du skapar en händelsehubb kan du aktivera avbildningsfunktionen genom att klicka på knappen **På** på portalskärmen **Skapa händelsehubb**. Sedan anger du ett lagringskonto och en behållare genom att klicka på **Azure Storage** i rutan **Capture Provider** (Avbildningsprovider). Eftersom tjänst-till-tjänst-autentisering används för lagring i avbildningsfunktionen i Event Hubs behöver du inte ange någon anslutningssträng för lagringen. Resursväljaren väljer automatiskt rätt resurs-URI för ditt lagringskonto. Om du använder Azure Resource Manager måste du ange den här URI:n uttryckligen som en sträng.

Standardvärdet för Tidsperiod är 5 minuter. Lägsta värde är 1 och högsta är 15. Du kan ange intervallet 10–500 MB för **Storlek**.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Samla in data till ett Azure Data Lake Store-konto

Om du vill samla in data till Azure Data Lake Store skapar du ett Data Lake Store-konto och en händelsehubb:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Skapa ett Azure Data Lake Store-konto och mappar

1. Skapa ett Data Lake Store-konto enligt anvisningarna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md). 
2. Skapa en mapp under det här kontot enligt anvisningarna i avsnittet [Skapa mappar i Azure Data Lake Store-konto](../data-lake-store/data-lake-store-get-started-portal.md#createfolder).
3. Klicka på **Datautforskaren** på sidan för Data Lake Store-kontot.
4. Klicka på **Åtkomst**.
5. Klicka på **Lägg till**.
6. Skriv **Microsoft.EventHubs** i rutan **Sök efter namn eller e-postadress** och välj sedan det här alternativet. 
7. Fliken **Behörigheter** visas. Ange behörigheterna som på bilden:

    ![][6]

8. Klicka på **OK**.
9. Nu skapar du en mapp i rotmappen genom att bläddra till målmappen och klicka på mappnamnet.
10. Klicka på **Åtkomst**.
11. Klicka på **Lägg till**.
12. Skriv **Microsoft.EventHubs** i rutan **Sök efter namn eller e-postadress** och välj sedan det här alternativet.
13. Fliken **Behörigheter** visas igen. Ange behörigheter som på bilden:

    ![][5]

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

1. Observera att händelsehubben måste vara i samma Azure-prenumeration som den Azure Data Lake Store som du precis skapade. Skapa en händelsehubb genom att klicka på knappen **På** under **Capture** (Avbilda) på portalsidan **Skapa händelsehubb**. 
2. Välj **Azure Data Lake Store** i rutan **Capture Provider** (Avbildningsprovider) på portalsidan **Skapa händelsehubb**.
3. I **Välj Data Lake Store** anger du det Data Lake Store-konto som du skapade tidigare och i fältet **Data Lake Path** (Sökväg till Data Lake) anger du sökvägen till den datamapp som du skapade.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Lägga till eller konfigurera avbildningsfunktionen i en befintlig händelsehubb

Du kan konfigurera avbildningsfunktionen i befintliga händelsehubbar som finns i namnområden för Event Hubs. Om du vill aktivera avbildningsfunktionen i en befintlig händelsehubb eller om du vill ändra inställningarna för avbildningsfunktionen, klickar du på namnområdet för att läsa in skärmen **Essentials**. Klicka sedan på den händelsehubb som du vill aktivera eller ändra avbildningsinställningen för. Klicka slutligen i avsnittet **Egenskaper** på sidan som är öppen och redigera inställningarna för avbildningsfunktionen enligt följande bilder:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png
[5]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture5.png
[6]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture6.png

## <a name="next-steps"></a>Nästa steg

- Läs mer om Event Hubs Capture i [Översikt över Event Hubs Capture][capture-overview].
- Du kan också konfigurera avbildningsfunktionen i Event Hubs med hjälp av Azure Resource Manager-mallar. Mer information finns i [Skapa ett namnområde för Event Hubs med en händelsehubb och aktivera avbildning med hjälp av en Azure Resource Manager-mall](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).

[capture-overview]: event-hubs-capture-overview.md
