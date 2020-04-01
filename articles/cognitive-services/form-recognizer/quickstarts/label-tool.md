---
title: 'Snabbstart: Etikettformulär, träna en modell och analysera ett formulär med hjälp av exempeletikettverktyget - Formuläre recognizer'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda exempeletikettverktyget Formulär recognizer för att manuellt märka formulärdokument. Sedan ska du träna en anpassad modell med de märkta dokumenten och använda modellen för att extrahera nyckel/värdepar.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 0cfe58ab0d161019d5f53d9135c65db7beff2bb4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397999"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Träna en formulärmedkänningsmodell med etiketter med hjälp av exempeletikettverktyget

I den här snabbstarten ska du använda REST-API:et för formulärmed exempeletikettverktyget för att träna en anpassad modell med manuellt märkta data. Se avsnittet [Träna med etiketter](../overview.md#train-with-labels) i översikten om du vill veta mer om den här funktionen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten måste du ha:

- En uppsättning av minst sex former av samma typ. Du ska använda dessa data för att träna modellen och testa ett formulär. Du kan använda en [exempeldatauppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) för den här snabbstarten. Ladda upp utbildningsfilerna till roten för en blob-lagringsbehållare i ett Azure Storage-konto.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulärkonformeringsresurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-the-sample-labeling-tool"></a>Ställ in exempeletikettverktyget

Du ska använda Docker-motorn för att köra exempeletikettverktyget. Följ dessa steg för att ställa in Docker-behållaren. En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> OCR-formulärmärkningsverktyget är också tillgängligt som ett projekt med öppen källkod på GitHub. Verktyget är ett webbprogram byggt med React + Redux och skrivs i TypeScript. Mer information om eller bidra finns i [OCR-formuläretikettverktyg](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application).

1. Installera först Docker på en värddator. Den här guiden visar hur du använder lokal dator som värd. Om du vill använda en Docker-värdtjänst i Azure läser du hur du kan styra hur du vill använda [exempeletikettverktyget.](../deploy-label-tool.md) 

   Värddatorn måste uppfylla följande maskinvarukrav:

    | Container | Minimum | Rekommenderas|
    |:--|:--|:--|
    |Exempel på etikettverktyg|2 kärnor, 4 GB minne|4-kärniga, 8 GB minne|

   Installera Docker på datorn genom att följa lämpliga instruktioner för operativsystemet: 
   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [Macos](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Hämta exempelmärkningsverktygsbehållaren `docker pull` med kommandot.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Nu är du redo att `docker run`köra behållaren med .
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Det här kommandot gör exempeletikettverktyget tillgängligt via en webbläsare. Gå [http://localhost:3000](http://localhost:3000)till .

> [!NOTE]
> Du kan också märka dokument och träna modeller med hjälp av FORM Recognizer REST API. Om du vill träna och analysera med REST API finns i [Träna med etiketter med REST API och Python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Ställ in indata

Kontrollera först att alla utbildningsdokument är av samma format. Om du har formulär i flera format ordnar du dem i undermappar baserat på gemensamt format. När du tränar måste du dirigera API:et till en undermapp.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurera resursdelning mellan domäner (CORS)

Aktivera CORS på ditt lagringskonto. Välj ditt lagringskonto i Azure-portalen och klicka på fliken **CORS** i den vänstra rutan. Fyll i följande värden på den nedersta raden. Klicka sedan på **Spara** högst upp.

* Tillåtet ursprung = * 
* Tillåtna \[metoder = markera alla\]
* Tillåtna rubriker = *
* Exponerade rubriker = * 
* Max ålder = 200

> [!div class="mx-imgBorder"]
> ![CORS-inställningar i Azure-portalen](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Anslut till exempeletikettverktyget

Exempeletikettverktyget ansluter till en källa (där de ursprungliga formulären finns) och ett mål (där det exporterar de skapade etiketterna och utdata).

Anslutningar kan ställas in och delas mellan projekt. De använder en utökningsbar leverantörsmodell, så att du enkelt kan lägga till nya käll-/målleverantörer.

Om du vill skapa en ny anslutning klickar du på ikonen Nya anslutningar (plug) i det vänstra **navigeringsfältet.**

Fyll i fälten med följande värden:

* **Visningsnamn** - Anslutningsvisningsnamnet.
* **Beskrivning** - Din projektbeskrivning.
* **SAS-URL** – SAS-URL:en (Shared Access Signature) för din Azure Blob Storage-behållare. Om du vill hämta SAS-URL:en öppnar du Microsoft Azure Storage Explorer, högerklickar på behållaren och väljer **Hämta signatur för delad åtkomst**. Ställ in utgångstiden till en tid efter att du har använt tjänsten. Kontrollera att behörigheterna **Läs,** **Skriv,** **Ta bort**och **Lista** är markerade och klicka på **Skapa**. Kopiera sedan värdet i **avsnittet URL.** Det bör ha `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formen: .

![Anslutningsinställningar för exempeletikettverktyget](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

I exempeletikettverktyget lagrar projekt dina konfigurationer och inställningar. Skapa ett nytt projekt och fyll i fälten med följande värden:

* **Visningsnamn** - projektets visningsnamn
* **Säkerhetstoken** - Vissa projektinställningar kan innehålla känsliga värden, till exempel API-nycklar eller andra delade hemligheter. Varje projekt genererar en säkerhetstoken som kan användas för att kryptera/dekryptera känsliga projektinställningar. Du kan hitta säkerhetstoken i programinställningarna genom att klicka på kugghjulsikonen i det nedre hörnet av det vänstra navigeringsfältet.
* **Källa anslutning** - Azure Blob Storage-anslutning som du skapade i föregående steg som du vill använda för det här projektet.
* **Mappsökväg** - Valfritt - Om källformulären finns i en mapp på blob-behållaren anger du mappnamnet här
* **Formulär recognizer Service Uri** - Din formulär recognizer slutpunkt URL.
* **API-nyckel** - Din prenumerationsnyckel för formulär recognizer.
* **Beskrivning** - Valfritt - Projektbeskrivning

![Ny projektsida på exempeletikettverktyget](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Märk formulären

När du skapar eller öppnar ett projekt öppnas huvudtaggredigerarens fönster. Taggredigeraren består av tre delar:

* Ett förhandsgranskningsfönster som kan ändras som innehåller en rullningsbar lista med formulär från källanslutningen.
* Huvudredigerarfönstret där du kan använda taggar.
* Fönstret taggredigerare som tillåter användare att ändra, låsa, ändra ordning på och ta bort taggar. 

### <a name="identify-text-elements"></a>Identifiera textelement

Klicka **på Kör OCR på alla filer** i den vänstra rutan för att hämta textlayoutinformationen för varje dokument. Etikettverktyget ritar markeringsrutor runt varje textelement.

### <a name="apply-labels-to-text"></a>Använda etiketter på text

Därefter ska du skapa taggar (etiketter) och använda dem på de textelement som du vill att modellen ska känna igen.

1. Använd först hjälp av redigeringsfönstret för taggar för att skapa de taggar som du vill identifiera.
  1. Klicka **+** här om du vill skapa en ny tagg.
  1. Ange taggnamnet.
  1. Spara taggen genom att trycka på Retur.
1. Markera ett eller flera ord från de markerade textelementen i huvudredigeraren.
1. Klicka på taggen du vill använda eller tryck på motsvarande tangentbordstangent. Siffernycklarna tilldelas som snabbtangenter för de första 10 taggarna. Du kan ändra ordning på taggarna med hjälp av upp- och nedpilsikonerna i taggredigeringsfönstret.
    > [!Tip]
    > Tänk på följande när du märker formulären.
    > * Du kan bara använda en tagg på varje markerat textelement.
    > * Varje tagg kan bara användas en gång per sida. Om ett värde visas flera gånger i samma formulär skapar du olika taggar för varje instans. Till exempel: "faktura# 1", "faktura# 2" och så vidare.
    > * Taggar kan inte sträcka sig över sidor.
    > * Etikettvärden som de visas i formuläret. försök inte att dela upp ett värde i två delar med två olika taggar. Ett adressfält ska till exempel märkas med en enda tagg även om det sträcker sig över flera rader.
    > * Ta inte med nycklar i&mdash;dina taggade fält bara värdena.
    > * Tabelldata bör identifieras automatiskt och kommer att vara tillgängliga i den slutliga utdata JSON-filen. Men om modellen inte kan identifiera alla tabelldata kan du även tagga dessa fält manuellt. Tagga varje cell i tabellen med en annan etikett. Om dina formulär har tabeller med varierande antal rader kontrollerar du att du taggar minst ett formulär med största möjliga tabell.


Följ stegen ovan för att märka fem av formulären och gå sedan vidare till nästa steg.

![Huvudredigerarfönstret i exempeletikettverktyget](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Träna en anpassad modell

Klicka på tågikonen (tågbilen) i den vänstra rutan för att öppna sidan Utbildning. Klicka sedan på **knappen Träna** för att börja träna modellen. När utbildningsprocessen är klar visas följande information:

* **Modell-ID** - ID för den modell som skapades och tränades. Varje träningsanrop skapar en ny modell med eget ID. Kopiera strängen till en säker plats. Du behöver det om du vill göra förutsägelse samtal via REST API.
* **Genomsnittlig noggrannhet** - Modellens genomsnittliga noggrannhet. Du kan förbättra modellens noggrannhet genom att märka ytterligare formulär och utbildning igen för att skapa en ny modell. Vi rekommenderar att du börjar med att märka fem formulär och lägga till fler formulär efter behov.
* Listan över taggar och den uppskattade noggrannheten per tagg.

![träningsvy](../media/label-tool/train-screen.png)

När utbildningen är klar undersöker du värdet **Genomsnittlig noggrannhet.** Om det är lågt bör du lägga till fler inmatningsdokument och upprepa stegen ovan. De dokument som du redan har märkt finns kvar i projektindexet.

> [!TIP]
> Du kan också köra träningsprocessen med ett REST API-anrop. Mer information om hur du gör detta finns i [Träna med etiketter med Python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Analysera ett formulär

Klicka på ikonen Förutsäga (rektanglar) till vänster för att testa din modell. Ladda upp ett formulärdokument som du inte har använt i utbildningsprocessen. Klicka sedan på knappen **Förutsäga** till höger för att få nyckel-/värdeprognoser för formuläret. Verktyget kommer att använda taggar i markeringsrutor och kommer att rapportera förtroendet för varje tagg.

> [!TIP]
> Du kan också köra Analys API med ett REST-anrop. Mer information om hur du gör detta finns i [Träna med etiketter med Python](./python-labeled-data.md).

## <a name="improve-results"></a>Förbättra resultaten

Beroende på den rapporterade noggrannheten, kanske du vill göra vidareutbildning för att förbättra modellen. När du har gjort en förutsägelse undersöker du konfidensvärdena för var och en av de använda taggarna. Om det genomsnittliga noggrannhetsutbildningsvärdet var högt, men konfidenspoängen är låga (eller om resultaten är felaktiga), bör du lägga till filen som används för förutsägelse i träningsuppsättningen, märka den och träna igen.

Den rapporterade genomsnittliga noggrannheten, konfidenspoängen och den faktiska noggrannheten kan vara inkonsekventa när de analyserade dokumenten skiljer sig från dem som används i utbildningen. Tänk på att vissa dokument ser likadana ut när de visas av personer men kan se distinkt ut för AI-modellen. Du kan till exempel träna med en formulärtyp som har två varianter, där träningsuppsättningen består av 20 % variation A och 80 % variation B. Under förutsägelse, förtroende poäng för dokument av variation A kommer sannolikt att vara lägre.

## <a name="save-a-project-and-resume-later"></a>Spara ett projekt och återuppta senare

Om du vill återuppta projektet vid ett annat tillfälle eller i en annan webbläsare måste du spara projektets säkerhetstoken och skriva in det igen senare. 

### <a name="get-project-credentials"></a>Hämta projektautentiseringsuppgifter
Gå till sidan projektinställningar (skjutreglageikonen) och notera namnet på säkerhetstoken. Gå sedan till dina programinställningar (kugghjulsikonen), som visar alla säkerhetstoken i din aktuella webbläsarinstans. Hitta projektets säkerhetstoken och kopiera dess namn och nyckelvärde till en säker plats.

### <a name="restore-project-credentials"></a>Återställa autentiseringsuppgifter för projekt
När du vill återuppta projektet måste du först skapa en anslutning till samma bloblagringsbehållare. Upprepa stegen ovan för att göra detta. Gå sedan till sidan programinställningar (kugghjulsikonen) och se om projektets säkerhetstoken finns där. Om den inte är det lägger du till en ny säkerhetstoken och kopierar över tokennamnet och nyckeln från föregående steg. Klicka sedan på Spara inställningar. 

### <a name="resume-a-project"></a>Återuppta ett projekt

Gå slutligen till huvudsidan (husikonen) och klicka på Öppna molnprojekt. Välj sedan blob-lagringsanslutningen och välj projektets *.vott-fil.* Programmet läser in alla projektets inställningar eftersom det har säkerhetstoken.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du använder exempeletikettverktyget Formulärmedkännare för att träna en modell med manuellt märkta data. Om du vill integrera märkningsverktyget i ditt eget program använder du REST-API:erna som hanterar märkt datautbildning.

> [!div class="nextstepaction"]
> [Träna med etiketter med Python](./python-labeled-data.md)
