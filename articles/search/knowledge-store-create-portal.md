---
title: Skapa ett kunskaps lager i Azure Portal-Azure Search
description: Skapa en Azure Search kunskaps lager för att spara omfattande kunskaper från kognitiv Sök pipelinen med hjälp av guiden Importera data i Azure Portal.
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: fb979a7ff4144694aecad0985c5bce9be2de05bd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265200"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>Skapa en Azure Search kunskaps lager i Azure Portal

> [!Note]
> Kunskaps lagret är i för hands version och bör inte användas i produktionen. [Azure Search REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>

Kunskap Store är en funktion i Azure Search som sparar utdata från en AI-pipeline för senare analys eller annan efterföljande bearbetning. En AI-fördefinierad pipeline tar emot bildfiler eller ostrukturerade textfiler, indexerar dem med hjälp av Azure Search, tillämpar AI-anrikninger från Cognitive Services (till exempel bild analys och naturlig språk bearbetning) och sparar sedan resultaten i ett kunskaps lager i Azure lagrings. Du kan sedan använda verktyg som Power BI eller Storage Explorer för att utforska kunskaps lagret.

I den här artikeln ska du använda guiden Importera data på Azure Portal för att mata in, indexera och tillämpa AI-berikare i en uppsättning Hotell recensioner. Hotell granskningarna importeras till Azure blogg lagring och resultatet sparas som ett kunskaps lager i Azure Table Storage.

När du har skapat kunskaps lagret kan du lära dig hur du får åtkomst till det här kunskaps lagret med hjälp av Storage Explorer eller Power BI.

## <a name="prerequisites"></a>Förutsättningar

+ [Skapa en Azure Search tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för den här självstudien.

+ [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) för att lagra exempel data och kunskaps lagret. Ditt lagrings konto måste använda samma plats (till exempel US-WEas Your Azure Search-tjänsten och *konto typen* måste vara *StorageV2 (generell användning v2)* (standard) eller *Storage (generell användning v1)* .

## <a name="load-the-data"></a>Läs in data

Läs in CSV-filen för hotell granskningar i Azure Blob Storage så att den kan nås av en Azure Search-indexerare och matas genom AI-anrikningen.

### <a name="create-an-azure-blob-container-with-the-data"></a>Skapa en Azure Blob-behållare med data

1. [Ladda ned hotell gransknings data som sparats i en CSV-fil (HotelReviews_Free. csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Dessa data härstammar från Kaggle.com och innehåller kundfeedback om hotell.
1. [Logga](https://portal.azure.com)in på Azure Portal och navigera till ditt Azure Storage-konto.
1. [Skapa en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) Det gör du genom att klicka på **blobbar**i det vänstra navigerings fältet för ditt lagrings konto och sedan klicka på **+ container** i kommando fältet.
1. För det nya behållar **namnet**anger `hotel-reviews`du.
1. Välj valfri **offentlig åtkomst nivå**. Vi använde standardvärdet.
1. Skapa Azure Blob-behållaren genom att klicka på **OK** .
1. Öppna den nya `hotels-review` behållaren, klicka på **Ladda upp**och välj den **HotelReviews-Free. csv** -fil som du laddade ned i det första steget.

    ![Överför data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp Hotell recensioner")

1. Klicka på **överför** för att importera CSV-filen till Azure Blob Storage. Den nya behållaren kommer att visas.

    ![Skapa Azure Blob-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa Azure Blob-behållaren")

### <a name="get-the-azure-storage-account-connection-string"></a>Hämta Azure Storage kontots anslutnings sträng

1. Navigera till ditt Azure Storage-konto på portalen.
1. I det vänstra navigerings fältet för tjänsten klickar du på **åtkomst nycklar**.
1. Under **nyckel 1**kopierar du och sparar *anslutnings strängen*. Strängen börjar med `DefaultEndpointsProtocol=https`. Ditt lagrings konto namn och nyckel är inbäddade i strängen. Håll den här strängen praktisk. Du kommer att behöva det i framtida steg.

## <a name="create-and-run-ai-enrichments"></a>Skapa och kör AI-berikningar

Skapa kunskaps lagret med hjälp av guiden Importera data. Du kommer att skapa en data källa, välja berikning, konfigurera ett kunskaps lager och ett index och sedan köra.

### <a name="start-the-import-data-wizard"></a>Starta guiden Importera data

1. [Leta upp Sök tjänsten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)på Azure Portal.

1. Klicka på **Importera data** i kommando fältet för att starta import guiden.

### <a name="connect-to-your-data-import-data-wizard"></a>Anslut till dina data (guiden Importera data)

I den här guiden ska du skapa en data Källa från Azure-blobben med dina hotell data.

1. I listan **data källa** väljer du **Azure Blob Storage**.
1. Som **namn**anger `hotel-reviews-ds`du.
1. För **tolknings läge**väljer du **avgränsad text**och markerar sedan kryss rutan **första raden innehåller rubrik** . Se till att **avgränsnings tecken** är ett kommatecken (,).
1. Ange din **anslutnings sträng** för lagrings tjänsten som du sparade i föregående steg.
1. För **container namn**anger `hotel-reviews`du.
1. Klicka på **Nästa: Lägg till kognitiv sökning (valfritt**).

      ![Skapa ett data käll objekt](media/knowledge-store-create-portal/hotel-reviews-ds.png "Skapa ett data käll objekt")

## <a name="add-cognitive-search-import-data-wizard"></a>Lägg till kognitiv sökning (guiden Importera data)

I den här guiden ska du skapa en färdigheter med kognitiva färdigheter. De kunskaper vi använder i det här exemplet extraherar viktiga fraser och identifierar språk-och sentiment. Dessa berikningar blir "projiceras" i ett kunskaps lager som Azure-tabeller.

1. Expandera **bifoga Cognitive Services**. **Kostnads fri (begränsade anrikninger)** är valt som standard. Du kan använda den här resursen eftersom antalet poster i HotelReviews-Free. csv är 19 och den här kostnads fria resursen tillåter upp till 20 transaktioner per dag.
1. Expandera **Lägg till anrikninger**.
1. För **färdigheter-namn**anger `hotel-reviews-ss`du.
1. För **data fält för källa**väljer du **reviews_text*.
1. Välj **sidor (5000-segment)** för **detaljerad granularitet nivå**
1. Välj dessa kognitiva kunskaper:
    + **Extrahera nyckelfraser**
    + **Identifiera språk**
    + **Identifiera sentiment**

      ![Skapa en färdigheter](media/knowledge-store-create-portal/hotel-reviews-ss.png "Skapa en färdigheter")

1. Expandera **Spara berikade kunskaper i kunskaps lagret**.
1. Ange **lagrings kontots anslutnings sträng** som du sparade i föregående steg.
1. Välj följande **Azure Table-projektioner**:
    + **Dokument**
    + **Sidor**
    + **Nyckel fraser**

    ![Konfigurera kunskaps lager](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurera kunskaps lager")

1. Klicka på **Nästa: Anpassa mål index**.

### <a name="import-data-import-data-wizard"></a>Importera data (guiden Importera data)

I det här steget konfigurerar du ett index för valfria full texts öknings frågor. Guiden kommer att sampla data källan för att härleda fält och data typer. Du behöver bara välja attributen för det önskade beteendet. Attributet för **hämtning** kan till exempel tillåta Sök tjänsten att returnera ett fält värde medan **sökbart** aktiverar fullständig texts ökning i fältet.

1. För **index namn**anger `hotel-reviews-idx`du.
1. För attribut gör du följande val:
    + Välj **hämtnings** Bart för alla fält.
    + Välj **Filterable** och **fasettable** för dessa fält: *Sentiment*, *språk*, diskussions *fraser*
    + Välj **sökbart** för följande fält: *ort*, *namn*, *reviews_text*, *språk*, diskussions *fraser*

    Ditt index bör se ut ungefär som på följande bild. Eftersom listan är lång visas inte alla fält i bilden.

    ![Konfigurera ett index](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurera ett index")

1. Klicka på **Nästa: Skapa en indexerare**.

### <a name="create-an-indexer"></a>Skapa en indexerare

I det här steget ska du konfigurera en indexerare som hämtar data källan, färdigheter och det index som du definierade i föregående steg i guiden.

1. Som **namn**anger `hotel-reviews-idxr`du.
1. Behåll **standardvärdet**för **schema**.
1. Klicka på **Skicka** för att köra indexeraren. Data extrahering, indexering, program av kognitiva kunskaper alla sker i det här steget.

### <a name="monitor-the-notifications-queue-for-status"></a>Övervaka meddelande kön för status

1. I Azure Portal övervakar du aktivitets loggen för meddelanden som länkar till en klicknings bara **Azure Search meddelande** status. Det kan ta flera minuter att slutföra körningen.

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data med kognitiva tjänster och projicerat resultaten i ett kunskaps lager, kan du använda Storage Explorer eller Power BI för att utforska din omfattande data uppsättning.

Information om hur du utforskar det här kunskaps lagret med hjälp av Storage Explorer finns i följande genom gång.

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)

Information om hur du ansluter det här kunskaps lagret till Power BI finns i följande genom gång.

> [!div class="nextstepaction"]
> [Anslut med Power BI](knowledge-store-connect-power-bi.md)

Om du vill upprepa den här övningen eller testa en annan AI-förhands granskning tar du bort indexet *hotell reidxrs-* . Om du tar bort indexeraren återställs den kostnads fria dagliga transaktions räknaren tillbaka till noll.
