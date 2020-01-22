---
title: 'Använd Video Indexer-API: et för att anpassa en person modell – Azure'
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en person modell med Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 370e9e515359e2e2e598db90aa379f796b13c3fe
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292407"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Anpassa en person modell med Video Indexer API

Video Indexer stöder ansikts igenkänning och kändis igenkänning för video innehåll. Kändis igenkännings funktionen täcker cirka 1 000 000 ansikten baserat på den begärda data källan, till exempel IMDB-, Wikipedia-och Top LinkedIn-påverkan. Ansikten som inte känns igen av kändis igenkännings funktion identifieras. de får dock inget namn. När du har laddat upp videon för att Video Indexer och få tillbaka resultatet kan du gå tillbaka och namnge de ansikten som inte kändes igen. När du har namngett ett ansikte med ett namn läggs ansikte och namn till i kontots person modell. Video Indexer kommer sedan att identifiera den här ytan i dina kommande videor och tidigare videor.

Du kan använda Video Indexer API för att redigera ansikten som upptäcktes i en video, enligt beskrivningen i det här avsnittet. Du kan också använda Video Indexer webbplats, som beskrivs i [Anpassa person modellen med hjälp av video Indexer webbplats](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Hantera flera person modeller 

Video Indexer stöder flera person modeller per konto. Den här funktionen är för närvarande endast tillgänglig via Video Indexer API: er.

Om ditt konto tillgodoser olika användnings fall kan du vilja skapa flera person modeller per konto. Om ditt innehåll till exempel är relaterat till idrotts kan du skapa en separat person modell för varje sport (fotboll, basket boll, fotboll osv.). 

När en modell har skapats kan du använda den genom att ange modell-ID: t för en speciell person modell vid överföring/indexering eller Omindexering av en video. Träna en ny ansikte för en video uppdaterar den speciella anpassade modell som videon var kopplad till.

Varje konto har en gräns på 50 person modeller. Om du inte behöver stöd för flera person modeller tilldelar du inte ett ID för person modeller till videon när du laddar upp/indexerar eller Omindexerar. I det här fallet använder Video Indexer standard modellen för anpassade personer i ditt konto.

## <a name="create-a-new-person-model"></a>Skapa en ny person modell

Om du vill skapa en ny person modell i det angivna kontot använder du [skapa en person modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) -API.

Svaret innehåller namnet och det genererade modell-ID: t för den person modell som du nyss skapade enligt formatet i exemplet nedan.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Du bör sedan använda **ID-** värdet för parametern **personModelId** när du [laddar upp en video för att indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [Indexera om en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Ta bort en person modell

Om du vill ta bort en anpassad person modell från det angivna kontot använder du [ta bort en person modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) -API. 

När person modellen har tagits bort förblir indexet för dina aktuella videor som använde den borttagna modellen oförändrade tills du indexerar om dem. Vid Omindexering kommer de ansikten som beskrevs i den borttagna modellen inte att kännas igen av Video Indexer i dina aktuella videor som har indexerats med modellen. dessa ansikten kommer dock fortfarande att identifieras. Dina aktuella videor som indexeras med den borttagna modellen använder nu ditt kontos standard person modell. Om ansikten från den borttagna modellen också är namngivna i ditt kontos standard modell, kommer dessa ansikten att fortsätta att kännas igen i videor.

Det finns inget returnerat innehåll när person modellen har tagits bort.

## <a name="get-all-person-models"></a>Hämta alla person modeller

Om du vill hämta alla person modeller på det angivna kontot använder du [Hämta en person modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) -API.

Svaret innehåller en lista över alla person modeller i ditt konto (inklusive standard person modellen i det angivna kontot) och var och en av deras namn och ID: n efter formatet i exemplet nedan.

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

Du kan välja vilken modell du vill använda för en video genom att använda **ID-** värdet för person modellen för **personModelId** -parametern när du [laddar upp en video för att indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller indexera om [en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Uppdatera ett ansikte

Med det här kommandot kan du uppdatera en ansikte i videon med ett namn med ID: t för videon och ID: t för FACET. Då uppdateras den person modell som videon var kopplad till vid uppladdning/indexering eller Omindexering. Om ingen person modell har tilldelats, uppdaterar den kontots standard person modell. 

När detta händer känner den igen förekomster av samma ansikte i dina andra aktuella videor som delar samma person modell. Det kan ta lite tid att identifiera ansikte i dina andra aktuella videor eftersom det är en batch-process.

Du kan uppdatera en ansikte som Video Indexer identifieras som en kändis med ett nytt namn. Det nya namnet som du ger företräde framför den inbyggda kändis-igenkänningen.

Om du vill uppdatera FACET använder du [Uppdatera ett video ansikts](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) -API.

Namnen är unika för person modeller, så om du ger två olika ansikten i samma person modell samma **namn** parameter värde video Indexer Visa ansikten som samma person och konvergerar dem när du har indexerat om videon. 

## <a name="next-steps"></a>Nästa steg

[Anpassa person modellen med hjälp av Video Indexer webbplats](customize-person-model-with-website.md)
