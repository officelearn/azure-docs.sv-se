---
title: 'Självstudier: Moderera produktbilder för e-handel – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Konfigurera ett program att analysera och klassificera produktbilder med angivna etiketter (med Azure för visuellt innehåll och Custom Vision). Tagga stötande bilder granskas ytterligare (med Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: ec17f9f0206ef639bd47d694880c064a012ea1cf
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604198"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Självstudier: Moderera produktbilder för e-handel med Azure Content Moderator

I de här självstudierna får du lära dig hur du använder Azure Cognitive Services, inklusive Content Moderator för att klassificera och måttlig produktbilder i ett scenario med e-handel. Du använder visuellt innehåll och anpassad visuellt innehåll för att lägga till taggar (etiketter) i bilder och sedan skapar du en granskning för team som kombinerar Content Moderator machine-learning-baserade tekniker med mänsklig granskning team att tillhandahålla ett intelligent moderering system.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera dig för Content Moderator och skapa ett granskningsteam.
> * Använd Content Moderators bild-API för att söka efter potentiellt vuxet eller vågat innehåll.
> * Använd tjänsten för visuellt innehåll för att söka efter kändisinnehåll (eller andra identifierbara taggar för visuellt innehåll).
> * Använd tjänsten Custom Vision om du vill söka efter flaggor, leksaker och pennor (eller andra anpassade taggar).
> * Presentera kombinerade sökresultat för mänsklig granskning och slutgiltigt beslutsfattande.

Den fullständiga exempelkoden är tillgänglig på lagringsplatsen [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) på GitHub.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator-tjänsten och få din nyckel.
- En prenumerationsnyckel för visuellt innehåll (samma instruktioner som ovan).
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).
- En uppsättning bilder för varje etikett som klassificeraren för Custom Vision ska använda (i det här fallet leksaker, pennor och amerikanska flaggor).

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Referera till den [försök Content Moderator på webben](quick-start.md) Snabbstart för instruktioner om hur du registrerar dig för den [Content Moderator granskar verktyget](https://contentmoderator.cognitive.microsoft.com/) och skapa en granskningsteam. Anteckna värdet för **team-ID:t** på sidan **Autentiseringsuppgifter**.

## <a name="create-custom-moderation-tags"></a>Skapa anpassade modereringstaggar

Därefter skapar du anpassade taggar i granskningsverktyget (se den [taggar](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) artikel om du behöver hjälp med den här processen). I det här fallet ska vi lägga till följande taggar: **kändisar**, **USA**, **flagga**, **leksak** och **penna**. Inte alla taggar måste vara flashminnet kategorier i visuellt innehåll (t.ex. **kändisar**); du kan lägga till dina egna anpassade taggar som du tränar Custom Vision-klassificerare att identifiera dem senare.

![Konfigurera anpassade taggar](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt

1. Öppna dialogrutan New Project (Nytt projekt) i Visual Studio. Expandera **Installerat**, sedan **Visual C#** och välj **Konsolapp (.NET Framework)** .
1. Ge programmet namnet **EcommerceModeration** och klicka sedan på **OK**.
1. Om du lägger till det här projektet i en befintlig lösning, väljer du det här projektet som enda Startprojekt.

Den här självstudien visar den kod som är centrala för projektet, men det kommer inte täcker varje rad med kod. Kopiera hela innehållet i _Program.cs_ från exempelprojektet ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) till filen _Program.cs_ för det nya projektet. Gå sedan igenom avsnitten nedan om du vill veta mer om hur projektet fungerar och hur du använder det själv.

## <a name="define-api-keys-and-endpoints"></a>Definiera API-nycklar och slutpunkter

Den här självstudien använder tre kognitiva tjänster. Därför kräver tre motsvarande nycklar och API-slutpunkter. Se följande fält i klassen **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Måste du uppdatera den `___Key` fält med värdena för dina prenumerationsnycklar (du får den `CustomVisionKey` senare), och du kan behöva ändra den `___Uri` fält så att de innehåller rätt region-identifierare. Fyll i `YOURTEAMID`-delen av fältet `ReviewUri` med ID:t för granskningsteamet som du skapade tidigare. Du måste fylla i den sista delen av den `CustomVisionUri` fältet vid ett senare tillfälle.

## <a name="primary-method-calls"></a>Primära metodanrop

Se följande kod i **Main**-metoden, som loopar igenom en lista över webbadresser till bilder. Den analyserar varje avbildning med tre olika tjänster, registrerar tillämpade taggar i den **ReviewTags** matris och skapar sedan en granskning för mänskliga moderatorer genom att skicka bilderna för Content Moderator granskar verktyget. Du utforskar dessa metoder i följande avsnitt. Om du vill kan du kan styra vilka bilder som ska skickas till granska, med hjälp av den **ReviewTags** matris i en villkorlig instruktionen för att kontrollera vilka taggar har tillämpats.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy-metod

Se metoden **EvaluateAdultRacy** i klassen **Program**. Den här metoden tar en bild-URL och en matris med nyckel/värde-par som parametrar. Den anropar Content Moderators bild-API (med REST) för att få poängen för vuxet och vågat innehåll på bilden. Om poängen för antingen är större än 0.4 (intervallet är mellan 0 och 1), anger motsvarande värde den **ReviewTags** matris att **SANT**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags method

Nästa metod tar en bild-URL och din prenumerationsinformation för visuellt innehåll och analyserar bilden för förekomst av kändisar. Om det finns en eller flera kändisar ställs motsvarande värde i matrisen **ReviewTags** in på **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags-metod

Sedan kan du titta på metoden **EvaluateCustomVisionTags** som klassificerar de faktiska produkterna&mdash;i det här fallet flaggor, leksaker och pennor. Följ instruktionerna i den [hur du skapar en klassificerare](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) guide för att skapa en egen anpassad avbildning klassificerare och identifierar flaggor, toys, och pennor (eller som du valde som din anpassade taggar) i bilder. Du kan använda bilder i den **Exempelbilder** mappen för den [GitHub-lagringsplatsen](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) att träna snabbt några av kategorierna i det här exemplet.

![Custom Vision-webbsida med inlärningsbilder av pennor, leksaker och flaggor](images/tutorial-ecommerce-custom-vision.PNG)

När du har tränat din klassificerare, hitta förutsägelse nyckel och förutsägelse slutpunkts-URL (se [hämta nyckel för URL- och förutsägelsetransaktioner](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) om du behöver hjälp med att hämta dem), och tilldela dessa värden till din `CustomVisionKey` och `CustomVisionUri` fälten respektive. Metoden använder dessa värden för att fråga klassificeraren. Om klassificeraren hittar en eller flera av de anpassade taggarna i bilden ställer den här metoden in de motsvarande värdena i matrisen **ReviewTags** på **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Skapa granskningar för granskningsverktyg

I föregående avsnitt utforskade du hur appen söker igenom inkommande bilder för vuxet eller olämpligt innehåll (Content Moderator), kändisar (visuellt) och olika andra objekt (Custom Vision). Sedan kan du se den **CreateReview** metoden som överför avbildningar med alla tillämpade taggar (överförda som _Metadata_) till Content Moderator-Granskningsverktyget.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Bilderna visas på granskningsfliken i [Content Moderator-granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/).

![Skärmbild av Content Moderator-granskningsverktyget med flera bilder och deras markerade taggar](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Skicka en lista över bilder för testning

Som du ser i metoden **Main** söker det här programmet efter en ”C:Test”-katalog med en _Urls.txt_ -fil som innehåller en lista över bild-URL:er. Skapa den här fil- och eller ändra sökvägen så att den pekar till textfilen. Fyll sedan i den här filen med URL: er med avbildningar som du vill testa.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Köra programmet

Om du har följt alla steg ovan, bör programmet bearbeta varje avbildning (frågor på alla tre tjänster för relevanta taggar) och sedan ladda upp bilder med tagginformation till Content Moderator-Granskningsverktyget.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du ett program för att analysera produktbilder, tagga dem produkttyp och tillåta en granskningsteam att fatta välgrundade beslut om innehållsmoderering. Härnäst får du mer information om bildmoderering.

> [!div class="nextstepaction"]
> [Granska modererade bilder](./review-tool-user-guide/review-moderated-images.md)
