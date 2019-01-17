---
title: 'Självstudier: Moderera produktbilder för e-handel – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Konfigurera ett program för att analysera och klassificera produktbilder med angivna etiketter (med Visuellt innehåll i Azure och Custom Vision) och tagga stötande bilder som ska granskas ytterligare (med Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: e490e51d0eb3e1c4534bed887508474ce3ffcb22
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259333"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Självstudier: Moderera produktbilder för e-handel med Azure Content Moderator

I den här självstudien får du lära dig att använda Azure Cognitive Services, inklusive Content Moderator för att effektivt klassificera och moderera produktbilder i ett scenario med e-handel. Du kommer att använda Visuellt innehåll och Custom Vision för att tillämpa olika taggar (etiketter) på bilder, och därefter skapar du en teamgranskning, som kombinerar Content Moderators maskininlärningsbaserade tekniker med mänskliga granskningsteam för att tillhandahålla ett intelligent modereringssystem.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera dig för Content Moderator och skapa ett granskningsteam.
> * Använd Content Moderators bild-API för att söka efter potentiellt vuxet eller vågat innehåll.
> * Använd tjänsten för visuellt innehåll för att söka efter kändisinnehåll (eller andra identifierbara taggar för visuellt innehåll).
> * Använd tjänsten Custom Vision om du vill söka efter flaggor, leksaker och pennor (eller andra anpassade taggar).
> * Presentera kombinerade sökresultat för mänsklig granskning och slutgiltigt beslutsfattande.

Den fullständiga exempelkoden är tillgänglig på lagringsplatsen [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) på GitHub.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator-tjänsten och få din nyckel.
- En prenumerationsnyckel för visuellt innehåll (samma instruktioner som ovan).
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).
- En uppsättning bilder för varje etikett som klassificeraren för Custom Vision ska använda (i det här fallet leksaker, pennor och amerikanska flaggor).

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Referera till snabbstarten [Get familiar with Content Moderator](quick-start.md) (Bekanta dig med Content Moderator) för instruktioner om hur du registrerar dig för [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) och skapar ett granskningsteam. Anteckna värdet för **team-ID:t** på sidan **Autentiseringsuppgifter**.

## <a name="create-custom-moderation-tags"></a>Skapa anpassade modereringstaggar

Därefter skapar du anpassade taggar i granskningsverktyget (läs artikeln [Taggar](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) om du behöver hjälp med den här processen). I det här fallet ska vi lägga till följande taggar: **kändisar**, **USA**, **flagga**, **leksak** och **penna**. Observera att alla taggar inte måste vara identifierbara kategorier i visuellt innehåll (t.ex. **kändisar**). Du kan lägga till dina egna anpassade taggar när du tränar Custom Vision-klassificeraren för att identifiera dem senare.

![Konfigurera anpassade taggar](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installerat**, sedan **Visual C#** och välj **Konsolapp (.NET Framework)**.
1. Ge programmet namnet **EcommerceModeration** och klicka sedan på **OK**.
1. Om du lägger till det här projektet i en befintlig lösning väljer du det här projektet som det enda startprojektet.

Den här självstudien fokuserar på koden som är central för projektet, men den tar inte upp alla kodrader som behövs. Kopiera hela innehållet i _Program.cs_ från exempelprojektet ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) till filen _Program.cs_ för det nya projektet. Gå sedan igenom avsnitten nedan om du vill veta mer om hur projektet fungerar och hur du använder det själv.

## <a name="define-api-keys-and-endpoints"></a>Definiera API-nycklar och slutpunkter

Som nämnts ovan använder den här självstudien tre kognitiva tjänster. Därför krävs tre motsvarande nycklar och API-slutpunkter. Se följande fält i klassen **Program**: 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Du måste uppdatera `___Key`-fältet med värdet för din prenumerationsnyckel (du får `CustomVisionKey` senare), och du kanske måste ändra `___Uri`-fälten så att de innehåller rätt regionsidentifierare. Fyll i `YOURTEAMID`-delen av fältet `ReviewUri` med ID:t för granskningsteamet som du skapade tidigare. Du ska fylla i den sista delen av fältet `CustomVisionUri` vid ett senare tillfälle.

## <a name="primary-method-calls"></a>Primära metodanrop

Se följande kod i **Main**-metoden, som loopar igenom en lista över webbadresser till bilder. Den analyserar varje bild med tre olika tjänster, registrerar tillämpade taggar i matrisen **ReviewTags** och skapar sedan en granskning för mänskliga moderatorer (skickar bilder för Content Moderator-granskningsverktyget). Du utforskar dessa metoder i följande avsnitt. Observera att här, om du vill, kan du kan styra vilka bilder som ska skickas till granskning med hjälp av matrisen **ReviewTags** i en villkorlig instruktion för att kontrollera vilka taggar har tillämpats.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy-metod

Se metoden **EvaluateAdultRacy** i klassen **Program**. Den här metoden tar en bild-URL och en matris med nyckel/värde-par som parametrar. Den anropar Content Moderators bild-API (med REST) för att få poängen för vuxet och vågat innehåll på bilden. Om poängen är högre än 0,4 (intervallet är från 0 till 1) anger den motsvarande värde i matrisen **ReviewTags** till **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags-metod

Nästa metod tar en bild-URL och din prenumerationsinformation för visuellt innehåll och analyserar bilden för förekomst av kändisar. Om det finns en eller flera kändisar ställs motsvarande värde i matrisen **ReviewTags** in på **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags-metod

Sedan kan du titta på metoden **EvaluateCustomVisionTags** som klassificerar de faktiska produkterna&mdash;i det här fallet flaggor, leksaker och pennor. Följ instruktionerna i guiden [Skapa en klassificerare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) för att skapa en egen anpassad bildklassificerare för att detektera förekomsten av flaggor, leksaker och pennor (eller det du valde som dina anpassade taggar) i bilder.

![Custom Vision-webbsida med inlärningsbilder av pennor, leksaker och flaggor](images/tutorial-ecommerce-custom-vision.PNG)

När du har tränat din klassificerare hämtar du förutsägelsenyckeln och slutpunkts-URL:en för förutsägelse (se [Hämta URL och förutsägelsenyckel](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) om du behöver hjälp med att hämta dem), och tilldela dessa värden till dina fält `CustomVisionKey` respektive `CustomVisionUri`. Metoden använder dessa värden för att fråga klassificeraren. Om klassificeraren hittar en eller flera av de anpassade taggarna i bilden ställer den här metoden in de motsvarande värdena i matrisen **ReviewTags** på **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Skapa granskningar för granskningsverktyg

I föregående avsnitt noterade du vilka metoder som söker igenom inkommande bilder efter vuxet och vågat innehåll (Content Moderator), kändisar (Visuellt innehåll) och olika andra objekt (Custom Vision). Se nu att metoden **CreateReview** överför bilderna, med alla tillämpade taggar (överförda som _Metadata_), till Content Moderator-granskningsverktyget så att de blir tillgängliga för mänsklig granskning. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Bilderna visas på granskningsfliken i [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/).

![Skärmbild av Content Moderator-granskningsverktyget med flera bilder och deras markerade taggar](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Skicka en lista över bilder för testning

Som du ser i metoden **Main** söker det här programmet efter en ”C:Test”-katalog med en _Urls.txt_ -fil som innehåller en lista över bild-URL:er. Skapa sådan fil och katalog, eller ändra sökvägen så att den pekar på textfilen och fyll i den här filen med URL:er till bilder som du vill testa.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Köra programmet

Om du har följt alla steg ovan bör programmet bearbeta varje bild (och fråga alla tre tjänster för relevanta taggar) och sedan ladda upp bilder med tagginformation till Content Moderator-granskningsverktyget.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du ett program för att analysera produktbilder i syfte att tagga dem via produkttyp och låta ett granskningsteam fatta välgrundade beslut om innehållsmoderering. Härnäst får du mer information om bildmoderering.

> [!div class="nextstepaction"]
> [Granska modererade bilder](./review-tool-user-guide/review-moderated-images.md)