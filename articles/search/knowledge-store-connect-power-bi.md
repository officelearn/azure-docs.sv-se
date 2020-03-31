---
title: Ansluta till en kunskapsbutik (förhandsgranskning) med Power BI
titleSuffix: Azure Cognitive Search
description: Anslut en Azure Cognitive Search knowledge store (förhandsversion) med Power BI för analys och utforskning.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 4fd71a7f322cb2672eb485f17e4de2619a7c2d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270033"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Ansluta ett kunskapslager med Power BI

> [!IMPORTANT] 
> Knowledge Store är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller förhandsgranskningsfunktioner. Det finns för närvarande begränsat portalstöd och inget .NET SDK-stöd.

I den här artikeln får du lära dig hur du ansluter till och utforskar ett kunskapslager med Power Query i Power BI Desktop-appen. Du kan komma igång snabbare med mallar eller skapa en anpassad instrumentpanel från grunden.

+ Följ stegen i [Skapa ett kunskapsarkiv i Azure-portalen](knowledge-store-create-portal.md) eller [Skapa ett Azure Cognitive Search-kunskapslager med hjälp av REST](knowledge-store-create-rest.md) för att skapa exempelkunskapsarkivet som används i den här genomgången. Du behöver också namnet på Azure Storage-kontot som du använde för att skapa kunskapsarkivet, tillsammans med dess åtkomstnyckel från Azure-portalen.

+ [Installera Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Exempel på Power BI-mall - endast Azure-portal

När du skapar ett [kunskapsarkiv med Azure-portalen](knowledge-store-create-portal.md)kan du välja att hämta en [Power BI-mall](https://github.com/Azure-Samples/cognitive-search-templates) på den andra sidan i guiden **Importera data.** Den här mallen ger dig flera visualiseringar, till exempel WordCloud och Network Navigator, för textbaserat innehåll. 

Klicka på **Hämta Power BI-mall** på sidan **Lägg till kognitiva kunskaper** för att hämta och hämta mallen från den offentliga GitHub-platsen. Guiden ändrar mallen så att den passar formen på dina data, som fångas i de kunskapslagerprojektioner som anges i guiden. Därför varierar mallen du hämtar varje gång du kör guiden, förutsatt att olika dataindata och färdighetsval.

![Exempel på Azure Cognitive Search Power BI-mall](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Exempel på Power BI-mall")

> [!NOTE]
> Även om mallen hämtas medan guiden är mitt i flygningen, måste du vänta tills kunskapsarkivet faktiskt skapas i Azure Table storage innan du kan använda den.

## <a name="connect-with-power-bi"></a>Anslut med Power BI

1. Starta Power BI Desktop och klicka på **Hämta data**.

1. I fönstret **Hämta data** väljer du **Azure**och väljer sedan Azure **Table Storage**.

1. Klicka på **Anslut**.

1. För **Kontonamn eller URL**anger du i ditt Azure Storage-kontonamn (den fullständiga URL:en skapas åt dig).

1. Om du uppmanas till det anger du lagringskontonyckeln.

1. Välj de tabeller som innehåller de hotellrecensioner data som skapats av föregående genomgångar. 

   + För portalgenomgången är tabellnamn *hotelReviewsSSSDocument,* *hotelReviewsSsEntities,* *hotelReviewsSSKeyPhrases*och *hotelReviewsSsPages*. 
   
   + För REST-genomgången är tabellnamn *hotelReviewsDocument,* *hotelReviewsPages,* *hotelReviewsKeyPhrases*och *hotelReviewsSentiment*.

1. Klicka på **Läs in**.

1. **Öppna** **Power Query Editor**i det övre menyfliksområdet.

   ![Öppna Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Öppna Power Query")

1. Välj *hotelReviewsSsDocument*och ta sedan bort kolumnerna *PartitionKey,* *RowKey*och *Timestamp.* 
   ![Redigera tabeller](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Redigera tabeller")

1. Klicka på ikonen med motsatta pilar längst upp till höger i tabellen för att expandera *innehållet*. När listan med kolumner visas markerar du alla kolumner och avmarkerar sedan kolumner som börjar med "metadata". Klicka på **OK** om du vill visa de markerade kolumnerna.

   ![Redigera tabeller](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandera innehåll")

1. Ändra datatypen för följande kolumner genom att klicka på ABC-123-ikonen längst upp till vänster i kolumnen.

   + För *content.latitude* och *Content.longitude*väljer du **Decimaltal**.
   + För *Content.reviews_date* och *Content.reviews_dateAdded*väljer du **Datum/tid**.

   ![Ändra datatyper](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Ändra datatyper")

1. Välj *hotelReviewsSsPages*och upprepa sedan steg 9 och 10 för att ta bort kolumnerna och expandera *innehållet*.
1. Ändra datatypen för *Content.SentimentScore* till **Decimaltal**.
1. Välj *hotelReviewsSSSKeyPhrases* och upprepa steg 9 och 10 för att ta bort kolumnerna och expandera *innehållet*. Det finns inga ändringar av datatyp för den här tabellen.

1. Klicka på Stäng **och Använd**i kommandofältet.

1. Klicka på panelen Modell i det vänstra navigeringsfönstret och kontrollera att Power BI visar relationer mellan alla tre tabellerna.

   ![Validera relationer](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Validera relationer")

1. Dubbelklicka på varje relation och se till att **korsfilterriktningen** är inställd på **Båda**.  Detta gör att dina visuella objekt kan uppdateras när ett filter används.

1. Klicka på rapportpanelen i det vänstra navigeringsfönstret för att utforska data via visualiseringar. För textfält är tabeller och kort användbara visualiseringar. Du kan välja fält från var och en av de tre tabellerna för att fylla i tabellen eller kortet. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Rensa

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforskar det här kunskapsarkivet med Lagringsutforskaren finns i följande artikel.

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)