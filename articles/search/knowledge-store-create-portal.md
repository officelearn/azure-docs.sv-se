---
title: Skapa ett kunskapsarkiv (förhandsversion) i Azure-portalen
titleSuffix: Azure Cognitive Search
description: Använd guiden Importera data för att skapa ett kunskapsarkiv som används för att bevara utökat innehåll. Anslut till ett kunskapslager för analys från andra appar eller skicka nytt använt innehåll till underordnade processer. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472374"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>Snabbstart: Skapa ett Kunskapsarkiv för Azure Cognitive Search i Azure-portalen

> [!IMPORTANT] 
> Knowledge Store är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Knowledge Store är en funktion i Azure Cognitive Search som beständiga utdata från en pipeline för kognitiva färdigheter för efterföljande analyser eller nedströmsbearbetning. 

En pipeline accepterar ostrukturerad text och bilder som rått innehåll, tillämpar AI via Cognitive Services (till exempel OCR, bildanalys och bearbetning av naturligt språk), extraherar information och matar ut nya strukturer och information. En av de fysiska artefakter som skapats av en pipeline är ett [kunskapslager](knowledge-store-concept-intro.md), som du kan komma åt genom verktyg för att analysera och utforska innehåll.

I den här snabbstarten kombinerar du tjänster och data i Azure-molnet för att skapa ett kunskapslager. När allt är på plats kör du guiden **Importera data** i portalen för att samla allt. Slutresultatet är original textinnehåll plus AI-genererat innehåll som du kan visa i portalen ([Storage explorer](knowledge-store-view-storage-explorer.md)).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-services-and-load-data"></a>Skapa tjänster och läsa in data

Den här snabbstarten använder Azure Cognitive Search, Azure Blob Storage och [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) för AI. 

Eftersom arbetsbelastningen är så liten utnyttjas Cognitive Services bakom kulisserna för att tillhandahålla kostnadsfri bearbetning för upp till 20 transaktioner dagligen. Eftersom datauppsättningen är så liten kan du hoppa över att skapa eller koppla en Cognitive Services-resurs.

1. [Ladda ner HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dessa data är hotell granskningsdata sparas i en CSV-fil (kommer från Kaggle.com) och innehåller 19 bitar av kundfeedback om ett enda hotell. 

1. [Skapa ett Azure-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) eller [hitta ett befintligt konto](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) under din aktuella prenumeration. Du använder Azure-lagring för både råinnehåll som ska importeras och kunskapsarkivet som är slutresultatet.

   Välj kontotypen **StorageV2 (allmänt V2).**

1. Öppna sidorna Blob-tjänster och skapa en behållare med namnet *hotel-reviews*.

1. Klicka på **Överför**.

    ![Ladda upp data](media/knowledge-store-create-portal/upload-command-bar.png "Ladda upp hotellrecensionerna")

1. Välj **den HotelReviews-Free.csv-fil** som du laddade ner i det första steget.

    ![Skapa Azure Blob-behållaren](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Skapa Azure Blob-behållaren")

1. Du är nästan klar med den här resursen, men innan du lämnar dessa sidor använder du en länk i det vänstra navigeringsfönstret för att öppna sidan **Snabbtangenter.** Hämta en anslutningssträng för att hämta data från Blob-lagring. En anslutningssträng liknar följande exempel:`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Växla till Azure Cognitive Search fortfarande i portalen. [Skapa en ny tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnadsfri tjänst för den här snabbstarten.

Du är nu redo att gå vidare med guiden Importera data.

## <a name="run-the-import-data-wizard"></a>Köra guiden Importera data

På sidan Översikt över söktjänsten klickar du på **Importera data** i kommandofältet för att skapa ett kunskapslager i fyra steg.

  ![Kommandot Importera data](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>Steg 1: Skapa en datakälla

1. I **Anslut till dina data** väljer du **Azure Blob Storage** och väljer kontot och containern som du skapade. 
1. För **namnet**anger `hotel-reviews-ds`du .
1. För **tolkningsläge**väljer du **Avgränsad text**och markerar sedan kryssrutan **Första raden Innehåller sidhuvud.** Kontrollera att **avgränsare** är ett kommatecken (,).
1. Klistra in i anslutningssträngen som du kopierade från sidan **Access Nycklar** i Azure Storage i Anslutningssträngen. **Connection String**
1. I **Behållare**anger du namnet på blob-behållaren som innehåller data.

    Sidan ska se ut ungefär som följande skärmbild.

    ![Skapa ett datakällobjekt](media/knowledge-store-create-portal/hotel-reviews-ds.png "Skapa ett datakällobjekt")

1. Fortsätt till nästa sida.

### <a name="step-2-add-cognitive-skills"></a>Steg 2: Lägga till kognitiva kunskaper

I det här guidesteget skapar du en kompetens med kognitiva skicklighetsberikande. Källdata består av kundrecensioner på flera språk. Kunskaper som är relevanta för den här datauppsättningen inkluderar extrahering av nyckelfraser, sentimentidentifiering och textöversättning. I ett senare steg kommer dessa enrichments att "projiceras" i ett kunskapslager som Azure-tabeller.

1. Expandera **Bifoga kognitiva tjänster**. **Gratis (Begränsade anrikningar)** väljs som standard. Du kan använda denna resurs eftersom antalet poster i HotelReviews-Free.csv är 19 och denna gratis resurs tillåter upp till 20 transaktioner per dag.
1. Expandera **Lägg till anrikningar**.
1. För **Skillset-namn**anger du `hotel-reviews-ss`.
1. För **källdatafält**väljer du **reviews_text**.
1. För **detaljrikningsnivå för anrikning**väljer du **Sidor (5 000 teckensegment)**
1. Välj dessa kognitiva färdigheter:
    + **Extrahera nyckelfraser**
    + **Översätta text**
    + **Identifiera sentiment**

      ![Skapa en kunskapsuppsättning](media/knowledge-store-create-portal/hotel-reviews-ss.png "Skapa en kunskapsuppsättning")

1. Expandera **Spara enrichments till kunskapsbutik**.
1. Välj dessa **Azure-tabellprojektioner:**
    + **Dokument**
    + **Sidor**
    + **Nyckelfraser**
1. Ange **anslutningssträngen för lagringskonto** som du sparade i ett tidigare steg.

    ![Konfigurera kunskapsarkivet](media/knowledge-store-create-portal/hotel-reviews-ks.png "Konfigurera kunskapsarkivet")

1. Du kan också hämta en Power BI-mall. När du öppnar mallen från guiden anpassas den lokala PBIT-filen så att den återspeglar formen på dina data.

1. Fortsätt till nästa sida.

### <a name="step-3-configure-the-index"></a>Steg 3: Konfigurera indexet

I det här guidesteget konfigurerar du ett index för valfria fulltextsökningsfrågor. Datakällan kommer att ta prov på datakällan för att härleda fält och datatyper. Du behöver bara välja attribut för önskat beteende. Attributet **Hämtningsbar** gör det till exempel möjligt för söktjänsten att returnera ett fältvärde medan **sökbara** aktiverar fulltextsökning i fältet.

1. För **Indexnamn** `hotel-reviews-idx`anger du .
1. För attribut godkänner du standardvalen: **Hämtningsbara** och **sökbara** för de nya fält som pipelinen skapar.

    Indexet ska se ut ungefär som följande bild. Eftersom listan är lång visas inte alla fält i bilden.

    ![Konfigurera ett index](media/knowledge-store-create-portal/hotel-reviews-idx.png "Konfigurera ett index")

1. Fortsätt till nästa sida.

### <a name="step-4-configure-the-indexer"></a>Steg 4: Konfigurera indexeraren

I det här guidesteget konfigurerar du en indexerare som samlar datakällan, kompetensen och indexet som du definierade i föregående guidesteg.

1. För **Namn** `hotel-reviews-idxr`anger du .
1. För **Schema**behåller du **standardinställningen En gång**.
1. Klicka på **Skicka** om du vill köra indexeraren. Datautvinning, indexering, tillämpning av kognitiva färdigheter sker alla i detta steg.

## <a name="monitor-status"></a>Övervaka status

Kognitiv färdighetsindexering tar längre tid att slutföra än typisk textbaserad indexering. Guiden ska öppna listan med indexerare på översiktssidan så att du kan följa förloppet. För självnavigering går du till översikten över sidan och klickar på **indexerare**.

I Azure-portalen kan du också övervaka aktivitetsloggen meddelanden för en klickbar **Azure Cognitive Search-meddelandestatuslänk.** Körningen kan ta flera minuter att slutföra.

## <a name="next-steps"></a>Nästa steg

Nu när du har berikat dina data med Cognitive Services och projicerat resultaten i ett kunskapslager kan du använda Storage Explorer eller Power BI för att utforska din utökade datauppsättning.

Du kan visa innehåll i Storage Explorer eller ta det ett steg längre med Power BI för att få insikter genom visualisering.

> [!div class="nextstepaction"]
> [Visa med Storage Explorer](knowledge-store-view-storage-explorer.md)
> [Connect med Power BI](knowledge-store-connect-power-bi.md)

> [!Tip]
> Om du vill upprepa den här övningen eller prova en annan GENOMGÅNG AV AI-anrikning tar du bort *indexeraren för hotel-reviews-idxr.* Om du tar bort indexeraren återställs den kostnadsfria dagliga transaktionsräknaren tillbaka till noll för bearbetning av Cognitive Services.
