---
title: Ansluta till ett kunskaps lager med Power BI-Azure Search
description: Anslut en Azure Search kunskaps lager med Power BI för analys och utforskning.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fa6187dc270b18f513b5ee4046d0a6c085f9ae12
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963052"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Anslut ett kunskaps lager med Power BI

> [!Note]
> Kunskaps lagret är i för hands version och bör inte användas i produktionen. [Azure Search REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>
I den här artikeln får du lära dig hur du ansluter och utforskar ett kunskaps lager med hjälp av Power Query i Power BI Desktop-appen. Om du vill skapa ett kunskaps lager exempel som används i den här genom gången, se [skapa ett kunskaps lager i Azure Portal](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Förutsättningar

+ Följ stegen i [skapa ett kunskaps lager i Azure Portal](knowledge-store-create-portal.md) för att skapa det exempel kunskaps arkiv som används i den här genom gången. Du kommer också att behöva namnet på det Azure Storage-konto som du använde för att skapa kunskaps lagret, tillsammans med dess åtkomst nyckel från Azure Portal.

+ [Installera Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>Anslut med Power BI

1. Starta Power BI Desktop och klicka på **Hämta data**.

1. I fönstret **Hämta data** väljer du **Azure**och väljer sedan **Azure Table Storage**.

1. Klicka på **Anslut**.

1. För **konto namn eller URL**anger du i Azure Storage konto namn (den fullständiga URL: en skapas åt dig).

1. Ange lagrings konto nyckeln om du uppmanas att göra det.

1. Välj tabellerna *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases*och *hotelReviewsSsPages* . Tabellerna är Azure Table-projektioner av hotell gransknings exempel data och inkluderar de kognitiva tjänstens anrikninger som valdes när kunskaps lagret skapades.

1. Klicka på **Läs in**.

1. Klicka på **Redigera frågor** i det översta menyfliksområdet för att öppna **Power Query redigeraren**.

   ![Öppna Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Öppna Power Query")

1. Välj *hotelReviewsSsDocument*och ta sedan bort kolumnerna *PartitionKey*, *RowKey*och *timestamp* . 

   ![Redigera tabeller](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Redigera tabeller")

1. Klicka på ikonen med motsatta pilar längst upp till höger i tabellen för att expandera *innehållet*. När listan med kolumner visas väljer du alla kolumner och avmarkerar kolumner som börjar med ' metadata '. Klicka på **OK** för att visa de markerade kolumnerna.

   ![Redigera tabeller](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandera innehåll")

1. Ändra data typen för följande kolumner genom att klicka på ABC-123-ikonen längst upp till vänster i kolumnen.

   + För *innehållet. latitud* och *Contents. longitud*väljer du **decimal tal**.
   + För *Content. reviews_date* och *Content. Reviews_dateAdded*väljer du **datum/tid**.

   ![Ändra data typer](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Ändra data typer")

1. Välj *hotelReviewsSsPages*och upprepa sedan steg 9 och 10 för att ta bort kolumnerna och expandera *innehållet*.
1. Ändra data typen för *Content. SentimentScore* till **decimal tal**.
1. Välj *hotelReviewsSsKeyPhrases* och upprepa steg 9 och 10 om du vill ta bort kolumnerna och expandera *innehållet*. Det finns inga ändringar av data typen för den här tabellen.

1. I kommando fältet klickar du på **Stäng och Verkställ**.

1. Klicka på panelen modell i det vänstra navigerings fönstret och verifiera att Power BI visar relationer mellan alla tre tabeller.

   ![Verifiera relationer](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Verifiera relationer")

1. Dubbelklicka på varje relation och se till att **kors filter riktningen** är inställd på **båda**.  Detta gör att dina visuella objekt kan uppdateras när ett filter tillämpas.

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Rensa

När du arbetar med din egen prenumeration är det en bra idé i slutet av ett projekt för att identifiera om du fortfarande behöver de resurser som du har skapat. Resurser som har lämnats igång kostar dig pengar. Du kan ta bort resurser individuellt eller ta bort resurs gruppen för att ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

Information om hur du utforskar det här kunskaps lagret med hjälp av Storage Explorer finns i följande artikel.

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)

Information om hur du skapar ett kunskaps lager med hjälp av REST-API: er och Postman finns i följande artikel.  

> [!div class="nextstepaction"]
> [Skapa ett kunskaps lager i REST](knowledge-store-howto.md)
