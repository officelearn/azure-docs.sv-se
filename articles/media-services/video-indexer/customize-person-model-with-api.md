---
title: Anpassa en personmodell med API för videoindexerare
titleSuffix: Azure Media Services
description: Läs om hur du anpassar en personmodell med API:et för videoindexerare.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127896"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Anpassa en personmodell med API:et för videoindexerare

Video Indexer stöder ansiktsigenkänning och kändisigenkänning för videoinnehåll. Funktionen kändisigenkänning täcker cirka en miljon ansikten baserat på vanliga datakällor som IMDB, Wikipedia och de främsta LinkedIn-influencers. Ansikten som inte känns igen av funktionen kändisigenkänning identifieras men lämnas namnlösa. När du har laddat upp videon till Video Indexer och fått resultat tillbaka kan du gå tillbaka och namnge ansiktena som inte kändes igen. När du har märkt ett ansikte med ett namn läggs ansiktet och namnet till i kontots personmodell. Video Indexer kommer då att känna igen detta ansikte i dina framtida videor och tidigare videor.

Du kan använda API:et för videoindexer för att redigera ansikten som har identifierats i en video, enligt beskrivningen i det här avsnittet. Du kan också använda webbplatsen Video Indexer, enligt beskrivningen i [Modellen Anpassa person med hjälp av webbplatsen Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Hantera modeller med flera personer

Video Indexer stöder flera personmodeller per konto. Den här funktionen är för närvarande endast tillgänglig via videoindexerings-API:erna.

Om ditt konto tillgodoser olika användningsfallsscenarier kanske du vill skapa flera personmodeller per konto. Om ditt innehåll till exempel är relaterat till sport kan du skapa en separat personmodell för varje sport (fotboll, basket, fotboll och så vidare).

När en modell har skapats kan du använda den genom att ange modell-ID för en viss personmodell när du laddar upp/indexerar eller indexerar om en video. Utbildning ett nytt ansikte för en video uppdaterar den specifika anpassade modellen som videon var associerad med.

Varje konto har en gräns på 50 personmodeller. Om du inte behöver stöd för modellen med flera personer ska du inte tilldela ett personmodell-ID till videon när du laddar upp/indexerar eller indexerar om. I det här fallet använder Video Indexer standardmodellen för anpassad person i ditt konto.

## <a name="create-a-new-person-model"></a>Skapa en ny personmodell

Om du vill skapa en ny personmodell i det angivna kontot använder du API:et [för att skapa en personmodell.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)

Svaret innehåller namnet och genererat modell-ID för den personmodell som du just skapade efter formatet i exemplet nedan.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Du använder sedan **id-värdet** för parametern **personModelId** när [du laddar upp en video för att indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [indexera om en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Ta bort en personmodell

Om du vill ta bort en anpassad personmodell från det angivna kontot använder du [api:et för en personmodell.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)

När personmodellen har tagits bort kommer indexet för dina aktuella videor som använde den borttagna modellen att vara oförändrat tills du indexerar om dem. När du indexerar om identifieras inte ansiktena som har namngetts i den borttagna modellen av Video Indexer i dina aktuella videor som indexerades med den modellen, men ansiktena identifieras fortfarande. Dina aktuella videor som indexerades med den borttagna modellen använder nu kontots standardmodell person. Om ansikten från den borttagna modellen också namnges i kontots standardmodell fortsätter dessa ansikten att kännas igen i videorna.

Det finns inget returnerat innehåll när personmodellen tas bort.

## <a name="get-all-person-models"></a>Hämta alla Person-modeller

Om du vill hämta alla personmodeller i det angivna kontot använder du api:et [för att hämta en personmodell.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

Svaret innehåller en lista över alla personmodeller i ditt konto (inklusive standardmodellen person i det angivna kontot) och var och en av deras namn och ID:er som följer formatet för exemplet nedan.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Du kan välja vilken modell du vill använda `id` för en video `personModelId` genom att använda värdet för personmodellen för parametern när [du laddar upp en video för att indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [indexera om en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Uppdatera ett ansikte

Med det här kommandot kan du uppdatera ett ansikte i videon med ett namn med hjälp av ID:n för videon och id:t i ansiktet. Den här åtgärden uppdaterar sedan personmodellen som videon var kopplad till vid uppladdning/indexering eller omindexering. Om ingen personmodell har tilldelats uppdateras kontots standardmodell person.

Systemet känner sedan igen förekomsterna av samma ansikte i dina andra aktuella videor som delar samma personmodell. Det kan ta lite tid att få effekt när det här är en batchprocess.

Du kan uppdatera ett ansikte som Video Indexer känns igen som en kändis med ett nytt namn. Det nya namnet som du ger kommer att ha företräde framför den inbyggda kändis erkännande.

Om du vill uppdatera ansiktet använder du [uppdateringen av ett videoface API.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)

Namn är unika för personmodeller, så om du ger två `name` olika ansikten i samma personmodell samma parametervärde, visar Video Indexer ansiktena som samma person och konvergerar dem när du indexerar om videon.

## <a name="next-steps"></a>Nästa steg

[Anpassa personmodell med videoindexerarens webbplats](customize-person-model-with-website.md)
