---
title: Ansluta till kunskaps lager med Power BI-Azure Search
description: Skapa ett kunskaps lager med hjälp av guiden Importera data i Azure Portal och Anslut sedan med Power BI för analys och utforskning.
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: dd1e42b590127362441c99a19e612ddf42bdfa63
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641176"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Skapa en Azure Search kunskaps lager och Anslut med Power BI

> [!Note]
> Kunskaps lagret är i för hands version och bör inte användas i produktionen. [Azure Search REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>

Kunskap Store är en funktion i Azure Search som sparar utdata från en AI-pipeline för efterföljande analyser eller annan efterföljande bearbetning. En AI-fördefinierad pipeline tar emot bildfiler eller ostrukturerade textfiler från en data källa som stöds, skickar den till Azure Search indexering, tillämpar AI-anrikninger från Cognitive Services (till exempel bild analys och naturlig språk bearbetning) och sparar sedan resultatet till ett kunskaps lager i Azure Storage. Från kunskaps lagret kan du lägga till verktyg som Power BI eller Storage Explorer för att utforska resultaten.

I den här artikeln skapar du ett kunskaps lager i portalen och ansluter och utforskar med Power Query i Power BI Desktop. 

I den här genom gången används en data uppsättning som består av kund granskningar och omdömen, sentiment-poängsättning från Cognitive Services och sedan Power Query för att fråga dina dokument. Data härstammar från hotell gransknings data på Kaggle.com. 

## <a name="prerequisites"></a>Förutsättningar

+ [Ladda ned hotell granskningar CSV-filen (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Den här data uppsättningen innehåller poster med kundfeedback om hotell.

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure Search](search-create-service-portal.md). Du kan använda en kostnads fri tjänst för den här genom gången. 

+ [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Se till att kontot är "allmänt syfte", antingen *StorageV2 (generell användning v2)* som är standard, eller *lagring (generell användning v1)* . Välj samma region som Azure Search.
 
## <a name="prepare-data"></a>Förbereda data 

Läs in CSV-filen i Azure Blob Storage så att den kan nås av en Azure Search-indexerare.

1. [Logga](https://portal.azure.com)in på Azure Portal, navigera till ditt Azure Storage-konto, klicka på **blobbar**och klicka sedan på **+ container**.

1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) som innehåller exempel data: 

   1. Namnge behållaren `hotel-reviews`. 
   
   1. Ange den offentliga åtkomst nivån till något av dess giltiga värden. Vi använde standardvärdet.

1. När behållaren har skapats öppnar du den och väljer **Ladda upp** i kommando fältet.

1. Navigera till mappen som innehåller **HotelReviews-Free. csv**, markera filen och klicka på **överför**.

   ![Ladda upp CSV-filen](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "Ladda upp CSV-filen")

1. När du är i Azure Storage hämtar du anslutnings strängen och behållar namnet.  Du kommer att behöva båda dessa strängar när du skapar ett data käll objekt:

   1. På sidan Översikt klickar du på **åtkomst nycklar** och kopierar en *anslutnings sträng*. Den börjar med `DefaultEndpointsProtocol=https;` och avslutas med. `EndpointSuffix=core.windows.net` Ditt konto namn och din nyckel är mellan. 

   1. Behållar namnet måste `hotel-reviews` vara eller det namn som du har tilldelat. 

## <a name="create-and-run-ai-enrichments"></a>Skapa och kör AI-berikningar

Skapa kunskaps lagret med hjälp av guiden Importera data. Du kommer att importera data uppsättningen, välja berikning, konfigurera ett kunskaps lager och ett index och sedan köra.

1. [Hitta Sök tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) på Azure Portal.

1. Klicka på **Importera data** i kommando fältet.

1. Skapa objektet datakällobjektet på den första sidan i guiden.

   - Välj **Azure-Blob Storage**.

   - Ge data källan ett namn, t. ex. *Hotell-recensioner-DS*.

   - Eftersom data är. csv väljer du avgränsad **text** för tolknings läge, markerar den **första raden innehåller rubrik**och kontrollerar att avgränsnings tecken är ett kommatecken.

   - Anslutnings strängen till ditt Azure Storage konto kan hämtas i portalen under **åtkomst nycklar**.

   - Behållarens namn kan också hämtas från portalen i Azure Storage blobs-listan.

      ![Skapa ett data käll objekt](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "Skapa ett data käll objekt")

1. Lägg till anrikninger och konfigurera ett kunskaps lager på andra sidan i guiden.

   - I **bifoga Cognitive Services**kan du använda den kostnads fria resurs som tillåter upp till 20 transaktioner per dag. Antalet poster i HotelReviews-Free. csv är mindre än 20.

   - I **Lägg till anrikninger**namnger du färdigheter *hotell-Reviews-SS*, väljer fältet *reviews_text* , väljer en kornig het på *sidor (5000 tecken)* och väljer sedan dessa tre kognitiva kunskaper: *Extrahera nyckel fraser*, *identifiera språk*, *identifiera sentiment*.

      ![Skapa en färdigheter](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "Skapa en färdigheter")

   - I **Spara kunskaper i kunskaps lagret**anger du anslutnings strängen för ditt allmänna Azure Storage-konto. Ett kunskaps lager skapas i Azure Table Storage när du väljer alternativen för *Azure Table Projects* i det här avsnittet.

      ![Konfigurera kunskaps lager](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "Konfigurera kunskaps lager")
   
   Klicka på **Nästa: Anpassa mål index** för att fortsätta till nästa steg.

1. Konfigurera ett index för valfria full texts öknings frågor i Azure Search. Även om fokus för den här genom gången är Power BI ansluten till Azure Table Storage, kan guiden **Importera data** också skapa ett index som används för full texts ökning i Azure Search. 

   I guiden kan data källan härleda fält och data typer, så allt du behöver göra är att välja de attribut som behövs för att uppnå beteendet. *Hämtning* innebär till exempel att fält innehållet kan hämtas från tjänsten, medan sökbart aktiverar fullständig texts ökning på valda fält.

   - Ge indexet ett namn, t. ex. *Hotell-recensioner-IDX*.
   - Ange alla fält som **hämtnings**bara.
   - Ange *stad*, *namn*, *reviews_text*, *språk*och Sök *uttryck* som sökbara.
   - Ange *sentiment*, *språk*och som filtrerings bara och **aspekt**bara. 
   
    Ditt index bör se ut ungefär som på följande bild.

     ![Konfigurera ett index](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "Konfigurera ett index")

   Klicka på **Nästa: Skapa en indexerare** för att fortsätta till nästa steg.

1. Konfigurera en indexerare genom att ge den ett namn och en körnings takt. I den här genom gången använder du *hotell-Review-idxr* som index namn och använder standardschemat **Once** för att köra indexeraren direkt.

   Indexerings körningen placerar alla tidigare konfigurationer i rörelse. Extrahering, bearbetning och inmatning av allt sker här i det här steget.

1. Övervaka indexering i meddelande kön i portalen. Det tar flera minuter att slutföra körningen.

## <a name="connect-with-power-bi"></a>Anslut med Power BI

1. Starta Power BI Desktop och välj **Hämta data**.

1. I Hämta data väljer du **Azure** och sedan **Azure Table Storage**. Klicka på **Anslut**.

1. I konto namn eller URL, klistra in ditt Azure Storage konto namn (den fullständiga URL: en matchas åt dig).

1. Ange konto nyckeln.

1. Välj dokument, diskussions fraser och sidor. Det här är tabellerna som skapas av guiden Importera data när du väljer samma objekt i kunskaps arkivets konfiguration. Klicka på **Läs in**.

1. Öppna Power Query genom att klicka på kommandot **Redigera frågor** .

   ![Öppna Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Öppna Power Query")

1. För dokument:

   - Ta bort kolumnerna PartitionKey, RowKey och timestamp som skapats av Azure Table Storage. Kunskaps lager ger relationer som används i den här analysen.

   - Expandera kolumnen innehåll.

     ![Redigera tabeller](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "Redigera tabeller")

1. Välj alla fält och avmarkera sedan de som börjar med "metadata".

1. Korrigera data typen för följande kolumner med hjälp av ABC-123-ikonen på varje kolumn:

   - Datum kolumner ska vara **datetime**
   - *Latitud* ska vara ett **decimal tal**
   - *Longitud* ska vara ett **decimal tal**

1. Upprepa föregående steg för att ta bort PartitionKey och andra kolumner, expandera innehålls kolumner, ange *SentimentScore* till **decimal tal**.

1. Upprepa igen för sidor, ta bort PartitionKey och andra kolumner och expandera innehålls kolumner. Det finns inga ändringar av data typen för den här tabellen.

1. Klicka på **Stäng och Använd** längst till vänster i Power Query kommando fältet.

1. Verifiera att Power BI känner igen relationerna som kunskaps arkivet skapade i dina data. Klicka på panelen relationer i det vänstra navigerings fönstret. Alla tre tabeller bör vara relaterade.

   ![Verifiera relationer](media/knowledge-store-howto-powerbi/powerbi-relationships.png "Verifiera relationer")

## <a name="try-with-larger-data-sets"></a>Försök med större data uppsättningar

Vi är i själva syfte att hålla data uppsättningen små för att undvika avgifter för en demonstrations genom gång. För en mer verklighetstrogen upplevelse kan du skapa och sedan koppla en fakturerbar Cognitive Services-resurs för att möjliggöra ett större antal transaktioner mot sentiment Analyzer, extrahering av extrahering av diskussions fraser och språk detektor kunskaper.

Skapa nya behållare i Azure Blob Storage och överför varje CSV-fil till en egen behållare. Ange någon av dessa behållare i steget Skapa data källa i guiden Importera data.

| Beskrivning | Länka |
|-------------|------|
| Kostnadsfri nivå   | [HotelReviews_Free. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Liten (500 poster) | [HotelReviews_Small. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medel (6000 poster)| [HotelReviews_Medium. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Stora (fullständiga data mängder 35000 poster) | [HotelReviews_Large. csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)|

I det här steget i guiden lägger du till en fakturerbar [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resurs som skapats på *S0* -nivån i samma region som Azure Search för att använda större data mängder. 

  ![Skapa en Cognitive Services resurs](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Skapa en Cognitive Services resurs")

## <a name="next-steps"></a>Nästa steg

Om du vill upprepa den här övningen eller göra en annan AI-förhands granskning tar du bort den *hotell-och--och-IDX-* index som du nyss skapade. Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren tillbaka till noll. 

Mer detaljerade instruktioner för att demonstrera kunskaps lager finns i nästa artikel som visar hur du skapar ett kunskaps lager med hjälp av REST API: er och Postman.

> [!div class="nextstepaction"]
> [Kom igång med kunskaps lager](knowledge-store-howto.md)