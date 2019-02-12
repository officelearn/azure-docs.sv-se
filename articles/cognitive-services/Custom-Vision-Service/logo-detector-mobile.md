---
title: 'Självstudier: Använda anpassad logotypdetektor för att känna igen Azure-tjänster – Custom Vision'
titlesuffix: Azure Cognitive Services
description: I de här självstudierna ska du gå igenom en exempelapp som använder Azure Custom Vision som en del av ett scenario för identifiering av logotyper. Lär dig hur Custom Vision ska användas med andra komponenter för att leverera ett program för slutpunkt till slutpunkt.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pafarley
ms.openlocfilehash: 520e1981544f6ad0a6f1412a47dc8ea50fdd53a5
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771469"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Självstudier: Identifiera Azure-tjänstens logotyper i bilder med kameran

I de här självstudierna ska du gå igenom en exempelapp som använder Azure Custom Vision som en del av ett större scenario. Appen AI Visual Provision, en Xamarin.Forms-app för mobila plattformar, analyserar kamerabilder av Azure-tjänstens logotyper och distribuerar sedan de faktiska tjänsterna till användarens Azure-konto. Här kommer du lära dig hur använder Custom Vision tillsammans med andra komponenter för att leverera ett användbart slutpunkt till slutpunkt-program. Du kanske vill köra hela appen själv, eller så kan du bara slutföra Custom Vision-delen av installationen och utforska hur appen använder den.

De här självstudierna visar hur du:

> [!div class="checklist"]
> - Skapa en anpassad objektdetektor för att identifiera Azure-tjänstens logotyper
> - Anslut appen till Azure-tjänsten Visuellt innehåll och Custom Vision
> - Skapa ett konto för Azure-tjänstens huvudnamn för att distribuera Azure-tjänster från appen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- Xamarin-arbetsbelastning för Visual Studio (se [Installera Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- En iOS- eller Android-emulator för Visual Studio
- [Kommandoradsgränssnitt för Azure (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (valfritt)

## <a name="get-the-source-code"></a>Hämta källkoden

Om du vill använda webbappen som tillhandahållas kan du klona eller ladda ned appens källkod från lagringsplatsen [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) på GitHub. Öppna filen *Source/VisualProvision.sln* i Visual Studio. Vid ett senare tillfälle gör du nödvändiga ändringar av några av projektfilerna för att kunna köra appen.

## <a name="create-an-object-detector"></a>Skapa en objektidentifierare

Logga in på [Custom Visions webbplats](https://customvision.ai/) och skapa ett nytt projekt. Ange ett objektidentifieringprojekt och använd logotyp-domänen. Detta gör att tjänsten kan använda en algoritm som är optimerad för identifiering av logotyp. 

![nytt projektdialogförnster på Custom Vision-webbplatsen i webbläsaren Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Därefter behöver du träna algoritmen för logotypidentifiering genom att ladda upp bilder på Azure-tjänstens logotyper och tagga dem manuellt. AIVisualProvision-lagringsplatsen innehåller en uppsättning inlärningsbilder som du kan använda. På webbplatsen väljer du knappen **Lägg till bilder** på fliken **Training Images** (Inlärningsbilder) och går sedan till mappen **Documents/Images/Training_DataSet** på lagringsplatsen. Du måste manuellt tagga logotyperna i varje bild, så om du bara testar det här projektet kan du ladda upp en delmängd av bilderna. Du måste minst överföra 15 instanser av varje tagg som du planerar att använda.

När du har laddat upp inlärningsbilderna väljer du den första på skärmen. Då öppnas taggningsfönstret. Rita rutor och tilldela taggar för en logotyp i varje bild. 

![bild av logotyper med taggar som tillämpas på Custom Vision-webbplats](media/azure-logo-tutorial/tag-logos.png)

Appen är konfigurerad för att fungera med specifika taggsträngar. Se definitionerna i filen *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

När du har taggat en bild navigerar du till höger för att tagga nästa. Stäng taggningsfönstret när du är klar.

## <a name="train-the-object-detector"></a>Träna objektidentifieraren

I den vänstra rutan ställer du in reglaget **Taggar** på **Tagged** (Taggad), så bör du se alla bilder. Klicka sedan på den gröna knappen överst på sidan för att träna modellen. Detta lär algoritmen att identifiera samma taggar i nya avbildningar. Det testar också modellen på några av dina befintliga bilder för att generera noggrannhetspoäng.

![Custom Vision-webbplats, på fliken Training Images (Inlärningsbilder), är knappen Träna inramad](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Hämta URL:en för förutsägelse

När din modell tränas är du redo att integrera den med din app. Om du vill göra detta måste behöver du hämta slutpunkts-URL:en (adressen för din modell som ska skicka frågor till appen) och förutsägelsenyckeln (för att ge appen åtkomst till förutsägelsebegäranden). På fliken **Prestanda** klickar du på knappen **Prediction URL** (Förutsägelse-URL) längst upp på sidan.

![Custom Vision-webbplats med en förutsägelse-API-skärm, som visar en URL-adress och api-nyckeln](media/azure-logo-tutorial/cusvis-endpoint.png)

Kopiera bildfilens URL och `Prediction-key`-värdet till lämpliga fält filen *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Granska Custom Vision-användning

Öppna filen *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* för att se hur din Custom Vision-nyckel och slutpunkts-URL används i appen. Metoden **PredictImageContentsAsync** tar en byteström av en bildfil tillsammans med en annuleringstoken (för hantering av asynkron aktivitet), anropar förutsägelse-API:t för Custom Vision och returnerar förutsägelseresultatet. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Resultatet får formen av en **PredictionResult**-instans, som innehåller en lista över **förutsägelseinstanser**. En **förutsägelse** innehåller en identifierad tagg och dess avgränsningsfältsplats i bilden.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Om du vill lära dig mer om hur appen hanterar dessa data kan du starta vid metoden **GetResourcesAsync** som definieras i filen *Source/VisualProvision/Services/Recognition/RecognitionService.cs*. 

## <a name="add-computer-vision"></a>Lägga till Visuellt innehåll

Custom Vision-delen av kursen är klar, men om du vill köra appen behöver du integrera tjänsten för visuellt innehåll. Appen använder Visuellt innehålls text funktion för textigenkänning för att komplettera logotypidentifieringsprocessen. En Azure-logotyp kan identifieras av utseendet _eller_ genom texten som är tryckt nära den. Till skillnad från Custom Vision-modeller är Visuellt innehåll tränat i förväg för att utföra vissa åtgärder på bilder eller videor.

Prenumerera bara på tjänsten Visuellt innehåll för att hämta en nyckel och slutpunkts-URL. Se [Skaffa prenumerationsnycklar](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe) om du behöver hjälp med det här steget.

![tjänsten för visuellt innehåll i Azure-portalen med snabbstartsmenyn vald; en länk för nycklar är markerad, och även API:ets slutpunkts-URL](media/azure-logo-tutorial/comvis-keys.png)

Öppna sedan filen *Source\VisualProvision\AppSettings.cs* och fyll i variablerna `ComputerVisionEndpoint` och `ComputerVisionKey` med rätt värden.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]


## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Appen kräver ett Azure-konto för tjänstens huvudnamn för att distribuera tjänster till din Azure-prenumeration. Med tjänstens huvudnamn kan du delegera specifika behörigheter för en app med rollbaserad åtkomstkontroll. Se [guiden för tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals) om du vill veta mer.

Du kan skapa ett huvudnamn för tjänsten med Azure Cloud Shell eller Azure CLI (enligt följande). Först loggar du in och väljer den prenumeration som du vill använda.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Skapa sedan tjänstens huvudnamn (observera att det kan ta lite tid att slutföra).

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Vid slutförande bör du se följande JSON-utdata som innehåller autentiseringsuppgifterna som krävs.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```
Anteckna värdena `clientId` och `tenantId`. Lägg till dem i rätt fält i filen *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Kör appen

Nu har du gett appen åtkomst till:
* en tränad Custom Vision-modell
* tjänsten visuellt innehåll
* ett huvudnamn för tjänsten 

Följ dessa steg om du vill köra appen:

1. Välj antingen projektet VisualProvision.Android eller VisualProvision.iOS i Visual Studio Solution Explorer och välj en motsvarande emulator eller ansluten mobil enhet från den nedrullningsbara menyn i huvudverktygsfältet (observera att du behöver en MacOS-enhet för att köra en iOS-emulator). Kör sedan appen.

1. På den första skärmen som läses in anger du klient-ID för tjänstens huvudnamn, klient-ID och lösenord. Klicka på knappen för att **logga in**.

    > [!NOTE]
    > På vissa emulatorer kanske **inloggningsknappen** inte aktiveras i det här steget. Om det händer kan du stoppa appen, öppna filen _Source/VisualProvision/Pages/LoginPage.xaml_, leta rätt på `Button`-elementet märkt med LOGIN BUTTON (INLOGGNINGSKNAPPEN) och ta bort raden:
      ```xaml
      IsEnabled="{Binding IsValid}"
      ```
    
    > Kör sedan appen igen.

    ![appskärmen med fält för autentiseringsuppgifter för tjänstens huvudnamn](media/azure-logo-tutorial/app-credentials.png)

1. På nästa skärm väljer du din Azure-prenumeration från den nedrullningsbara menyn (den bör innehålla alla prenumerationer som tjänstens huvudnamn har åtkomst till). Klicka på knappen **Fortsätt**.

    ![appskärmen med en listruta för mål-Azure-prenumeration](media/azure-logo-tutorial/app-az-subscription.png)

    I det här läget kan appen uppmana dig att ge enheterna åtkomst till kamera och fotolagring. Godkänn dessa behörigheter.

1. Därefter aktiveras kameran på enheten. Ta ett foto av en av Azure-tjänstens logotyper du har lärt dig. En distributionsdialogruta ber dig att välja en region och resursgrupp för de nya tjänsterna (som du skulle göra om du distribuerar dem från Azure-portalen). 

    ![en smartphonekameraskärm med två pappersurklipp för Azure-logotyper i vyn](media/azure-logo-tutorial/app-camera-capture.png)

    ![en appskärmen med fält för distributionsregion och resursgruppost](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Rensa resurser 

Om du har följt alla steg i det här scenariot och använt appen för att distribuera Azure-tjänster till ditt konto kan du gå till [Azure-portalen](https://ms.portal.azure.com/) när du är klar och avbryta de tjänster som du inte vill använda.

Om du planerar att skapa egna objektidentifieringsprojekt med Custom Vision i framtiden kanske du vill ta bort logotypidentifieringsprojektet som du skapade i den här självstudien. Du kan skapa två Custom Vision-projekt med en kostnadsfri utvärderingsversion. På [Custom Vision-webbplatsen](https://customvision.ai) går du till **Projects** (Projekt) och väljer papperskorgen under **My New Project** (Mitt nya projekt).

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du konfigurera och utforska en komplett Xamarin.Forms-app som använder Custom Vision Service för att identifiera logotyper i mobila kamerabilder. Läs sedan om metodtips för att skapa en Custom Vision-modell, så att du kan göra det kraftfullt och korrekt när du skapar en modell för din egen app.

> [!div class="nextstepaction"]
> [Förbättra din klassificerare](getting-started-improving-your-classifier.md)