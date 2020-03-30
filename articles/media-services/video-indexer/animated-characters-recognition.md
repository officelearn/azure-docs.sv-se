---
title: Animerad teckenidentifiering med videoindexerare
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder animerad teckenidentifiering med Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989917"
---
# <a name="animated-character-detection-preview"></a>Animerad teckenidentifiering (förhandsgranskning)

Azure Media Services Video Indexer stöder identifiering, gruppering och igenkänning av tecken i animerat innehåll via integrering med [anpassade visioner](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)för Cognitive Services . Den här funktionen är tillgänglig både via portalen och via API:et.

När du har laddat upp en animerad video med en viss animeringsmodell extraherar Video Indexer nyckelrutor, identifierar animerade figurer i dessa bildrutor, grupper med liknande karaktär och väljer det bästa exemplet. Sedan skickas de grupperade tecknen till Anpassad vision som identifierar tecken baserat på de modeller som den tränades på. 

Innan du börjar träna modellen identifieras tecknen namnlöst. När du lägger till namn och tränar modellen känner Video Indexer igen tecknen och namnger dem därefter.

## <a name="flow-diagram"></a>Flödesdiagram

Följande diagram visar flödet av den animerade teckenidentifieringsprocessen.

![Flödesdiagram](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Konton

Beroende på vilken typ av videoindexerkonto är olika funktionsuppsättningar tillgängliga. Information om hur du ansluter ditt konto till Azure finns i [Skapa ett videoindexerkonto som är anslutet till Azure](connect-to-azure.md).

* Utvärderingskonto: Video Indexer använder ett internt Custom Vision-konto för att skapa modell och ansluta den till ditt Video Indexer-konto. 
* Betalt konto: du ansluter ditt Custom Vision-konto till ditt Video Indexer-konto (om du inte redan har ett måste du skapa ett konto först).

### <a name="trial-vs-paid"></a>Rättegång kontra betald

|Funktioner|Utvärdering|Betalas|
|---|---|---|
|Konto för anpassad syn|Hanteras bakom kulisserna av Video Indexer. |Ditt Custom Vision-konto är anslutet till Video Indexer.|
|Antal animeringsmodeller|En|Upp till 100 modeller per konto (Custom Vision-begränsning).|
|Träna modellen|Video Indexer tränar modellen för nya tecken ytterligare exempel på befintliga tecken.|Kontoägaren tränar modellen när de är redo att göra ändringar.|
|Avancerade alternativ i anpassad syn|Ingen åtkomst till custom vision-portalen.|Du kan själv justera modellerna i portalen Custom Vision.|

## <a name="use-the-animated-character-detection-with-portal"></a>Använda den animerade teckenidentifieringen med portalen 

I det här avsnittet beskrivs de steg du behöver vidta för att börja använda den animerade teckenidentifieringsmodellen. 

Eftersom integreringen av anpassade syner kan du börja skapa och använda modellen med animerade tecken i utvärderingskontona ("Anslut ditt anpassade visionskonto").

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Anslut ditt Custom Vision-konto (endast betalda konton)

Om du äger ett betalkonto för videoindexerare måste du först ansluta ett anpassat visionskonto. Om du inte redan har ett anpassat synkonto kan du skapa ett. Mer information finns i [Anpassad vision](../../cognitive-services/custom-vision-service/home.md).

> [!NOTE]
> Båda kontona måste finnas i samma region. Integrationen av anpassade visioner stöds för närvarande inte i Japan-regionen.

#### <a name="connect-a-custom-vision-account-with-api"></a>Ansluta ett anpassat vision-konto med API 

Följ dessa steg för att ansluta ditt Custom Vision-konto till Video Indexer eller för att ändra kontot För anpassad syn som för närvarande är anslutet till Video Indexer:

1. Bläddra till [www.customvision.ai](https://www.customvision.ai) och logga in.
1. Kopiera följande nycklar: 

    * Utbildningsnyckel (för utbildningsresursen)
    * Prognosnyckel (för förutsägelseresursen)
    * Slutpunkt 
    * Resurs-ID för förutsägelse
    
    > [!NOTE]
    > För att ge alla nycklar måste du ha två separata resurser i Anpassad vision, en för utbildning och en för förutsägelse.
1. Bläddra och logga in på [Video Indexer](https://vi.microsoft.com/).
1. Klicka på frågetecknet längst upp till höger på sidan och välj **API-referens**.
1. Se till att du prenumererar på API Management genom att klicka på **fliken Produkter.** Om du har ett API anslutet kan du fortsätta till nästa steg, annars prenumerera. 
1. Klicka på **fullständig API-referens** på utvecklarportalen och bläddra till **åtgärder**.  
1. Välj **Anslut anpassat synkonto (PREVIEW)** och klicka på **Prova det**.
1. Fyll i de obligatoriska fälten samt åtkomsttoken och klicka på **Skicka**. 

    Mer information om hur du hämtar åtkomsttoken för videoindexer går till [utvecklarportalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)och läser [relevant dokumentation](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. När samtalet returnerar 200 OK-svar är ditt konto anslutet.
1. Så här verifierar du anslutningen genom att bläddra till portalen [Video Indexer](https://vi.microsoft.com/)):
1. Klicka på **anpassningsknappen Innehållsmodell** i det övre högra hörnet.
1. Gå till fliken **Animerade tecken.**
1. När du klickar på Hantera modeller i Custom Vision"**, kommer du att överföras till Custom Vision-konto som du just anslutit.

> [!NOTE]
> För närvarande stöds endast modeller som har skapats via Video Indexer. Modeller som skapas via Anpassad syn kommer inte att vara tillgängliga. Dessutom är det bästa praxis att redigera modeller som har skapats via Video Indexer endast via Video Indexer-plattformen, eftersom ändringar som görs via Custom Vision kan orsaka oavsiktliga resultat.

### <a name="create-an-animated-characters-model"></a>Skapa en modell för animerade figurer

1. Gå till [Video Indexer](https://vi.microsoft.com/)-webbplatsen och logga in.
1. Klicka på anpassningsknappen för innehållsmodell längst upp till höger på sidan.

    ![Anpassning av innehållsmodell](./media/animated-characters-recognition/content-model-customization.png)
1. Gå till fliken **Animerade tecken** i avsnittet modellanpassning.
1. Klicka på **Lägg till modell**.
1. Namn du modell och klicka på enter för att spara namnet.

> [!NOTE]
> Det bästa är att ha en anpassad vision modell för varje animerad serie. 

### <a name="index-a-video-with-an-animated-model"></a>Indexera en video med en animerad modell

1. Klicka på **knappen Ladda upp** från den övre menyn.
1. Välj en video att ladda upp (från en fil eller en URL).
1. Klicka på **Avancerade alternativ**.
1. Under **Personer / Animerade tecken** väljer Animation **modeller**.
1. Om du har en modell väljs den automatiskt, och om du har flera modeller kan du välja den relevanta av rullgardinsmenyn.
1. Klicka på ladda upp.
1. När videon har indexerats visas de identifierade tecknen i avsnittet **Animerade tecken** i fönstret **Insikter.**

> [!NOTE] 
> Innan du taggar och tränar modellen kommer alla animerade figurer att heta "Okänd #X". När du tränar modellen kommer de också att erkännas.

### <a name="customize-the-animated-characters-models"></a>Anpassa modeller för animerade tecken

1. Tag och träna modellen.

    1. Tagga det identifierade tecknet genom att redigera dess namn. När ett tecken har tränats in i modellen, kommer det att kännas igen det nästa video indexeras med den modellen. 
    1. Om du vill tagga ett animerat tecken i videon går du till fliken **Insikter** och klickar på knappen **Redigera** längst upp till höger i fönstret.
    1. I fönstret **Insikter** klickar du på något av de identifierade animerade tecknen och ändrar deras namn från "Okänd #X" (eller namnet som tidigare tilldelats tecknet).
    1. När du har skrivit in det nya namnet klickar du på checkikonen bredvid det nya namnet. Detta sparar det nya namnet i modellen i Video Indexer.
    1. När du har redigerat alla namn du vill ha måste du träna modellen.

        Öppna anpassningssidan och klicka på fliken **Animerade tecken** och klicka sedan på **tågknappen** för att träna din modell.
         
        Om du har ett betalkonto kan du klicka på länken **Hantera modeller i Kundseende** (som visas nedan). Du kommer sedan att vidarebefordras till modellens sida i **Custom Vision**.
 
        ![Anpassning av innehållsmodell](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. När du har tränat kommer alla videor som indexeras eller indexeras om med den modellen att känna igen de tränade tecknen. 
    Betalda konton som har tillgång till sitt Custom Vision-konto kan se modellerna och taggade bilder där. Läs mer om [hur du förbättrar klassificeraren i Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Ta bort ett animerat tecken.

    1. Om du vill ta bort en animerad bild i dina videoinsikter går du till fliken **Insikter** och klickar på knappen **Redigera** längst upp till höger i fönstret.
    1. Välj det animerade tecknet och klicka sedan på knappen **Ta bort** under deras namn.

    > [!NOTE]
    > Detta tar bort insikten från den här videon men påverkar inte modellen.

1. Ta bort en modell.

    1. Klicka på **anpassningsknappen Innehållsmodell** på den övre menyn och gå till fliken **Animerade tecken.**
    1. Klicka på ellipsikonen till höger om den modell du vill ta bort och sedan på delete-knappen.
    
    * Betalkonto: modellen kommer att kopplas från Video Indexer och du kommer inte att kunna återansluta den.
    * Testkonto: modellen kommer att tas bort från tullen vision också. 
    
        > [!NOTE]
        > I ett utvärderingskonto har du bara en modell som du kan använda. När du har tagit bort den kan du inte träna andra modeller.

## <a name="use-the-animated-character-detection-with-api"></a>Använda den animerade teckenidentifieringen med API 

1. Anslut ett anpassat vision-konto.

    Om du äger ett betalkonto för videoindexerare måste du först ansluta ett anpassat visionskonto. <br/>
    Om du inte redan har ett anpassat synkonto kan du skapa ett. Mer information finns i [Anpassad vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home).

    [Anslut ditt Custom Vision-konto med API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Skapa en modell för animerade figurer.

    Använd API:et [för skapa animeringsmodell.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)
1. Indexera eller indexera om en video.

    Använd [api:et för omindexering.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Anpassa modeller för animerade figurer.

    Använd [API:et för tåganimeringsmodellen.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)

### <a name="view-the-output"></a>Visa utdata

Se de animerade figurerna i den genererade JSON-filen.

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

* För närvarande stöds inte funktionen "animeringsidentifiering" i östasien.
* Tecken som verkar vara små eller långt i videon kanske inte identifieras korrekt om videons kvalitet är dålig.
* Rekommendationen är att använda en modell per uppsättning animerade tecken (till exempel per en animerad serie).

## <a name="next-steps"></a>Nästa steg

[Översikt över Video Indexer](video-indexer-overview.md)
