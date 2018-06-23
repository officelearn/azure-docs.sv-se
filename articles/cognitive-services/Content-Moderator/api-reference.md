---
title: Azure innehåll kontrollant API-referens | Microsoft Docs
description: 'Lär dig mer om innehåll avbrottsmoderering och granska API: er för innehåll kontrollanten.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 94de9b91cc242e8b7e5479cacab8380adac551f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351564"
---
# <a name="content-moderator-api-reference"></a>API-referens för Content Moderator

Du komma igång med Azure innehåll kontrollant API: er på följande sätt: (Se även [hantera autentiseringsuppgifter](review-tool-user-guide/credentials.md).)

- I Azure-portalen [prenumerera på API: er för innehåll kontrollant](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Registrera dig för den [kontrollant innehållsgranskning verktyget](http://contentmoderator.cognitive.microsoft.com/). Se [Quickstart](quick-start.md).

## <a name="moderation-apis"></a>API:er för moderering

Du kan använda följande innehåll kontrollant API: erna för att ställa in efter avbrottsmoderering-arbetsflöden.

| Beskrivning | Referens |
| -------------------- |-------------|
| **Bild Avbrottsmoderering API**<br /><br />Skanna bilder och identifiera potentiella innehåll för vuxna och dyr med hjälp av taggar, förtroende poäng och andra extraherade information. <br /><br />Använd informationen för att publicera, neka eller granska innehållet i arbetsflödet efter måtta. <br /><br />| [Bild av Avbrottsmoderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "bild Avbrottsmoderering API-referens")   |
| **Text Avbrottsmoderering API**<br /><br />Skanna textinnehåll. Svordomar villkoren och personligt identifierbar information (PII) returneras. <br /><br />Använd informationen för att publicera, neka eller granska innehållet i arbetsflödet efter måtta.<br /><br /> | [Text Avbrottsmoderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Text Avbrottsmoderering API-referens")   |
| **Video Avbrottsmoderering API**<br /><br />Skanna videor och identifiera potentiella innehåll för vuxna och dyr. <br /><br />Använd informationen för att publicera, neka eller granska innehållet i arbetsflödet efter måtta.<br /><br /> | [Avbrottsmoderering API videoöversikt](video-moderation-api.md "Video Avbrottsmoderering API-översikt")   |
| **Visa en lista över hanterings-API**<br /><br />Skapa och hantera anpassade undantag eller inkludering listor med bilder och text. Om aktiverad kan den **bild – matchar** och **Text - skärmen** operations vill fuzzy matchar innehållet mot din anpassade listor som skickas. <br /><br />Du kan hoppa över machine learning-baserade avbrottsmoderering steg effektivt.<br /><br /> | [Visa en lista över Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "listan Management API-referens")   |

## <a name="review-api"></a>Granska API

Granska API innehåller följande komponenter:

| Beskrivning | Referens |
| -------------------- |-------------|
| **Jobb**<br /><br /> Starta genomsökningen och granska avbrottsmoderering arbetsflöden för både bilden och texten innehåll. Ett jobb med måtta söker ditt innehåll med hjälp av det bild Avbrottsmoderering API och Text Avbrottsmoderering API. Avbrottsmoderering jobb använda det definierade och arbetsflöden för att generera granskningar som standard. <br /><br />När en mänsklig kontrollant har granskat automatiskt tilldelade taggar och förutsägelse data och skicka innehållet avbrottsmoderering beslut, skickar granska API all information till API-slutpunkt.<br /><br /> | [Jobbet referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "jobbet referens")   |
| **Granskningar**<br /><br />Använd verktyget granska direkt skapa bild eller text omdömen för mänsklig kontrollanter.<br /><br /> När en mänsklig kontrollant har granskat automatiskt tilldelade taggar och förutsägelse data och skicka innehållet avbrottsmoderering beslut, skickar granska API all information till API-slutpunkt.<br /><br /> | [Granska referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "granska referens")   |
| **Arbetsflöden**<br /><br />Skapa, uppdatera och få information om de anpassade arbetsflöden som ni skapar. Du kan definiera arbetsflöden med hjälp av verktyget granska. <br /> <br />Arbetsflöden kan du vanligtvis använder innehåll kontrollant, men kan också använda vissa andra API: er som är tillgängliga som kopplingar i verktyget granska.<br /><br /> | [Referens för arbetsflöde](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "arbetsflöde referens")   |


