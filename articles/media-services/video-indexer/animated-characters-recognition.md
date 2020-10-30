---
title: Animerat typsnitts identifiering med Video Indexer
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder animerad identifiering av bokstäver med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 1ac27f8fe0c6c608bfc261e9ac2d96651c94b341
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040086"
---
# <a name="animated-character-detection-preview"></a>Identifiering av animerade karaktärer (förhandsversion)

Azure Media Services Video Indexer stöder identifiering, gruppering och igenkänning av tecken i animerat innehåll via integrering med [Cognitive Services anpassad vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Den här funktionen är tillgänglig både via portalen och via API: et.

När du har laddat upp en animerad video med en speciell modell för animering Video Indexer extraherar nyckel rutor, identifierar animerade tecken i dessa ramar, grupper liknande tecken och väljer det bästa exemplet. Sedan skickar den grupperade tecken till Custom Vision som identifierar tecken baserade på de modeller som den tränade på. 

Innan du börjar träna din modell identifieras tecknen namelessly. När du lägger till namn och tränar modellen identifierar Video Indexer tecknen och namnger dem därefter.

## <a name="flow-diagram"></a>Flödesdiagram

Följande diagram visar flödet av den animerade processen för att identifiera animering.

![Flödesdiagram](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Konton

Olika funktions uppsättningar är tillgängliga beroende på vilken typ av Video Indexer konto som används. Information om hur du ansluter ditt konto till Azure finns i [skapa ett video Indexer-konto som är anslutet till Azure](connect-to-azure.md).

* Utvärderings konto: Video Indexer använder ett internt Custom Vision konto för att skapa en modell och ansluta den till ditt Video Indexer-konto. 
* Betalt konto: du ansluter ditt Custom Vision-konto till ditt Video Indexer-konto (om du inte redan har ett konto måste du först skapa ett konto).

### <a name="trial-vs-paid"></a>Utvärdering kontra betald

|Funktioner|Utvärdering|Förutbetalt|
|---|---|---|
|Custom Vision konto|Hanteras i bakgrunden med Video Indexer. |Ditt Custom Vision-konto är anslutet till Video Indexer.|
|Antal modeller för animering|En|Upp till 100 modeller per konto (Custom Vision begränsning).|
|Träna modellen|Video Indexer tågen modellen för nya tecken ytterligare exempel på befintliga tecken.|Konto ägaren tågen modellen när de är redo att göra ändringar.|
|Avancerade alternativ i Custom Vision|Ingen åtkomst till Custom Vision-portalen.|Du kan justera modellerna själv i Custom Vision-portalen.|

## <a name="use-the-animated-character-detection-with-portal"></a>Använda animerat teckensnitts identifiering med portalen 

I det här avsnittet beskrivs de steg som du måste vidta för att börja använda den animerade tecknens identifierings modellen. 

Eftersom Custom Vision-integrering hanteras av Video Indexer i utvärderings kontot, kan du börja skapa och använda den animerade tecken modellen och hoppa över följande avsnitt ("Anslut ditt Custom Vision konto").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Anslut ditt Custom Vision konto (endast betalda konton)

Om du äger ett Video Indexer betalt konto måste du först ansluta ett Custom Vision-konto. Om du inte redan har ett Custom Vision konto kan du skapa ett. Mer information finns i [Custom vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Båda kontona måste finnas i samma region. Custom Vision-integration stöds för närvarande inte i regionen Japan.

#### <a name="connect-a-custom-vision-account-with-api"></a>Ansluta ett Custom Vision-konto med API 

Följ dessa steg för att ansluta dig Custom Vision konto till Video Indexer eller ändra det Custom Vision-konto som är kopplat till Video Indexer:

1. Bläddra till [www.customvision.AI](https://www.customvision.ai) och logga in.
1. Kopiera följande nycklar: 

    * Tränings nyckel (för utbildnings resursen)
    * Förutsägelse nyckel (för förutsägelse resurs)
    * Slutpunkt 
    * Resurs-ID för förutsägelse
    
    > [!NOTE]
    > För att tillhandahålla alla nycklar måste du ha två separata resurser i Custom Vision, en för utbildning och en för förutsägelse.
1. Bläddra och logga in på [video Indexer](https://vi.microsoft.com/).
1. Klicka på frågetecknet i det övre högra hörnet på sidan och välj **API-referens** .
1. Se till att du prenumererar på API Management genom att klicka på fliken **produkter** . Om du har ett API anslutet kan du fortsätta till nästa steg, annars prenumerera. 
1. Klicka på den **fullständiga API-referensen** på Developer-portalen och gå till **åtgärder** .  
1. Välj **anslut Custom vision konto (FÖRhands granskning)** och klicka på **prova** .
1. Fyll i obligatoriska fält och åtkomsttoken och klicka på **Skicka** . 

    Om du vill ha mer information om hur du hämtar Video Indexer-åtkomsttoken går du till [Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)och läser [relevant dokumentation](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Ditt konto är anslutet när anropet returnerade svaret 200 OK.
1. Så här verifierar du din anslutning genom att bläddra till [video Indexer](https://vi.microsoft.com/)-portalen:
1. Klicka på **anpassnings knappen för innehålls modell** i det övre högra hörnet.
1. Gå till fliken **animerade tecken** .
1. När du klickar på Hantera modeller i Custom Vision "* *" överförs du till det Custom Vision-konto som du precis anslöt.

> [!NOTE]
> För närvarande stöds endast modeller som skapats via Video Indexer. Modeller som skapas via Custom Vision är inte tillgängliga. Vi rekommenderar dessutom att du redigerar modeller som har skapats via Video Indexer bara via Video Indexers plattformen, eftersom ändringar som görs via Custom Vision kan orsaka oönskade resultat.

### <a name="create-an-animated-characters-model"></a>Skapa en animerad tecken modell

1. Gå till [Video Indexer](https://vi.microsoft.com/)-webbplatsen och logga in.
1. Klicka på anpassnings knappen för innehålls modell i det övre högra hörnet på sidan.

    ![Skärm bild som visar sidan "Video Indexer" med knappen "anpassning av innehålls modell" markerad i det övre högra hörnet.](./media/animated-characters-recognition/content-model-customization.png)
1. Gå till fliken **animerade tecken** i avsnittet modell anpassning.
1. Klicka på **Lägg till modell** .
1. Namn du modell och klicka på RETUR för att spara namnet.

> [!NOTE]
> Den bästa metoden är att ha en anpassad vision modell för varje animerad serie. 

### <a name="index-a-video-with-an-animated-model"></a>Indexera en video med en animerad modell

1. Klicka på knappen **överför** på den översta menyn.
1. Välj en video att ladda upp (från en fil eller en URL).
1. Klicka på **Avancerade alternativ** .
1. Välj **animerings modeller** under **personer/animerade tecken** .
1. Om du har en modell väljs den automatiskt, och om du har flera modeller kan du välja den som är relevant för den aktuella List menyn.
1. Klicka på överför.
1. När videon har indexerats visas identifierade tecken i avsnittet **animerade tecken** i fönstret **insikter** .

> [!NOTE] 
> Innan du taggar och tränar modellen med varandra får alla animerade tecken namnet "okänd #X". När du har tränat modellen kommer de också att identifieras.

### <a name="customize-the-animated-characters-models"></a>Anpassa modeller för animerade tecken

1. Tagga och träna modellen.

    1. Tagga det identifierade specialtecknet genom att redigera dess namn. När ett kort har tränats in i modellen identifieras det nästa video som indexeras med den modellen. 
    1. Om du vill tagga ett animerat kort i videon går du till fliken **insikter** och klickar på knappen **Redigera** i det övre högra hörnet i fönstret.
    1. I fönstret **insikter** klickar du på något av de synliga animerade tecknen och ändrar deras namn från "okänd #X" (eller det namn som tidigare har tilldelats tecknet).
    1. När du har skrivit det nya namnet klickar du på kryss ikonen bredvid det nya namnet. Detta sparar det nya namnet i modellen i Video Indexer.
    1. När du har redigerat alla namn som du vill ha måste du träna modellen.

        Öppna anpassnings sidan och klicka på fliken **animerade tecken** och klicka sedan på knappen **träna** för att träna din modell.
         
        Om du har ett betalt konto kan du klicka på länken **Hantera modeller i Customer vision** (se nedan). Du kommer sedan att vidarebefordras till modellens sida i **Custom vision** .
 
        ![Anpassning av innehålls modell](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. När de har tränats in kommer alla videor som ska indexeras eller indexeras med den modellen att identifiera de intränaa tecknen. 
    Betalda konton som har åtkomst till sitt Custom Vision konto kan se modeller och taggade bilder där. Lär dig mer om [att förbättra din klassificerare i Custom vision](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md).

1. Ta bort ett animerat teckensnitt.

    1. Om du vill ta bort ett animerat teckensnitt i dina video insikter går du till fliken **insikter** och klickar på knappen **Redigera** i det övre högra hörnet i fönstret.
    1. Välj det animerade specialtecknet och klicka sedan på knappen **ta bort** under namn.

    > [!NOTE]
    > Detta tar bort insikter från den här videon men påverkar inte modellen.

1. Ta bort en modell.

    1. Klicka på knappen **anpassning av innehålls modell** på den översta menyn och gå till fliken **animerade tecken** .
    1. Klicka på ikonen med tre punkter till höger om den modell som du vill ta bort och klicka sedan på knappen Ta bort.
    
    * Betalt konto: modellen kommer att kopplas från Video Indexer och du kommer inte att kunna ansluta den igen.
    * Utvärderings konto: modellen tas även bort från insikten. 
    
        > [!NOTE]
        > I ett utvärderings konto har du bara en modell som du kan använda. När du har tagit bort det kan du inte träna andra modeller.

## <a name="use-the-animated-character-detection-with-api"></a>Använda animerad Character avkänning med API 

1. Anslut ett Custom Vision-konto.

    Om du äger ett Video Indexer betalt konto måste du först ansluta ett Custom Vision-konto. <br/>
    Om du inte redan har ett Custom Vision konto kan du skapa ett. Mer information finns i [Custom vision](../../cognitive-services/custom-vision-service/overview.md).

    [Anslut ditt Custom vision-konto med hjälp av API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Skapa en animerad tecken modell.

    Använd API för att [skapa animerings modell](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Indexera eller indexera om en video.

    Använd [Omindexering](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) av API. 
1. Anpassa modeller för animerade tecken.

    Använd modell-API: t för [träna animering](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) .

### <a name="view-the-output"></a>Visa utdata

Se de animerade tecknen i den genererade JSON-filen.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Begränsningar

* För närvarande stöds inte funktionen "animation Identification" i East-Asia region.
* Tecken som verkar vara små eller långt i videon kanske inte identifieras korrekt om videons kvalitet är låg.
* Rekommendationen är att använda en modell per uppsättning animerade tecken (till exempel per animerad serie).

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)