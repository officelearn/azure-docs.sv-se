---
title: Samla in data från Händelsehubbar i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använd Azure Data Lake Storage Gen1 att samla in data från Event Hubs
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884522"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Använd Azure Data Lake Storage Gen1 att samla in data från Event Hubs

Lär dig hur du använder Azure Data Lake Storage Gen1 att samla in data som tas emot av Händelsehubbar i Azure.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Ett konto med Azure Data Lake Storage Gen1**. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Ett namnområde för Event Hubs**. Anvisningar finns i [skapa ett namnområde för Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Kontrollera att kontot Data Lake Storage Gen1 och Event Hubs-namnområdet finns i samma Azure-prenumeration.


## <a name="assign-permissions-to-event-hubs"></a>Tilldela behörigheter till Event Hubs

I det här avsnittet skapar du en mapp i konton där du vill samla in data från Händelsehubbar. Också tilldela du behörigheter till Event Hubs så att det kan skriva data till ett Data Lake Storage Gen1-konto. 

1. Öppna det Data Lake Storage Gen1 konto där du vill samla in data från Event Hubs och klicka sedan på **Datautforskaren**.

    ![Data Lake Storage Gen1 datautforskaren](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Data Lake Storage Gen1 datautforskaren")

1.  Klicka på **ny mapp** och ange sedan ett namn på mappen där du vill samla in data.

    ![Skapa en ny mapp i Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "skapa en ny mapp i Data Lake Storage Gen1")

1. Tilldela behörigheter i roten för Data Lake Storage Gen1. 

    a. Klicka på **Datautforskaren**, Välj roten Gen1 för Data Lake Storage-kontot och klicka sedan på **åtkomst**.

    ![Tilldela behörigheter för roten för Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "tilldela behörigheter för roten för Data Lake Storage Gen1")

    b. Under **åtkomst**, klickar du på **Lägg till**, klickar du på **Välj användare eller grupp**, och söker sedan efter `Microsoft.EventHubs`. 

    ![Tilldela behörigheter för roten för Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "tilldela behörigheter för roten för Data Lake Storage Gen1")
    
    Klicka på **Välj**.

    c. Under **tilldela behörigheter**, klickar du på **Select-behörigheter**. Ange **behörigheter** till **köra**. Ange **lägga till i** till **den här mappen och alla underordnade**. Ange **lägga till som** till **ett åtkomstbehörighetsinlägg och en standardbehörighetsinlägg**.

    > [!IMPORTANT]
    > När du skapar en ny mapphierarki för insamling av data som tas emot av Händelsehubbar i Azure kan är det här ett enkelt sätt att kontrollera åtkomst till målmappen.  Lägger till behörigheter till alla underordnade i en mapp på översta nivån med många underordnade filer och mappar kan dock ta lång tid.  Om din rotmapp innehåller ett stort antal filer och mappar kan det vara snabbare att lägga till **kör** behörigheter för `Microsoft.EventHubs` individuellt till varje mapp i sökvägen till mappen slutdestinationen. 

    ![Tilldela behörigheter för roten för Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "tilldela behörigheter för roten för Data Lake Storage Gen1")

    Klicka på **OK**.

1. Tilldela behörigheter för mappen under kontot Data Lake Storage Gen1 där du vill samla in data.

    a. Klicka på **Datautforskaren**, Välj mappen i Data Lake Storage Gen1-kontot och klicka sedan på **åtkomst**.

    ![Tilldela behörigheter för mappen Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "tilldela behörigheter för mappen Data Lake Storage Gen1")

    b. Under **åtkomst**, klickar du på **Lägg till**, klickar du på **Välj användare eller grupp**, och söker sedan efter `Microsoft.EventHubs`. 

    ![Tilldela behörigheter för mappen Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "tilldela behörigheter för mappen Data Lake Storage Gen1")
    
    Klicka på **Välj**.

    c. Under **tilldela behörigheter**, klickar du på **Select-behörigheter**. Ange **behörigheter** till **läsa, skriva,** och **köra**. Ange **lägga till i** till **den här mappen och alla underordnade**. Slutligen anger **lägga till som** till **ett åtkomstbehörighetsinlägg och en standardbehörighetsinlägg**.

    ![Tilldela behörigheter för mappen Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "tilldela behörigheter för mappen Data Lake Storage Gen1")
    
    Klicka på **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurera Event Hubs för att samla in data till Data Lake Storage Gen1

I det här avsnittet skapar du en Händelsehubb i ett namnområde för Event Hubs. Du kan även konfigurera Event Hub för att samla in data till ett Azure Data Lake Storage Gen1-konto. Det här avsnittet förutsätter att du redan har skapat ett namnområde för Event Hubs.

1. Från den **översikt** klickar du på rutan i Event Hubs-namnområdet **+ Event Hub**.

    ![Skapa Händelsehubb](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "skapa Händelsehubb")

1. Ange följande värden om du vill konfigurera Event Hubs för att samla in data till Data Lake Storage Gen1.

    ![Skapa Händelsehubb](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "skapa Händelsehubb")

    a. Ange ett namn för Event Hub.
    
    b. För den här självstudiekursen ställer du in **Partitionsantal** och **meddelandelagring** standardvärdena.
    
    c. Ange **avbilda** till **på**. Ange den **tidsfönster** (hur ofta du vill avbilda) och **storleksgräns** (datastorlek att samla in). 
    
    d. För **Capture Provider**väljer **Azure Data Lake Store** och välj sedan det Data Lake Storage Gen1-konto som du skapade tidigare. För **sökväg till Data Lake**, anger du namnet på den mapp du skapade i Data Lake Storage Gen1-konto. Du behöver bara den relativa sökvägen till mappen.

    e. Lämna den **exempel avbildning på filformat** till standardvärdet. Det här alternativet reglerar mappstrukturen som har skapats under mappen avbildning.

    f. Klicka på **Skapa**.

## <a name="test-the-setup"></a>Testa installationen

Nu kan du testa lösningen genom att skicka data till Azure Event Hub. Följ anvisningarna på [skicka händelser till Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). När du börja skicka data måste du se data i Data Lake Storage Gen1 med mappen struktur du angav. Exempelvis kan se du en mappstruktur enligt följande skärmbild i ditt Data Lake Storage Gen1-konto.

![Exempel på EventHub-data i Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "exempel EventHub-data i Data Lake Storage Gen1")

> [!NOTE]
> Även om du inte har meddelanden som skickas till Event Hubs, skriver Event Hubs tomma filer med endast rubrikerna in på Data Lake Storage Gen1-kontot. Filerna som är skrivna med samma tidsintervall som du angav när du skapade Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analysera data i Data Lake Storage Gen1

När data finns i Data Lake Storage Gen1, kan du köra analytiska jobb ska bearbetas och bearbeta data. Se [USQL Avro exempel](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) på hur du gör detta med hjälp av Azure Data Lake Analytics.
  

## <a name="see-also"></a>Se också
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopiera data från Azure Storage BLOB till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
