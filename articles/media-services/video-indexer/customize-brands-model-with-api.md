---
title: Anpassa en varumärkesmodell med API för videoindexerare
titleSuffix: Azure Media Services
description: Läs om hur du anpassar en Brands-modell med API:et för videoindexerare.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128000"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Anpassa en brands-modell med API:et för videoindexerare

Video Indexer stöder varumärkesidentifiering från tal- och visuell text vid indexering och omindexering av video- och ljudinnehåll. Varumärkesidentifieringsfunktionen identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bings varumärkesdatabas. Om Microsoft till exempel nämns i video- eller ljudinnehåll eller om det visas i visuell text i en video, identifierar Video Indexer det som ett varumärke i innehållet. Med en anpassad Brands-modell kan du utesluta vissa varumärken från att identifieras och inkludera varumärken som ska ingå i din modell som kanske inte finns i Bings varumärkesdatabas.

En detaljerad översikt finns i [Översikt](customize-brands-model-overview.md).

Du kan använda API:erna för videoindexer för att skapa, använda och redigera anpassade varumärken som identifieras i en video, enligt beskrivningen i det här avsnittet. Du kan också använda webbplatsen Video Indexer enligt beskrivningen i [modellen Anpassa varumärken med hjälp av webbplatsen Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Skapa ett varumärke

[Skapa ett varumärke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) API skapar ett nytt anpassat varumärke och lägger till den anpassade Brands-modellen för det angivna kontot.

> [!NOTE]
> Om `enabled` du ställer in (i brödtexten) för true placeras varumärket i listan *Inkludera* för Video Indexer att identifiera. Om `enabled` du vill false placeras varumärket i listan *Uteslut,* så att videoindexeraren inte identifierar det.

Några andra parametrar som du kan ställa in i kroppen:

* Värdet `referenceUrl` kan vara alla referenswebbplatser för varumärket, till exempel en länk till wikipediasidan.
* Värdet `tags` är en lista med taggar för varumärket. Den här taggen visas i fältet *Kategori för* varumärket på webbplatsen Video Indexer. Varumärket "Azure" kan till exempel taggas eller kategoriseras som "Cloud".

### <a name="response"></a>Svar

Svaret innehåller information om det varumärke som du just skapade i formatet för exemplet nedan.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Ta bort ett varumärke

Ta [bort ett varumärke](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) API tar bort ett varumärke från den anpassade Brands-modellen för det angivna kontot. Kontot anges i parametern. `accountId` När varumärket har anropats har det inte längre finns i listorna *Inkludera* eller *Exkludera* varumärken.

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när varumärket tas bort.

## <a name="get-a-specific-brand"></a>Skaffa ett specifikt varumärke

Med [skaffa ett varumärkes-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) kan du söka efter information om ett varumärke i den anpassade Brands-modellen för det angivna kontot med hjälp av varumärkes-ID.

### <a name="response"></a>Svar

Svaret innehåller information om det varumärke som du sökte (med varumärkes-ID) i formatet för exemplet nedan.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`att vara `true` inställd på att betyda att varumärket är i *include-listan* för Video Indexer att upptäcka, och `enabled` att vara falsk betyder att varumärket finns i listan *Uteslut,* så videoindexerare kommer inte att upptäcka det.

## <a name="update-a-specific-brand"></a>Uppdatera ett visst varumärke

Med uppdateringen av [ett varumärkes-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) kan du söka efter information om ett varumärke i den anpassade brands-modellen för det angivna kontot med hjälp av varumärkes-ID.

### <a name="response"></a>Svar

Svaret innehåller den uppdaterade informationen om varumärket som du uppdaterade i formatet för exemplet nedan.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Få alla varumärken

[Api:et för att få alla varumärken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) returnerar alla varumärken i den anpassade brands-modellen för det angivna kontot oavsett om varumärket är avsett att finnas i listan *Inkludera* eller *Exkludera* varumärken.

### <a name="response"></a>Svar

Svaret innehåller en lista över alla varumärken i ditt konto och var och en av deras uppgifter enligt formatet i exemplet nedan.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Varumärket *Exempel* finns i listan *Inkludera* för Videoindexer att identifiera och varumärket *Example2* finns i listan *Uteslut,* så videoindexeraren identifieras inte.

## <a name="get-brands-model-settings"></a>Hämta modellinställningar för Varumärken

[API:et för get brands-inställningar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) returnerar modellinställningarna för Varumärken i det angivna kontot. Modellinställningarna för Varumärken representerar om identifiering från Bing-märkesdatabasen är aktiverad eller inte. Om Bing-varumärken inte är aktiverade identifierar Video Indexer bara varumärken från den anpassade brands-modellen för det angivna kontot.

### <a name="response"></a>Svar

Svaret visar om Bing-varumärken är aktiverade enligt formatet i exemplet nedan.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`är inställda på true representerar att Bing-varumärken är aktiverade. Om `useBuiltin` är falskt inaktiveras Bing-varumärken. Värdet `state` kan ignoreras eftersom det har inaktuellt.

## <a name="update-brands-model-settings"></a>Modellera inställningar för Update Brands

[Api:et för uppdateringsvarumärken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) uppdaterar modellinställningarna för Varumärken i det angivna kontot. Modellinställningarna för Varumärken representerar om identifiering från Bing-märkesdatabasen är aktiverad eller inte. Om Bing-varumärken inte är aktiverade identifierar Video Indexer bara varumärken från den anpassade brands-modellen för det angivna kontot.

Flaggan `useBuiltIn` inställd på true innebär att Bing-varumärken är aktiverade. Om `useBuiltin` är falskt inaktiveras Bing-varumärken.

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när modellinställningen Brands uppdateras.

## <a name="next-steps"></a>Nästa steg

[Anpassa brands-modellen med hjälp av webbplats](customize-brands-model-with-website.md)
