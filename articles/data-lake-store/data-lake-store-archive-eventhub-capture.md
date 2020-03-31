---
title: Samla in data från eventhubbar i Azure Data Lake Storage Gen1 | Microsoft-dokument
description: Använda Azure Data Lake Storage Gen1 för att samla in data från eventhubbar
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265667"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Använda Azure Data Lake Storage Gen1 för att samla in data från eventhubbar

Lär dig hur du använder Azure Data Lake Storage Gen1 för att samla in data som tas emot av Azure Event Hubs.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Ett Azure Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar en finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Ett namnområde för händelsehubbar**. Instruktioner finns i [Skapa ett namnområde för händelsehubbar](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Kontrollera att Data Lake Storage Gen1-kontot och händelsehubbars namnområde finns i samma Azure-prenumeration.


## <a name="assign-permissions-to-event-hubs"></a>Tilldela behörigheter till eventhubbar

I det här avsnittet skapar du en mapp i kontot där du vill samla in data från Event Hubs. Du tilldelar också behörigheter till eventhubbar så att de kan skriva data till ett Data Lake Storage Gen1-konto. 

1. Öppna kontot Datasjölagringsgenm1 där du vill samla in data från eventhubbar och klicka sedan på **Data Explorer**.

    ![Datautforskare för Datasjölagring Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Datautforskare för Datasjölagring Gen1")

1.  Klicka på **Ny mapp** och ange sedan ett namn för en mapp där du vill samla in data.

    ![Skapa en ny mapp i Gen1 för lagring av Datasjö](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Skapa en ny mapp i Gen1 för lagring av Datasjö")

1. Tilldela behörigheter vid roten av Data Lake Storage Gen1. 

    a. Klicka på **Data Explorer,** markera roten för datasjölagringsgenm1-kontot och klicka sedan på **Access**.

    ![Tilldela behörigheter för datasjölagringgend1-roten](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Tilldela behörigheter för datasjölagringgend1-roten")

    b. Klicka på **Lägg till**under **Access,** klicka på `Microsoft.EventHubs`Välj användare eller **grupp**och sök sedan efter . 

    ![Tilldela behörigheter för datasjölagringgend1-roten](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Tilldela behörigheter för datasjölagringgend1-roten")
    
    Klicka på **Markera**.

    c. Klicka på Välj **behörigheter**under **Tilldela behörigheter**. Ange **behörigheter** som ska **köras**. Ange **Lägg till i** den här mappen och alla **underordnade**. Ange **Lägg till som** i en **behörighetspost för åtkomst och en standardbehörighetspost**.

    > [!IMPORTANT]
    > När du skapar en ny mapphierarki för att samla in data som tas emot av Azure Event Hubs är detta ett enkelt sätt att säkerställa åtkomst till målmappen.  Det kan dock ta lång tid att lägga till behörigheter för alla underordnade i en mapp på den översta nivån med många underordnade filer och mappar.  Om rotmappen innehåller ett stort antal filer och mappar kan det gå `Microsoft.EventHubs` snabbare att lägga till **Körbehörigheter** för varje mapp i sökvägen till slutmålsmappen. 

    ![Tilldela behörigheter för datasjölagringgend1-roten](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Tilldela behörigheter för datasjölagringgend1-roten")

    Klicka på **OK**.

1. Tilldela behörigheter för mappen under kontot DataSjölagringsgend1 där du vill samla in data.

    a. Klicka på **Data Explorer,** markera mappen i datasjölagringsgenm1-kontot och klicka sedan på **Access**.

    ![Tilldela behörigheter för mappen DataSjölagring gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Tilldela behörigheter för mappen DataSjölagring gen1")

    b. Klicka på **Lägg till**under **Access,** klicka på `Microsoft.EventHubs`Välj användare eller **grupp**och sök sedan efter . 

    ![Tilldela behörigheter för mappen DataSjölagring gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Tilldela behörigheter för mappen DataSjölagring gen1")
    
    Klicka på **Markera**.

    c. Klicka på Välj **behörigheter**under **Tilldela behörigheter**. Ange **behörigheter** för att **läsa, skriva** och **köra**. Ange **Lägg till i** den här mappen och alla **underordnade**. Slutligen anger du Lägg till **som** i **en åtkomstbehörighetspost och en standardbehörighetspost**.

    ![Tilldela behörigheter för mappen DataSjölagring gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Tilldela behörigheter för mappen DataSjölagring gen1")
    
    Klicka på **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Konfigurera händelsehubbar för att samla in data till Data Lake Storage Gen1

I det här avsnittet skapar du en händelsehubb i ett namnområde för eventhubbar. Du konfigurerar också händelsehubben för att samla in data till ett Azure Data Lake Storage Gen1-konto. Det här avsnittet förutsätter att du redan har skapat ett namnområde för eventhubbar.

1. Klicka på **+ Händelsehubben**i **fönstret Översikt** i namnområdet Händelserhubrar.

    ![Skapa händelsehubb](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Skapa händelsehubb")

1. Ange följande värden för att konfigurera händelsehubbar för att samla in data till Data Lake Storage Gen1.

    ![Skapa händelsehubb](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Skapa händelsehubb")

    a. Ange ett namn för händelsehubben.
    
    b. För den här självstudien anger du **partitionsantal** och **meddelandekvarhållning** till standardvärdena.
    
    c. Ställ in **Capture** **på På**. Ange **tidsfönstret** (hur ofta du ska samla in) och **Storleksfönstret** (datastorleken som ska samlas in). 
    
    d. För **Capture Provider**väljer du Azure Data Lake **Store** och väljer sedan det DataSjölagringsgenm1-konto som du skapade tidigare. För **Sökväg för Datasjö**anger du namnet på mappen som du skapade i datasjölagringsgenm1-kontot. Du behöver bara ange den relativa sökvägen till mappen.

    e. Lämna **filnamnsformaten Sample** till standardvärdet. Det här alternativet styr mappstrukturen som skapas under insamlingsmappen.

    f. Klicka på **Skapa**.

## <a name="test-the-setup"></a>Testa installationen

Du kan nu testa lösningen genom att skicka data till Azure Event Hub. Följ instruktionerna på [Skicka händelser till Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). När du har börjat skicka data visas data som återspeglas i DataSjölagring Gen1 med hjälp av mappstrukturen som du angav. Du ser till exempel en mappstruktur, som visas i följande skärmbild, i ditt Data Lake Storage Gen1-konto.

![Exempel på EventHub-data i Gen1 för lagring av datasjö](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Exempel på EventHub-data i Gen1 för lagring av datasjö")

> [!NOTE]
> Även om du inte har meddelanden som kommer in i Event Hubs, skriver Event Hubs tomma filer med bara rubrikerna i Data Lake Storage Gen1-kontot. Filerna skrivs samtidigt som du angav när du skapade händelsehubbar.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analysera data i DataSjölagring Gen1

När data finns i Data Lake Storage Gen1 kan du köra analytiska jobb för att bearbeta och knapra data. Se [USQL Avro Exempel](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) på hur du gör detta med Hjälp av Azure Data Lake Analytics.
  

## <a name="see-also"></a>Se även
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Kopiera data från Azure Storage Blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
