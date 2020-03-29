---
title: API-referens - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Läs mer om de olika api:erna för innehållsmoderererering och granska API:er för Innehållsmoderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757383"
---
# <a name="content-moderator-api-reference"></a>API-referens för innehållsmoderator

Du kan komma igång med Azure Content Moderator API:er på följande sätt:

- Prenumerera [på API:et för innehållsmoderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)i Azure-portalen .
- Se [Prova innehållsmoderator på webben](quick-start.md) för att registrera dig med verktyget Granskning av [innehållsmodererator](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>API:er för moderering

Du kan använda följande API:er för innehållsmodererator för att konfigurera arbetsflöden för efter moderering.

| Beskrivning | Referens |
| -------------------- |-------------|
| **API för bildmoderering**<br /><br />Skanna bilder och identifiera potentiellt innehåll för vuxna och racy med hjälp av taggar, konfidenspoäng och annan extraherad information. <br /><br />Använd den här informationen om du vill publicera, avvisa eller granska innehållet i arbetsflödet efter moderering. <br /><br />| [API-referens för bildmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "API-referens för bildmoderering")   |
| **API för textmoderering**<br /><br />Skanna textinnehåll. Svordomar och personuppgifter returneras. <br /><br />Använd den här informationen om du vill publicera, avvisa eller granska innehållet i arbetsflödet efter moderering.<br /><br /> | [API-referens för textmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "API-referens för textmoderering")   |
| **API för videomoderering**<br /><br />Skanna videor och upptäck potentiellt innehåll för vuxna och racy. <br /><br />Använd den här informationen om du vill publicera, avvisa eller granska innehållet i arbetsflödet efter moderering.<br /><br /> | [Api-översikt för videomoderering](video-moderation-api.md "Api-översikt för videomoderering")   |
| **API för listhantering**<br /><br />Skapa och hantera anpassade undantags- eller inkluderingslistor med bilder och text. Om det är aktiverat gör åtgärderna **Bild - Matchning** och **Text -** suddig matchning av det inskickade innehållet mot dina anpassade listor. <br /><br />För effektivitet kan du hoppa över det maskininlärningsbaserade måttfullhetssteget.<br /><br /> | [API-referens för listhantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "API-referens för listhantering")   |

## <a name="review-apis"></a>Omdömes-API:er

Gransknings-API:erna har följande komponenter:

| Beskrivning | Referens |
| -------------------- |-------------|
| **Jobb**<br /><br /> Initiera arbetsflöden för skanning och granskning av moderering för både bild- och textinnehåll. Ett modereringsjobb söker igenom innehållet med hjälp av API:et för bildmoderering och API:et för textmoderering. Modereringsjobb använder de definierade och standardarbetsflödena för att generera granskningar. <br /><br />När en mänsklig moderator har granskat de automatiskt tilldelade taggarna och förutsägelsedata och skickat ett beslut om innehållsmoderering skickar gransknings-API:et all information till api-slutpunkten.<br /><br /> | [Jobbreferens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Jobbreferens")   |
| **Omdömen**<br /><br />Använd granskningsverktyget för att direkt skapa bild- eller textrecensioner för mänskliga moderatorer.<br /><br /> När en mänsklig moderator har granskat de automatiskt tilldelade taggarna och förutsägelsedata och skickat ett beslut om innehållsmoderering skickar gransknings-API:et all information till api-slutpunkten.<br /><br /> | [Granska referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Granska referens")   |
| **Arbetsflöden**<br /><br />Skapa, uppdatera och få information om de anpassade arbetsflöden som ditt team skapar. Du definierar arbetsflöden med hjälp av granskningsverktyget. <br /> <br />Arbetsflöden använder vanligtvis Innehållsmoderator, men kan också använda vissa andra API:er som är tillgängliga som kopplingar i granskningsverktyget.<br /><br /> | [Arbetsflödesreferens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Arbetsflödesreferens")   |