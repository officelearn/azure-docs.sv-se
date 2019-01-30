---
title: 'Självstudier: Skapa en app för bearbetning av avbildning-C#'
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Utforska en grundläggande Windows-app som använder den API för visuellt innehåll i Microsoft Cognitive Services. Utför OCR, skapa miniatyrer och arbeta med visuella funktioner i en bild.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b944325287593a028d28545731370dc9ea6a5ac2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224929"
---
# <a name="tutorial-build-an-image-processing-app---c35"></a>Självstudier: Skapa en avbildning som app - C bearbetas&#35;

Utforska ett grundläggande Windows-program som använder visuellt för att utföra optisk teckenläsning (OCR), skapa smart beskärs miniatyrer, plus identifiera, kategorisera, tagg och beskriver visuella funktioner, inklusive ansikten, i en bild. Med exemplet nedan kan du skicka en bild-URL eller en lokalt lagrad fil. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app för Windows med hjälp av API för visuellt innehåll och Windows Presentation Foundation (WPF), en del av .NET Framework.

> [!div class="checklist"]
> * Hämta exempelappen från GitHub
> * Öppna och skapa exempelappen i Visual Studio
> * Kör exempelappen och interagera med den att utföra olika scenarier
> * Utforska de olika scenarier som ingår i exempelappen

## <a name="prerequisites"></a>Förutsättningar

Se till att du har uppfyllt följande krav innan du börjar utforska exempelappen:

* Du måste ha [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) eller senare.
* Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Skaffa prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-the-sample-app"></a>Hämta exempelappen

Exempelapp för visuellt innehåll är tillgängligt på GitHub från den `Microsoft/Cognitive-Vision-Windows` lagringsplats. Den här lagringsplatsen innehåller också de `Microsoft/Cognitive-Common-Windows` databasen som en Git-submodule. Du kan rekursivt klona den här databasen, bland annat submodule, antingen genom att använda den `git clone --recurse-submodules` kommandot från kommandoraden eller med hjälp av GitHub-skrivbord.

Till exempel att rekursivt klona databasen för exempelappen för visuellt innehåll från en kommandotolk, kör du följande kommando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Hämta inte den här databasen som en ZIP. Git omfattar inte undermoduler när du laddar ned en databas som en ZIP.

### <a name="get-optional-sample-images"></a>Hämta valfritt Exempelbilder

Alternativt kan du använda exemplet avbildningarna som ingår i [ansikte](../../Face/Overview.md) exempelapp som finns på GitHub från den `Microsoft/Cognitive-Face-Windows` lagringsplats. Exemplet appen innehåller en mapp, `/Data`, som innehåller flera avbildningar av personer. Du kan rekursivt klona den här lagringsplatsen, de metoder som beskrivs för exempelapp för visuellt innehåll.

Till exempel att rekursivt klona databasen för exempelappen ansikte från en kommandotolk, kör du följande kommando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Öppna och skapa exempelappen i Visual Studio

Du måste först skapa exempelappen så att Visual Studio kan lösa beroenden, innan du kan köra eller utforska exempelappen. Om du vill öppna och skapa exempelappen, gör du följande:

1. Öppna Visual Studio-lösningsfilen `/Sample-WPF/VisionAPI-WPF-Samples.sln`, i Visual Studio.
1. Se till att Visual Studio-lösningen innehåller två projekt:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Om SampleUserControlLibrary projektet är tillgänglig, bekräfta att du har klonat rekursivt den `Microsoft/Cognitive-Vision-Windows` lagringsplats.
1. I Visual Studio, antingen tryck på Ctrl + Skift + B eller välj **skapa** menyfliksområdet-menyn och välj sedan **skapa lösning** att skapa lösningen.

## <a name="run-and-interact-with-the-sample-app"></a>Kör och interagera med exempelappen

Du kan köra exempelappen, om du vill se hur det interagerar med dig och med klientbiblioteket för visuellt innehåll när du utför olika uppgifter, till exempel generera miniatyrer eller taggning avbildningar. Om du vill köra och interagera med exempelappen, gör du följande:

1. När versionen har slutförts, antingen trycker **F5** eller välj **felsöka** menyfliksområdet-menyn och välj sedan **Starta felsökning** att köra exempelappen.
1. När exempelappen visas, väljer **nyckel prenumerationshantering** från navigeringsfönstret för att visa sidan prenumerationshantering för nyckeln.
   ![Prenumerationssidan för hantering av nycklar](../Images/Vision_UI_Subscription.PNG)  
1. Ange din prenumerationsnyckel i **Prenumerationsnyckel**.
1. Ange slutpunkts-URL Om du utesluter den `/vision/v1.0`, av visuellt innehåll-resurs för din prenumerationsnyckel i **Endpoint**.  
   Exempel: Om du använder prenumerationsnyckeln från den kostnadsfria utvärderingsversionen av visuellt innehåll, ange följande slutpunkts-URL för regionen västra centrala USA Azure: `https://westcentralus.api.cognitive.microsoft.com`
1. Om du inte vill ange prenumerationsnyckel och slutpunkten URL nästa gång du kör exempelappen, Välj **spara inställningen** att spara prenumeration nyckel och slutpunkt URL: en till din dator. Om du vill ta bort din sparade tidigare prenumeration nyckel och slutpunkt URL väljer **ta bort inställningen**.

   > [!NOTE]
   > Exempelappen använder isolerad lagring och `System.IO.IsolatedStorage`, för att lagra din prenumeration nyckel och slutpunkt-URL.

1. Under **välja ett scenario** i navigeringsfönstret väljer du något av de scenarier som för närvarande ingår i exempelappen:  

   | Scenario | Beskrivning |
   |----------|-------------|
   |Analysera bild | Använder den [analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) åtgärd för att analysera en lokal eller fjärransluten avbildning. Du kan välja vilka visuella funktioner och språk för analys och se både avbildningen och resultaten.  |
   |Analysera avbildning med domänmodell | Använder den [lista domänspecifika modeller](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) åtgärden att lista domänmodeller där du kan välja, och [identifiera domänen specifika innehåll](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) åtgärd för att analysera en lokal eller fjärransluten avbildningen med hjälp av den valda domänmodell. Du kan också välja önskat språk för analys. |
   |Beskriv avbildningen | Använder den [Beskriv avbildningen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) att skapa en läsbar beskrivning av en lokal eller fjärransluten avbildning. Du kan också välja önskat språk för beskrivningen. |
   |Generera taggar | Använder den [tagg bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) åtgärden att tagga visuella funktioner för en lokal eller fjärransluten avbildning. Du kan också välja det språk som används för taggar. |
   |Identifiera Text (OCR) | Använder den [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) åtgärd för att identifiera och extrahera ut text från en avbildning. Du kan antingen välja språket som ska användas eller låta visuellt identifiera språket automatiskt. |
   |Identifiera Text V2 (på engelska) | Använder den [identifiera Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) och [hämta identifiera Text Åtgärdsresultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) åtgärder för att identifiera och extrahera utskrivna eller handskriven text från en avbildning asynkront. |
   |Hämta miniatyrbilden | Använder den [hämta miniatyrbilden](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) åtgärden att skapa en miniatyrbild för en lokal eller fjärransluten avbildning. |

   Följande skärmbild visar sidan tillhandahålls för scenariot analysera avbildningen när du har analyserat en exempelbild.
   ![Skärmbild av sidan analysera bild](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Utforska exempelappen

Visual Studio-lösningen för visuellt innehåll exempelappen innehåller två projekt:

* SampleUserControlLibrary  
  SampleUserControlLibrary projektet innehåller funktioner som delas av flera Cognitive Services-exempel. Projektet innehåller följande:
  * SampleScenarios  
    En UserControl som ger en standardiserad presentationer, till exempel i namnlisten och navigeringsfönstret innehållsrutan för exempel. Visuellt exempelappen använder den här kontrollen i fönstret MainWindow.xaml visar scenario sidor och komma åt information som delas mellan scenarier, till exempel prenumeration nyckel och slutpunkt URL: en.
  * SubscriptionKeyPage  
    En sida som ger en standardiserad layout för att ange en prenumeration nyckel och slutpunkt-URL för exempelappen. Exempelapp för visuellt innehåll använder den här sidan för att hantera prenumerationsnyckeln och slutpunkts-URL som används av sidorna för scenariot.
  * VideoResultControl  
    En UserControl som ger en standardiserad presentation video information. Visuellt exempelappen använder inte den här kontrollen.
* VisionAPI-WPF-Samples  
  Huvudsakliga projektet för exempelapp för visuellt innehåll, det här projektet innehåller alla intressanta funktioner för visuellt innehåll. Projektet innehåller följande:
  * AnalyzeInDomainPage.xaml  
    Scenario-sida för att analysera avbildningen med domänmodell scenario.
  * AnalyzeImage.xaml  
    Scenario-sida för att analysera bild-scenario.
  * DescribePage.xaml  
    Sidan scenariot för scenariot beskrivs bild.
  * ImageScenarioPage.cs  
    ImageScenarioPage klassen från vilken alla sidor i exempelappen scenario kommer. Den här klassen hanterar funktioner, t.ex att ange autentiseringsuppgifter och formatering av utdata, delas av alla sidor scenario.
  * MainWindow.xaml  
    Huvudfönstret för exempelappen SampleScenarios kontrollen används för att presentera SubscriptionKeyPage och scenario-sidor.
  * OCRPage.xaml  
    Scenario-sida för att identifiera Text (OCR)-scenario.
  * RecognizeLanguage.cs  
    RecognizeLanguage-klass som innehåller information om språk som stöds av de olika metoderna i exempelappen.
  * TagsPage.xaml  
    Scenario-sida för att generera taggar-scenario.
  * TextRecognitionPage.xaml  
    Scenario-sida för att identifiera Text V2 (engelska) scenariot.
  * ThumbnailPage.xaml  
    Scenario-sida för att hämta miniatyr-scenario.

### <a name="explore-the-sample-code"></a>Utforska exempelkoden

Viktiga delar av exempelkoden är framed med kommentar block som börjar med `KEY SAMPLE CODE STARTS HERE` och sluta med `KEY SAMPLE CODE ENDS HERE`, för att göra det enklare för dig att utforska exempelappen. Dessa viktiga delar av exempelkoden innehåller kod som är mest relevant för att lära dig hur du använder API för visuellt innehåll-klientbiblioteket för att utföra olika uppgifter. Du kan söka efter `KEY SAMPLE CODE STARTS HERE` i Visual Studio för att flytta mellan de mest relevanta avsnitt i koden i exempelappen för visuellt innehåll. 

Till exempel den `UploadAndAnalyzeImageAsync` metod, visas följande och ingår i AnalyzePage.xaml, visar hur du använder klientbiblioteket för att analysera en lokal avbildning genom att aktivera den `ComputerVisionClient.AnalyzeImageInStreamAsync` metoden.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Utforska klientbiblioteket

Det här exempelprogrammet använder klientbiblioteket för API för visuellt innehåll kan en tunn C#-klienten Omslutning för det API för visuellt innehåll i Azure Cognitive Services. Det finns klientbiblioteket från NuGet i den [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) paketet. När du har skapat Visual Studio-program kan hämta klientbiblioteket från dess motsvarande NuGet-paketet. Du kan också visa källkoden för klientbiblioteket i den `/ClientLibrary` mappen för den `Microsoft/Cognitive-Vision-Windows` lagringsplats.

Med klientbiblioteket funktioner fokuserad på den `ComputerVisionClient` klassen i den `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` namnområdet, samtidigt som de modeller som används av den `ComputerVisionClient` klassen när du interagerar med visuellt innehåll finns i den `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` namnområde. I de olika XAML scenario sidorna ingår i exempelappen, hittar du följande `using` direktiv för de namnområden:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Du lär dig mer om de olika metoderna som ingår i den `ComputerVisionClient` klassen när du utforskar de scenarier som ingår i exempelappen för visuellt innehåll.

## <a name="explore-the-analyze-image-scenario"></a>Utforska analysera bild-scenario

Det här scenariot hanteras av sidan AnalyzePage.xaml. Du kan välja vilka visuella funktioner och språk för analys och se både avbildningen och resultaten. Sidan scenariot gör detta genom att använda någon av följande metoder, beroende på bildens källa:

* UploadAndAnalyzeImageAsync  
  Den här metoden används för lokal avbildningar, där avbildningen måste vara kodad som en `Stream` och skickas till visuellt genom att anropa den `ComputerVisionClient.AnalyzeImageInStreamAsync` metoden.
* AnalyzeUrlAsync  
  Den här metoden används för fjärråtkomst-avbildningar, som URL för bilden skickas till visuellt genom att anropa den `ComputerVisionClient.AnalyzeImageAsync` metoden.

Den `UploadAndAnalyzeImageAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Eftersom exempelappen är att analysera en lokal avbildning, har att skicka innehållet i avbildningen till visuellt. Öppnas den lokala filen som anges i `imageFilePath` för läsning som en `Stream`, hämtar sedan de visuella funktioner och språket som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.AnalyzeImageInStreamAsync` metoden och skicka den `Stream` för filen, visuella funktioner och språk, returnerar sedan resultatet som en `ImageAnalysis` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

Den `AnalyzeUrlAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Hämtar de visuella funktioner och språket som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.AnalyzeImageInStreamAsync` metoden anropas, bild-URL, visuella funktioner och språk, returnerar sedan resultatet som en `ImageAnalysis` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Utforska analysera avbildningen med domänmodell scenario

Det här scenariot hanteras av sidan AnalyzeInDomainPage.xaml. Du kan välja en domänmodell som `celebrities` eller `landmarks`, och språk för att utföra en domänspecifika analys av avbildningen och se både avbildningen och resultaten. Sidan scenario använder följande metoder, beroende på bildens källa:

* GetAvailableDomainModelsAsync  
  Den här metoden hämtar listan över tillgängliga domänmodeller från visuellt innehåll och fyller den `_domainModelComboBox` kombinationsrutekontroll på sidan med hjälp av den `ComputerVisionClient.ListModelsAsync` metoden.
* UploadAndAnalyzeInDomainImageAsync  
  Den här metoden används för lokal avbildningar, där avbildningen måste vara kodad som en `Stream` och skickas till visuellt genom att anropa den `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metoden.
* AnalyzeInDomainUrlAsync  
  Den här metoden används för fjärråtkomst-avbildningar, som URL för bilden skickas till visuellt genom att anropa den `ComputerVisionClient.AnalyzeImageByDomainAsync` metoden.

Den `UploadAndAnalyzeInDomainImageAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Eftersom exempelappen är att analysera en lokal avbildning, har att skicka innehållet i avbildningen till visuellt. Öppnas den lokala filen som anges i `imageFilePath` för läsning som en `Stream`, hämtar språk som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metoden och skicka den `Stream` för filen, namnet på domänmodellen och språk, returnerar resultatet som en `DomainModelResults` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

Den `AnalyzeInDomainUrlAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Hämtar det språk som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.AnalyzeImageByDomainAsync` metoden anropas, bild-URL, visuella funktioner och språk, returnerar sedan resultatet som en `DomainModelResults` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

## <a name="explore-the-describe-image-scenario"></a>Utforska scenariot beskrivs bild

Det här scenariot hanteras av sidan DescribePage.xaml. Du kan välja ett språk för att skapa en läsbar beskrivning av avbildningen och se både avbildningen och resultaten. Sidan scenario använder följande metoder, beroende på bildens källa:

* UploadAndDescribeImageAsync  
  Den här metoden används för lokal avbildningar, där avbildningen måste vara kodad som en `Stream` och skickas till visuellt genom att anropa den `ComputerVisionClient.DescribeImageInStreamAsync` metoden.
* DescribeUrlAsync  
  Den här metoden används för fjärråtkomst-avbildningar, som URL för bilden skickas till visuellt genom att anropa den `ComputerVisionClient.DescribeImageAsync` metoden.

Den `UploadAndDescribeImageAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Eftersom exempelappen är att analysera en lokal avbildning, har att skicka innehållet i avbildningen till visuellt. Öppnas den lokala filen som anges i `imageFilePath` för läsning som en `Stream`, hämtar språk som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.DescribeImageInStreamAsync` metoden och skicka den `Stream` för filen, det maximala antalet kandidater (i det här fallet 3) och språket sedan returnerar resultat som en `ImageDescription` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

Den `DescribeUrlAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Hämtar det språk som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.DescribeImageAsync` metoden anropas, bild-URL, det maximala antalet kandidater (i det här fallet 3) och språk, returnerar sedan resultatet som en `ImageDescription` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

## <a name="explore-the-generate-tags-scenario"></a>Utforska Generera taggar-scenario

Det här scenariot hanteras av sidan TagsPage.xaml. Du kan välja ett språk att tagga visuella funktioner för en avbildning och se både avbildningen och resultaten. Sidan scenario använder följande metoder, beroende på bildens källa:

* UploadAndGetTagsForImageAsync  
  Den här metoden används för lokal avbildningar, där avbildningen måste vara kodad som en `Stream` och skickas till visuellt genom att anropa den `ComputerVisionClient.TagImageInStreamAsync` metoden.
* GenerateTagsForUrlAsync  
  Den här metoden används för fjärråtkomst-avbildningar, som URL för bilden skickas till visuellt genom att anropa den `ComputerVisionClient.TagImageAsync` metoden.

Den `UploadAndGetTagsForImageAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Eftersom exempelappen är att analysera en lokal avbildning, har att skicka innehållet i avbildningen till visuellt. Öppnas den lokala filen som anges i `imageFilePath` för läsning som en `Stream`, hämtar språk som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.TagImageInStreamAsync` metoden och skicka den `Stream` för filen och språk, returnerar sedan resultatet som en `TagResult` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

Den `GenerateTagsForUrlAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Hämtar det språk som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.TagImageAsync` metoden anropas, bild-URL och språk, returnerar sedan resultatet som en `TagResult` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Utforska identifiera Text (OCR)-scenario

Det här scenariot hanteras av sidan OCRPage.xaml. Du kan välja ett språk att identifiera och extrahera tryckt text från en avbildning och se både avbildningen och resultaten. Sidan scenario använder följande metoder, beroende på bildens källa:

* UploadAndRecognizeImageAsync  
  Den här metoden används för lokal avbildningar, där avbildningen måste vara kodad som en `Stream` och skickas till visuellt genom att anropa den `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metoden.
* RecognizeUrlAsync  
  Den här metoden används för fjärråtkomst-avbildningar, som URL för bilden skickas till visuellt genom att anropa den `ComputerVisionClient.RecognizePrintedTextAsync` metoden.

Den `UploadAndRecognizeImageAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Eftersom exempelappen är att analysera en lokal avbildning, har att skicka innehållet i avbildningen till visuellt. Öppnas den lokala filen som anges i `imageFilePath` för läsning som en `Stream`, hämtar språk som valts på sidan för scenariot. Anropar den `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metod, som anger att orientering inte har identifierats och skicka den `Stream` för filen och språk, returnerar sedan resultatet som en `OcrResult` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

Den `RecognizeUrlAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Hämtar det språk som valts på sidan för scenariot. Den anropar den `ComputerVisionClient.RecognizePrintedTextAsync` metoden, som anger att orientering inte har identifierats och för att skicka bild-URL och språk, returnerar sedan resultatet som en `OcrResult` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Utforska scenariot identifiera Text V2 (engelska)

Det här scenariot hanteras av sidan TextRecognitionPage.xaml. Du kan välja läge för taligenkänning och ett språk att identifiera och extrahera utskrivna eller handskriven text från en avbildning asynkront och se både avbildningen och resultaten. Sidan scenario använder följande metoder, beroende på bildens källa:

* UploadAndRecognizeImageAsync  
  Den här metoden används för lokal avbildningar, där avbildningen måste vara kodad som en `Stream` och skickas till visuellt genom att anropa den `RecognizeAsync` metoden och skicka ett parametriserade ombud för det `ComputerVisionClient.RecognizeTextInStreamAsync` metod.
* RecognizeUrlAsync  
  Den här metoden används för fjärråtkomst-avbildningar, som URL för bilden skickas till visuellt genom att anropa den `RecognizeAsync` metoden och skicka ett parametriserade ombud för det `ComputerVisionClient.RecognizeTextAsync` metod.
* RecognizeAsync den här metoden hanterar den asynkrona anropas för både den `UploadAndRecognizeImageAsync` och `RecognizeUrlAsync` metoder samt avsökning för resultat genom att anropa den `ComputerVisionClient.GetTextOperationResultAsync` metoden.

Det här scenariot är asynkron, till skillnad från andra scenarier som ingår i exempelappen för visuellt innehåll, eftersom en av metoderna anropas för att starta processen, men en annan metod anropas för att kontrollera status och returnerar resultatet av den här processen. Det logiska flödet i det här scenariot skiljer sig något från som i andra scenarier.

Den `UploadAndRecognizeImageAsync` metoden öppnas den lokala filen som anges i `imageFilePath` för läsning som en `Stream`, anropar sedan den `RecognizeAsync` metoden och skicka:

* Ett lambda-uttryck för parametriserade asynkrona ombud för det `ComputerVisionClient.RecognizeTextInStreamAsync` metoden med den `Stream` för filen och igenkänning av läge som parametrar, i `GetHeadersAsyncFunc`.
* En lambda-uttryck för ett ombud att hämta den `Operation-Location` svarshuvud värde, i `GetOperationUrlFunc`.

Den `RecognizeUrlAsync` metodanrop den `RecognizeAsync` metoden och skicka:

* Ett lambda-uttryck för parametriserade asynkrona ombud för det `ComputerVisionClient.RecognizeTextAsync` metoden med URL: en för fjärransluten avbildningen och igenkänning av läge som parametrar, i `GetHeadersAsyncFunc`.
* En lambda-uttryck för ett ombud att hämta den `Operation-Location` svarshuvud värde, i `GetOperationUrlFunc`.

När den `RecognizeAsync` metoden har slutförts, både `UploadAndRecognizeImageAsync` och `RecognizeUrlAsync` metoder returnerar resultatet som en `TextOperationResult` instans. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

Den `RecognizeAsync` anropar parametriserade ombud för antingen den `ComputerVisionClient.RecognizeTextInStreamAsync` eller `ComputerVisionClient.RecognizeTextAsync` metoden tidsstämpelsträng `GetHeadersAsyncFunc` och väntar på svaret. Metoden anropar sedan delegera tidsstämpelsträng `GetOperationUrlFunc` att hämta den `Operation-Location` svar huvudvärde från svaret. Det här värdet är den URL som används för att hämta resultaten av metoden tidsstämpelsträng `GetHeadersAsyncFunc` från visuellt.

Den `RecognizeAsync` sedan anropar den `ComputerVisionClient.GetTextOperationResultAsync` metoden och skicka den URL som hämtats från den `Operation-Location` svarshuvud att hämta status och resultatet av den metod som skickats in `GetHeadersAsyncFunc`. Om statusen inte visar att metoden har slutförts, eller utan fel, den `RecognizeAsync` metodanrop `ComputerVisionClient.GetTextOperationResultAsync` 3 flera gånger, väntar på 3 sekunder mellan anrop. Den `RecognizeAsync` metoden returnerar resultaten till den metod som kallas den.

## <a name="explore-the-get-thumbnail-scenario"></a>Utforska hämta miniatyr-scenario

Det här scenariot hanteras av sidan ThumbnailPage.xaml. Du kan ange om du vill använda smart beskärning och ange önskad höjd och bredd att generera en miniatyr från en avbildning, och se både avbildningen och resultaten. Sidan scenario använder följande metoder, beroende på bildens källa:

* UploadAndThumbnailImageAsync  
  Den här metoden används för lokal avbildningar, där avbildningen måste vara kodad som en `Stream` och skickas till visuellt genom att anropa den `ComputerVisionClient.GenerateThumbnailInStreamAsync` metoden.
* ThumbnailUrlAsync  
  Den här metoden används för fjärråtkomst-avbildningar, som URL för bilden skickas till visuellt genom att anropa den `ComputerVisionClient.GenerateThumbnailAsync` metoden.

Den `UploadAndThumbnailImageAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Eftersom exempelappen är att analysera en lokal avbildning, har att skicka innehållet i avbildningen till visuellt. Öppnas den lokala filen som anges i `imageFilePath` för läsning som en `Stream`. Den anropar den `ComputerVisionClient.GenerateThumbnailInStreamAsync` metoden och skicka bredd, höjd, den `Stream` för filen och om du vill använda smart beskärning returnerar sedan resultatet som en `Stream`. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

Den `RecognizeUrlAsync` metoden skapar en ny `ComputerVisionClient` instans, med den angivna prenumerations nyckel och slutpunkt-URL. Den anropar den `ComputerVisionClient.GenerateThumbnailAsync` metoden anropas, bredd, höjd, URL: en för bilden och om du vill använda smart beskärning returnerar sedan resultatet som en `Stream`. Metoderna som ärvs från den `ImageScenarioPage` klass presentera de returnerade resultaten på sidan för scenariot.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort mappen där du klonade i `Microsoft/Cognitive-Vision-Windows` lagringsplats. Om du har valt för att använda exempelbilderna även bort mappen där du klonade i `Microsoft/Cognitive-Face-Windows` lagringsplats.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med Ansikts-API](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
