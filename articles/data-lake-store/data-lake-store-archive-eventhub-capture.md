---
title: "Samla in data från Händelsehubbar i Azure Data Lake Store | Microsoft Docs"
description: "Använd Azure Data Lake Store att samla in data från Händelsehubbar"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: a9e69576958ae96d22a4eb03d0df429f0b307298
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Använd Azure Data Lake Store att samla in data från Händelsehubbar

Lär dig hur du använder Azure Data Lake Store att samla in data som tagits emot av Händelsehubbar i Azure.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Ett Azure Data Lake Store-konto**. Anvisningar om hur du skapar en finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Ett namnområde för Händelsehubbar**. Instruktioner finns i [skapa ett namnområde för Händelsehubbar](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Kontrollera att Data Lake Store-konto och namnområdet Händelsehubbar finns i samma Azure-prenumerationen.


## <a name="assign-permissions-to-event-hubs"></a>Tilldela behörigheter till Händelsehubbar

I det här avsnittet skapar du en mapp i kontot där du vill samla in data från Händelsehubbar. Du också tilldela behörigheter Händelsehubbar så att det kan skriva data till ett Data Lake Store-konto. 

1. Öppna Data Lake Store-konto där du vill samla in data från Händelsehubbar och klicka sedan på **Data Explorer**.

    ![Data Lake Store data explorer](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Datasjölager data explorer")

2.  Klicka på **ny mapp** och ange sedan ett namn på mappen där du vill samla in data.

    ![Skapa en ny mapp i Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "skapa en ny mapp i Data Lake Store")

3. Tilldela behörigheter i roten på Data Lake Store. 

    a. Klicka på **Data Explorer**, Välj roten på Data Lake Store-kontot och klicka sedan på **åtkomst**.

    ![Tilldela behörigheter för Data Lake Store roten](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "tilldela behörigheter för Data Lake Store-rot")

    b. Under **åtkomst**, klickar du på **Lägg till**, klickar du på **Välj användare eller grupp**, och sök sedan efter `Microsoft.EventHubs`. 

    ![Tilldela behörigheter för Data Lake Store roten](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "tilldela behörigheter för Data Lake Store-rot")
    
    Klicka på **Välj**.

    c. Under **tilldela behörigheter**, klickar du på **Välj behörigheter**. Ange **behörigheter** till **köra**. Ange **lägga till** till **den här mappen och alla underordnade**. Ange **lägga till som** till **en behörighetspost för åtkomst och en standard behörighetspost**.

    ![Tilldela behörigheter för Data Lake Store roten](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "tilldela behörigheter för Data Lake Store-rot")

    Klicka på **OK**.

4. Tilldela behörigheter för mappen under Data Lake Store-konto där du vill samla in data.

    a. Klicka på **Data Explorer**, Välj mappen i Data Lake Store-kontot och klicka sedan på **åtkomst**.

    ![Tilldela behörigheter för Data Lake Store-mapp](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "tilldela behörigheter för Data Lake Store-mapp")

    b. Under **åtkomst**, klickar du på **Lägg till**, klickar du på **Välj användare eller grupp**, och sök sedan efter `Microsoft.EventHubs`. 

    ![Tilldela behörigheter för Data Lake Store-mapp](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "tilldela behörigheter för Data Lake Store-mapp")
    
    Klicka på **Välj**.

    c. Under **tilldela behörigheter**, klickar du på **Välj behörigheter**. Ange **behörigheter** till **läsa, skriva,** och **köra**. Ange **lägga till** till **den här mappen och alla underordnade**. Slutligen, Ställ **lägga till som** till **en behörighetspost för åtkomst och en standard behörighetspost**.

    ![Tilldela behörigheter för Data Lake Store-mapp](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "tilldela behörigheter för Data Lake Store-mapp")
    
    Klicka på **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Konfigurera Händelsehubbar för att samla in data till Data Lake Store

I det här avsnittet skapar du en Händelsehubb i ett namnområde för Händelsehubbar. Du kan också konfigurera Händelsehubben för att samla in data till ett Azure Data Lake Store-konto. Det här avsnittet förutsätter att du redan har skapat ett namnområde för Händelsehubbar.

2. Från den **översikt** i Händelsehubbar namnområde, klickar du på **+ Event Hub**.

    ![Skapa Händelsehubb](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "skapar Händelsehubb")

3. Ange följande värden om du vill konfigurera Händelsehubbar för att samla in data till Data Lake Store.

    ![Skapa Händelsehubb](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "skapar Händelsehubb")

    a. Ange ett namn för Händelsehubben.
    
    b. Den här självstudien anges **antalet partitioner** och **meddelandet kvarhållning** standardvärdena.
    
    c. Ange **avbilda** till **på**. Ange den **tidsfönstret** (hur ofta du vill avbilda) och **storlek fönstret** (datastorlek att avbilda). 
    
    d. För **avbilda providern**väljer **Azure Data Lake Store** och välj Data Lake Store du skapade tidigare. För **Data Lake sökvägen**, ange namnet på mappen som du skapade i Data Lake Store-konto. Du behöver bara ange den relativa sökvägen till mappen.

    e. Lämna den **exempel avbilda filformat namnet** till standardvärdet. Det här alternativet styr mappstrukturen som skapas under mappen avbildning.

    f. Klicka på **Skapa**.

## <a name="test-the-setup"></a>Testa installationen

Nu kan du testa lösningen genom att skicka data till Azure-Händelsehubb. Följ anvisningarna på [skicka händelser till Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). När du startar data skickas, visas data som visas i Data Lake Store med mappstrukturen som du angav. Till exempel finns en mappstruktur som visas i följande skärmbild i Data Lake Store.

![Exempel på EventHub-data i Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "exempel EventHub-data i Data Lake Store")

> [!NOTE]
> Även om du inte har meddelanden som skickas till Händelsehubbar skriver Händelsehubbar tom filer med endast rubrikerna till Data Lake Store-konto. Filerna skrivs med samma tidsintervall som du angav när du skapar Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analysera data i Data Lake Store

När data i Data Lake Store kan köra du analytiska jobb ska bearbetas och matar data. Se [USQL Avro exempel](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) på hur du gör detta med hjälp av Azure Data Lake Analytics.
  

## <a name="see-also"></a>Se även
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Kopiera data från Azure Storage-Blobbar till Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
