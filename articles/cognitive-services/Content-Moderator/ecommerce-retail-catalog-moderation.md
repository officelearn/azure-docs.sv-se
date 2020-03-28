---
title: 'Handledning: Måttlig e-handel produktbilder - Content Moderator'
titleSuffix: Azure Cognitive Services
description: Den här självstudien visar hur du konfigurerar ett program för att analysera och klassificera produktavbildningar med angivna etiketter (med Azure Computer Vision och Custom Vision). Tagga stötande avbildningar som ska granskas ytterligare (med Hjälp av Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 5e74eda9e30c536c0eba4e847019344c87e10cce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774338"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Självstudiekurs: Måttliga produktavbildningar för e-handel med Azure Content Moderator

I den här självstudien får du lära dig hur du använder Azure Cognitive Services, inklusive Content Moderator, för att klassificera och moderera produktavbildningar för ett e-handelsscenario. Du använder datorseende och anpassad syn för att använda taggar (etiketter) på bilder, och sedan skapar du en gruppgranskning som kombinerar Content Moderators maskininlärningsbaserade teknik med mänskliga granskningsteam för att tillhandahålla ett intelligent modereringssystem.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera dig för Content Moderator och skapa ett granskningsteam.
> * Använd Content Moderators bild-API för att söka efter potentiellt vuxet eller vågat innehåll.
> * Använd tjänsten för visuellt innehåll för att söka efter kändisinnehåll (eller andra identifierbara taggar för visuellt innehåll).
> * Använd tjänsten Custom Vision om du vill söka efter flaggor, leksaker och pennor (eller andra anpassade taggar).
> * Presentera kombinerade sökresultat för mänsklig granskning och slutgiltigt beslutsfattande.

Den fullständiga exempelkoden är tillgänglig på lagringsplatsen [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) på GitHub.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator-tjänsten och få din nyckel.
- En prenumerationsnyckel för visuellt innehåll (samma instruktioner som ovan).
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).
- En uppsättning bilder för varje etikett som klassificeraren för Custom Vision ska använda (i det här fallet leksaker, pennor och amerikanska flaggor).

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Se [snabbstarten prova innehållsrekomtorn på webben](quick-start.md) för instruktioner om hur du registrerar dig för granskningsverktyget för [innehållsmoderatorer](https://contentmoderator.cognitive.microsoft.com/) och skapar ett granskningsteam. Anteckna värdet för **team-ID:t** på sidan **Autentiseringsuppgifter**.

## <a name="create-custom-moderation-tags"></a>Skapa anpassade modereringstaggar

Skapa sedan anpassade taggar i granskningsverktyget (se artikeln [Taggar](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) om du behöver hjälp med den här processen). I det här fallet ska vi lägga till följande taggar: **kändisar**, **USA**, **flagga**, **leksak** och **penna**. Inte alla taggar behöver vara detekterbara kategorier i Computer Vision (som **kändis);** Du kan lägga till egna anpassade taggar så länge du tränar klassificeraren Custom Vision för att upptäcka dem senare.

![Konfigurera anpassade taggar](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installerat**, sedan **Visual C#** och välj **Konsolapp (.NET Framework)**.
1. Ge programmet namnet **EcommerceModeration** och klicka sedan på **OK**.
1. Om du lägger till det här projektet i en befintlig lösning väljer du det här projektet som enda startprojekt.

Den här självstudien belyser koden som är central för projektet, men den täcker inte alla kodrader. Kopiera hela innehållet i _Program.cs_ från exempelprojektet ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) till filen _Program.cs_ för det nya projektet. Gå sedan igenom avsnitten nedan om du vill veta mer om hur projektet fungerar och hur du använder det själv.

## <a name="define-api-keys-and-endpoints"></a>Definiera API-nycklar och slutpunkter

Den här självstudien använder tre kognitiva tjänster. Därför krävs det tre motsvarande nycklar och API-slutpunkter. Se följande fält i klassen **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Du måste uppdatera fälten `___Key` med värdena för dina prenumerationsnycklar `___Uri` och du måste ändra fälten till rätt slutpunktsadresser (du får nyckeln Anpassad syn och slutpunkten senare). Du hittar dessa värden på **snabbstartsflikarna** för varje Azure-resurs. Fyll i `YOURTEAMID`-delen av fältet `ReviewUri` med ID:t för granskningsteamet som du skapade tidigare. Du fyller i den sista `CustomVisionUri` delen av fältet senare.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Primära metodanrop

Se följande kod i **Main**-metoden, som loopar igenom en lista över webbadresser till bilder. Den analyserar varje bild med de tre olika tjänsterna, registrerar de tillämpade taggarna i matrisen **ReviewTags** och skapar sedan en recension för mänskliga moderatorer genom att skicka bilderna till Content Moderator Review Tool. Du utforskar dessa metoder i följande avsnitt. Om du vill kan du styra vilka bilder som ska granskas med hjälp av arrayen **ReviewTags** i en villkorssats för att kontrollera vilka taggar som har använts.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy-metod

Se metoden **EvaluateAdultRacy** i klassen **Program**. Den här metoden tar en bild-URL och en matris med nyckel/värde-par som parametrar. Den anropar Content Moderators bild-API (med REST) för att få poängen för vuxet och vågat innehåll på bilden. Om poängen för någon av dem är större än 0,4 (intervallet är mellan 0 och 1) ställs motsvarande värde i arrayen **ReviewTags** till **Sant**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Metoden EvaluateComputerVisionTags

Nästa metod tar en bild-URL och din prenumerationsinformation för visuellt innehåll och analyserar bilden för förekomst av kändisar. Om det finns en eller flera kändisar ställs motsvarande värde i matrisen **ReviewTags** in på **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags-metod

Sedan kan du titta på metoden **EvaluateCustomVisionTags** som klassificerar de faktiska produkterna&mdash;i det här fallet flaggor, leksaker och pennor. Följ instruktionerna i [hur du skapar en klassarguide](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) för att bygga din egen anpassade bildklassificerare och upptäcka flaggor, leksaker och pennor (eller vad du väljer som dina egna taggar) i bilder. Du kan använda bilderna i **exempelbildermappen** i [GitHub-repoen](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) för att snabbt träna några av kategorierna i det här exemplet.

![Custom Vision-webbsida med inlärningsbilder av pennor, leksaker och flaggor](images/tutorial-ecommerce-custom-vision.PNG)

När du har tränat klassificeraren får du url:en för förutsägelsenyckel och för förutsägelseslutpunktsadressen (se [Hämta URL:en och förutsägelsenyckeln](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) om du behöver hjälp med att hämta dem) och tilldela dessa värden till dina `CustomVisionKey` och `CustomVisionUri` fält. Metoden använder dessa värden för att fråga klassificeraren. Om klassificeraren hittar en eller flera av de anpassade taggarna i bilden ställer den här metoden in de motsvarande värdena i matrisen **ReviewTags** på **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Skapa granskningar för granskningsverktyg

I föregående avsnitt har du utforskat hur appen söker igenom inkommande bilder efter barn- och racyinnehåll (Content Moderator), kändisar (Datorseende) och flera andra objekt (Anpassad vision). Se sedan metoden **CreateReview,** som laddar upp bilderna med alla tillämpade taggar (skickas in som _metadata)_ till granskningsverktyget för innehållsmodererator.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Bilderna visas på granskningsfliken i [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/).

![Skärmbild av Content Moderator-granskningsverktyget med flera bilder och deras markerade taggar](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Skicka en lista över bilder för testning

Som du ser i metoden **Main** söker det här programmet efter en ”C:Test”-katalog med en _Urls.txt_ -fil som innehåller en lista över bild-URL:er. Skapa den här filen och katalogen eller ändra sökvägen så att den pekar på textfilen. Fyll sedan i den här filen med webbadresserna till bilder som du vill testa.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Köra programmet

Om du har följt alla ovanstående steg ska programmet bearbeta varje bild (fråga alla tre tjänsterna efter deras relevanta taggar) och sedan ladda upp bilderna med tagginformation till granskningsverktyget för innehållsmoderator.

## <a name="next-steps"></a>Nästa steg

I den här självstudien ställer du in ett program för att analysera produktbilder, tagga dem efter produkttyp och låta ett granskningsteam fatta välgrundade beslut om innehållsmoderering. Härnäst får du mer information om bildmoderering.

> [!div class="nextstepaction"]
> [Granska modererade bilder](./review-tool-user-guide/review-moderated-images.md)
