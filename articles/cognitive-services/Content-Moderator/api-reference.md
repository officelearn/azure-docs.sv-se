---
title: API reference - Content Moderator
titlesuffix: Azure Cognitive Services
description: 'Lär dig mer om olika innehållsmoderering och granska API: er för Content Moderator.'
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 320638c958799bbf7fea73880fd3e27b6d598d23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269535"
---
# <a name="content-moderator-api-reference"></a>API-referens för Content Moderator

Du Kom igång med Azure Content Moderator API: er på följande sätt: (Se även [hantera autentiseringsuppgifter](review-tool-user-guide/credentials.md).)

- I Azure-portalen [prenumerera på API: er för Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Registrera dig för den [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/). Se [försök Content Moderator på webben](quick-start.md).

## <a name="moderation-apis"></a>API:er för moderering

Du kan använda följande Content Moderator API: erna för att ställa in efter moderering-arbetsflöden.

| Beskrivning | Referens |
| -------------------- |-------------|
| **API för Bildmoderering**<br /><br />Genomsök bilder och identifierar potentiellt vuxet eller olämpligt innehåll med hjälp av taggar, förtroende poäng och andra extrahera informationen. <br /><br />Använd den här informationen för att publicera, neka eller granska innehållet i arbetsflödet efter moderering. <br /><br />| [Bild Moderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Bildmoderering API-referens")   |
| **API för Textmoderering**<br /><br />Skanna textinnehåll. Svordomar villkoren och personliga data returneras. <br /><br />Använd den här informationen för att publicera, neka eller granska innehållet i arbetsflödet efter moderering.<br /><br /> | [Moderering av API-referens för text](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Text Moderering API-referens")   |
| **API för Videomoderering**<br /><br />Genomsök videor och identifiera potentiellt vuxet eller olämpligt innehåll. <br /><br />Använd den här informationen för att publicera, neka eller granska innehållet i arbetsflödet efter moderering.<br /><br /> | [Översikt över video Moderering API](video-moderation-api.md "Video Moderering API-översikt")   |
| **Lista över hanterade API: er**<br /><br />Skapa och hantera anpassade undantag eller inkludering listor med bilder och text. Om aktiverad kan den **bild – matchar** och **Text - skärmen** operations gör partiell matchning av skickade innehållet mot dina anpassade listor. <br /><br />För effektivitet, kan du hoppa över machine learning-baserade moderering steg.<br /><br /> | [Lista över API-referens hantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "listan Management API-referens")   |

## <a name="review-api"></a>Granska API

Granska API: et har följande komponenter:

| Beskrivning | Referens |
| -------------------- |-------------|
| **Jobb**<br /><br /> Starta sökning och granska moderering arbetsflöden för både bild- och innehåll. Ett jobb för moderering söker igenom ditt innehåll med hjälp av Image Moderering API och Moderering av Text-API. Moderering jobb använda det definierade och arbetsflöden för att generera granskningar som standard. <br /><br />När en mänsklig moderator har granskat automatiskt tilldelade taggar och förutsägelsedata och skickat ett innehållsmoderering beslut, skickar granska API: et all information som ditt API-slutpunkten.<br /><br /> | [Jobb-referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "jobb-referens")   |
| **Granskningar**<br /><br />Använd granskningsverktyget direkt skapa bild eller text granskningar för mänskliga moderatorer.<br /><br /> När en mänsklig moderator har granskat automatiskt tilldelade taggar och förutsägelsedata och skickat ett innehållsmoderering beslut, skickar granska API: et all information som ditt API-slutpunkten.<br /><br /> | [Granska referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "granska referens")   |
| **Arbetsflöden**<br /><br />Skapa, uppdatera och få information om de anpassade arbetsflöden som ditt team skapar. Du definierar arbetsflöden med hjälp av verktyg för granskning. <br /> <br />Arbetsflöden kan du vanligtvis använda Content Moderator, men kan också använda vissa API: er som är tillgängliga som kopplingar i granskningsverktyget.<br /><br /> | [Referens för arbetsflödet](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "arbetsflöde referens")   |


