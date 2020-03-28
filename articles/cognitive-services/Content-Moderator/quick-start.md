---
title: 'Snabbstart: Prova Innehållsmoderator på webben - Innehållsmoderator'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten kommer du att använda online Content Moderator Review verktyg för att testa de grundläggande funktionerna i Content Moderator utan att behöva skriva någon kod.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 03/13/2020
ms.author: pafarley
ms.openlocfilehash: 666b70ba8b632cb2cadf20de384e3e615acb2b3d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79203595"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Snabbstart: Prova Innehållsmoderator på webben

I den här snabbstarten använder du verktyget för granskning av innehållsremoer online för att testa de grundläggande funktionerna i Content Moderator utan att behöva skriva någon kod. Om du vill integrera den här tjänsten snabbare i din app kan du se de andra snabbstarterna i avsnittet [Nästa steg.](#next-steps)

## <a name="prerequisites"></a>Krav

- En webbläsare

## <a name="set-up-the-review-tool"></a>Konfigurera granskningsverktyget
Content Moderator Review-verktyget är ett webbaserat verktyg som gör det möjligt för mänskliga granskare att hjälpa den kognitiva tjänsten att fatta beslut. I den här guiden kommer du att gå igenom den korta processen med att ställa in granskningsverktyget så att du kan se hur tjänsten Content Moderator fungerar. Gå till webbplatsen för granskning av [innehållsmodererator](https://contentmoderator.cognitive.microsoft.com/) och registrera dig.

![Startsida för innehållsmoderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Skapa sedan ett granskningsteam. I ett arbetsscenario är detta den grupp personer som manuellt granskar tjänstens modereringsbeslut. Om du vill skapa ett team måste du välja en **region**och ange ett **teamnamn** och ett **team-ID**. Om du vill bjuda in kollegor till teamet kan du göra det genom att ange deras e-postadresser här.

> [!NOTE]
> **Team namn** är ett eget namn för din granskning team. Det här är namnet som visas i Azure-portalen. **Team-ID:et** är vad som används för att identifiera granskningsteamet programatiskt.

> [!div class="mx-imgBorder"]
> ![Bjud in gruppmedlem](images/create-team.png)

Om du väljer att kryptera data med en kundhanterad nyckel (CMK) blir du tillfrågad om **resurs-ID** för din Content Moderator-resurs på E0-prisnivån. Resursen du anger måste vara ny. 

> [!div class="mx-imgBorder"]
> ![Bjud in gruppmedlem med CMK](images/create-team-cmk.png)

Om du försöker återanvända en content moderator-resurs visas den här varningen: 

> [!div class="mx-imgBorder"]
> ![CMK-fel](images/create-team-cmk-fail.png)

## <a name="upload-sample-content"></a>Ladda upp exempelinnehåll

Nu är du redo att ladda upp exempelinnehåll. Välj **Prova > bild,** **Prova > text**eller Prova **> Video**.

![Prova bild- eller textmodering](images/tryimagesortext.png)

Skicka in ditt innehåll för moderering. Internt kommer granskningsverktyget att anropa modererings-API:erna för att skanna innehållet. När skanningen är klar visas ett meddelande som informerar dig om att det finns resultat som väntar på din recension.

![Moderera filer](images/submitted.png)

## <a name="review-moderation-tags"></a>Granska modereringstaggar

Granska de använda modereringstaggarna. Du kan se vilka taggar som tillämpades på ditt innehåll och vad poängen var i varje kategori. Mer information om vad de olika innehållstaggarna anger finns i avsnitten [Bild,](image-moderation-api.md) [Text](text-moderation-api.md)och Videomoderering. [Video](video-moderation-api.md)

![Granska resultatet](images/reviewresults_text.png)

I ett projekt kan du eller granskningsgruppen ändra dessa taggar eller lägga till fler taggar efter behov. Du skickar dessa ändringar med knappen **Nästa.** När ditt företagsprogram anropar moderator-API:erna köar det taggade innehållet här uppe, redo att granskas av de mänskliga granskningsteamen. Du kan snabbt granska stora mängder innehåll med den här metoden.

Nu har du använt verktyget Granskning av innehållsmodererator för att se exempel på vad tjänsten Content Moderator kan göra. Därefter kan du antingen lära dig mer om granskningsverktyget och hur du integrerar det i ett programvaruprojekt med hjälp av gransknings-API:erna, eller så kan du gå vidare till avsnittet [Nästa steg](#next-steps) för att lära dig hur du använder modererings-API:erna själva i appen.

## <a name="learn-more-about-the-review-tool"></a>Läs mer om granskningsverktyget

Om du vill veta mer om hur du använder verktyget Granskning av innehållsmoderatorer kan du ta en titt på [verktygsguiden](Review-Tool-User-Guide/human-in-the-loop.md) för granskning och läsa api:erna för granskningsverktyg för att lära dig hur du finjusterar den mänskliga granskningsupplevelsen:
- [Jobb-API:et](try-review-api-job.md) söker igenom innehållet med hjälp av modererings-API:erna och genererar recensioner i granskningsverktyget. 
- [Gransknings-API:et](try-review-api-review.md) skapar direkt bild-, text- eller videogranskningar för mänskliga moderatorer utan att först skanna innehållet. 
- [Arbetsflödes-API:et](try-review-api-workflow.md) skapar, uppdaterar och hämtar information om de anpassade arbetsflöden som ditt team skapar.

Du kan också fortsätta med nästa steg för att komma igång med api:erna för moderering i koden.

## <a name="next-steps"></a>Nästa steg

Läs om hur du använder modererings-API:erna själva i appen.
- Implementera bildmoderation. Använd [API-konsolen](try-image-api.md) eller följ [snabbstarten .NET SDK](dotnet-sdk-quickstart.md) för att skanna bilder och identifiera potentiellt innehåll för vuxna och racy med hjälp av taggar, konfidenspoäng och annan extraherad information.
- Implementera textmodering. Använd [API-konsolen](try-text-api.md) eller använd [snabbstarten .NET SDK](dotnet-sdk-quickstart.md) för att söka igenom textinnehåll efter potentiell svordomar, maskinstödd oönskad textklassificering (förhandsgranskning) och personliga data.
- Implementera videomoderation. Följ [videomoderationsvägningen för C# för](video-moderation-api.md) att skanna videor och upptäcka potentiellt innehåll för vuxna och racy. 
