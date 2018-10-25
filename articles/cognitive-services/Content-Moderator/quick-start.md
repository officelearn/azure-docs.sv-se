---
title: 'Snabbstart: Kom igång med Content Moderator'
titlesuffix: Azure Cognitive Services
description: Hur du kommer igång med Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: f25434814a7fb3d0f49cab539b394970c9bcfb3b
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023448"
---
# <a name="quickstart-get-familiar-with-content-moderator"></a>Snabbstart: Bekanta dig med Content Moderator

I den här snabbstarten ska du använda online Content Moderator-Granskningsverktyget för att testa de grundläggande funktionerna i Content Moderator utan att behöva skriva någon kod. Om du vill integrera den här tjänsten i din app snabbare kan se de andra snabbstarterna i den [nästa steg](#next-steps) avsnittet.

## <a name="prerequisites"></a>Förutsättningar

- En webbläsare

## <a name="set-up-the-review-tool"></a>Konfigurera granskningsverktyget
Content Moderator-Granskningsverktyget är ett webbaserat verktyg som gör att mänsklig granskare för att fatta beslut enklare cognitive service. I den här guiden får du genomgå kort processen för att ställa in granskningsverktyget så att du kan se hur tjänsten Content Moderator fungerar. Gå till den [Content Moderator-Granskningsverktyget](http://contentmoderator.cognitive.microsoft.com/) plats och registrera dig.

![Startsida för innehåll Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Skapa sedan en granskningsteam. I ett scenario med fungerande kommer detta att gruppen med personer som ska du manuellt granska tjänstens moderering beslut. Nu behöver du bara skapa ett namn på teamet. Om du vill att bjuda in kolleger till teamet kan göra du det genom att ange deras e-postadresser här.

![Bjud in gruppmedlem](images/QuickStart-2-small.png)

## <a name="upload-sample-content"></a>Ladda upp innehåll

Nu är du redo att överföra innehåll. Välj **försök > bild**, **försök > Text**, eller **försök > Video**.

![Testa avbildningen eller Textmoderering](images/tryimagesortext.png)

Skicka in ditt innehåll för moderering. Internt anropar granskningsverktyget moderering API: er så att ditt innehåll. När den skanningen är klar, visas ett meddelande om att det inte finns några resultat väntar på att granska.

![Måttlig filer](images/submitted.png)

## <a name="review-moderation-tags"></a>Granska moderering taggar

Granska de tillämpade moderering taggarna. Du kan se vilka taggar har kopplats till ditt innehåll och vad poängen var i varje kategori.

![Granska resultatet](images/reviewresults_text.png)

Du eller ditt granskningsteam kan ändra dessa taggar eller lägga till fler taggar efter behov i ett projekt. Du skickar ändringarna med den **nästa** knappen. Dina affärsprogram anropar Moderator-API: er, kommer taggade innehållet köa här, kan granskas av mänsklig granskning-team. Du kan snabbt granska stora mängder innehåll med hjälp av den här metoden.

Du har nu använt Content Moderator-Granskningsverktyget för att se exempel på vad tjänsten Content Moderator kan göra. Därefter kan du antingen lära dig mer om verktyg för granskning och hur du integrerar dem i ett programvaruprojekt med API: erna granska eller du kan gå vidare till den [nästa steg](#next-steps) att ta reda på hur du använder Moderering API: erna sig själva i din app.

## <a name="learn-more-about-the-review-tool"></a>Mer information om granskningsverktyget

Mer information om hur du använder Content Moderator granska för att ta en titt på de [Human-i-the-loop](Review-Tool-User-Guide/human-in-the-loop.md) guide och se granska verktyget API: erna information om hur du finjustera upplevelse för mänsklig granskning:
- Den [jobbet API](try-review-api-job.md) söker igenom ditt innehåll med hjälp av API: er för moderering och genererar granskningar i granskningsverktyget. 
- Den [granska API](try-review-api-review.md) direkt skapar bild, text eller video granskningar för mänskliga moderatorer utan att söka igenom innehållet. 
- Den [arbetsflöde API](try-review-api-workflow.md) skapar, uppdaterar och hämtar information om de anpassade arbetsflöden som ditt team skapar.

Eller Fortsätt med nästa steg för att komma igång med API: er för Moderering i din kod.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du Moderering API: erna i själva i din app.
- Implementera bildmoderering. Använd den [API-konsol](try-image-api.md) eller [ C# snabbstarten](image-moderation-quickstart-dotnet.md) Genomsök bilder och identifiera potentiellt vuxet eller olämpligt innehåll med hjälp av taggar, förtroende poäng och andra extraherade information.
- Implementera textmoderering. Använd den [API-konsol](try-text-api.md) eller använda den [ C# snabbstarten](text-moderation-quickstart-dotnet.md) söker igenom textinnehåll för potentiella svordomar, datorstödd oönskad text klassificering (förhandsversion) och personligt identifierbar information (PII). 
- Implementera videomoderering. Använd den [ C# snabbstarten](video-moderation-api.md) att söka igenom videor och identifiera potentiellt vuxet eller olämpligt innehåll. 
