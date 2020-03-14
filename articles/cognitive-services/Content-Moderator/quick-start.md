---
title: 'Snabb start: prova Content Moderator på webben – Content Moderator'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten ska du använda verktyget för Content Moderator granskning online för att testa de grundläggande funktionerna i Content Moderator utan att behöva skriva någon kod.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 03/13/2020
ms.author: pafarley
ms.openlocfilehash: 666b70ba8b632cb2cadf20de384e3e615acb2b3d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203595"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Snabb start: prova Content Moderator på webben

I den här snabb starten använder du verktyget för Content Moderator granskning online för att testa de grundläggande funktionerna i Content Moderator utan att behöva skriva någon kod. Om du vill integrera den här tjänsten i appen snabbare, se de andra snabb starterna i avsnittet [Nästa steg](#next-steps) .

## <a name="prerequisites"></a>Förutsättningar

- En webbläsare

## <a name="set-up-the-review-tool"></a>Konfigurera gransknings verktyget
Verktyget Content Moderator granskning är ett webbaserat verktyg som gör det möjligt för mänskliga granskare att hjälpa kognitiva tjänster att fatta beslut. I den här guiden ska du gå igenom kort processen för att konfigurera gransknings verktyget så att du kan se hur tjänsten Content Moderator fungerar. Gå till webbplatsen för [Content moderator granska verktyg](https://contentmoderator.cognitive.microsoft.com/) och registrera dig.

![Content Moderator start sida](images/homepage.PNG)

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Skapa sedan en gransknings grupp. I ett arbets scenario är det här gruppen med personer som manuellt kommer att granska tjänstens kontroll beslut. Om du vill skapa ett team måste du välja en **region**och ange ett **grupp namn** och ett **Team-ID**. Om du vill bjuda in kollegor till teamet kan du göra det genom att ange deras e-postadresser här.

> [!NOTE]
> **Team namn** är ett eget namn för gransknings teamet. Detta är det namn som visas i Azure Portal. **Team-ID: t** är det som används för att identifiera program mässigt för gransknings teamet.

> [!div class="mx-imgBorder"]
> ![Bjud in grupp medlem](images/create-team.png)

Om du väljer att kryptera data med hjälp av en kundhanterad nyckel (CMK) uppmanas du att ange **resurs-ID** för din Content moderator-resurs på E0 pris nivå. Den resurs du anger måste vara ny. 

> [!div class="mx-imgBorder"]
> ![Bjud in grupp medlem med CMK](images/create-team-cmk.png)

Om du försöker återanvända en Content Moderator resurs visas den här varningen: 

> [!div class="mx-imgBorder"]
> ![CMK Miss lyckas](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Ladda upp exempel innehåll

Nu är du redo att ladda upp exempel innehåll. Välj **prova > bild**, **prova > Text**eller **prova > Video**.

![Prova bild-eller text redigering](images/tryimagesortext.png)

Skicka in ditt innehåll för redaktörering. Internt kommer gransknings verktyget att anropa redigerings-API: erna för att söka igenom innehållet. När genomsökningen är klar visas ett meddelande som talar om att det finns resultat som väntar på din granskning.

![Måttliga filer](images/submitted.png)

## <a name="review-moderation-tags"></a>Granska moderator Taggar

Granska de använda redigerings taggarna. Du kan se vilka taggar som tillämpades på ditt innehåll och hur poängen var i varje kategori. Se avsnitten [avbildning](image-moderation-api.md), [text](text-moderation-api.md)och [video](video-moderation-api.md) för att lära dig mer om vad olika taggar för innehåll visar.

![Granska resultatet](images/reviewresults_text.png)

I ett projekt kan du eller din gransknings grupp ändra dessa taggar eller lägga till fler taggar efter behov. Du skickar dessa ändringar med knappen **Nästa** . När ditt företags program anropar moderator-API: erna kommer det taggade innehållet att placeras här, vilket är klart att granskas av de mänskliga gransknings teamen. Du kan snabbt granska stora mängder innehåll med hjälp av den här metoden.

Nu har du använt verktyget Content Moderator granskning för att se ett exempel på vad tjänsten Content Moderator kan göra. Sedan kan du antingen lära dig mer om gransknings verktyget och hur du integrerar det i ett program projekt med hjälp av gransknings-API: erna, eller så kan du gå vidare till [Nästa steg](#next-steps) -avsnitt om du vill lära dig hur du använder API: erna för kontrollanter i din app.

## <a name="learn-more-about-the-review-tool"></a>Läs mer om gransknings verktyget

Om du vill veta mer om hur du använder verktyget för Content Moderator granskning kan du titta närmare på [gransknings](Review-Tool-User-Guide/human-in-the-loop.md) verktygs guiden och se API: erna för gransknings verktyg för att lära dig att finjustera mänsklig gransknings upplevelsen:
- [Jobb-API: et](try-review-api-job.md) skannar ditt innehåll med hjälp av API: erna för kontrollanter och genererar granskningar i gransknings verktyget. 
- I [gransknings-API: et](try-review-api-review.md) skapas direkt bild-, text-eller video granskningar för de mänskliga moderatorerna utan att först skanna innehållet. 
- [Arbets flödes-API: et](try-review-api-workflow.md) skapar, uppdaterar och hämtar information om de anpassade arbets flöden som ditt team skapar.

Eller Fortsätt med nästa steg för att komma igång med redigerings-API: er i din kod.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder redigerings-API: erna själva i din app.
- Implementera avbildnings moderator. Använd [API-konsolen](try-image-api.md) eller följ snabb starten för [.NET SDK](dotnet-sdk-quickstart.md) för att skanna bilder och identifiera potentiellt vuxna och vågat innehåll genom att använda taggar, konfidens resultat och annan extraherad information.
- Implementera text moderator. Använd [API-konsolen](try-text-api.md) eller Använd snabb starten för [.NET SDK](dotnet-sdk-quickstart.md) om du vill söka efter potentiella svordomar i text innehåll, oönskad text klassificering (för hands version) och personliga data.
- Implementera videoinspelning. Följ [guiden för videoinspelnings anvisningar för att söka C# efter](video-moderation-api.md) videor och identifiera potentiellt vuxna och vågat innehåll. 
