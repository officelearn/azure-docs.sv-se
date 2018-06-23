---
title: Datorn Vision API C# kursen | Microsoft Docs
description: Utforska en grundläggande Windows-app som använder datorn Vision API i kognitiva Microsoft-tjänster. Utföra OCR, skapa miniatyrbilder och arbeta med visuella funktioner i en bild.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352893"
---
# <a name="computer-vision-api-c35-tutorial"></a>Datorn Vision API C&#35; självstudiekursen

Utforska ett grundläggande Windows-program som använder datorn Vision API för att utföra OCR (OCR), skapa smart beskäras miniatyrer, plus identifieras, kategoriseras taggen och beskriver visual funktioner, inklusive ansikten, i en bild. Den under exempel kan du skicka en bild-URL eller en lokalt lagrad fil. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app för Windows med Vision API och WPF (Windows Presentation Foundation), en del av .NET Framework.

### <a name="prerequisites"></a>Förutsättningar

#### <a name="platform-requirements"></a>Plattformskrav

I exemplet nedan har utvecklats för .NET Framework med [Visual Studio 2015, Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Prenumerera på datorn Vision API och få en nyckel för prenumeration 

Innan du skapar exemplet måste du prenumerera på datorn Vision API som ingår i kognitiva Microsoft-tjänster (tidigare projekt Oxford). Prenumerationen och information om nyckelhantering finns [prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Både den primära och sekundära nyckeln kan användas i den här självstudiekursen. 

> [!NOTE]
> Guiden är utformad för prenumerationen nycklar i den **westcentralus** region. Prenumerationen nycklar genereras datorn Vision kostnadsfri testversion används den **westcentralus** region, så att de fungerar korrekt. Om du genererade din prenumeration nycklar med ditt Azure-konto via [ https://azure.microsoft.com/ ](https://azure.microsoft.com/), måste du ange den **westcentralus** region. Nyckeln som skapas utanför den **westcentralus** region fungerar inte.

#### <a name="get-the-client-library-and-example"></a>Installera klienten för bibliotek och exempel

Du kan klona datorn Vision API-bibliotek och exempel klientprogrammet till datorn via [SDK](https://www.github.com/microsoft/cognitive-vision-windows). Hämta den som en ZIP.

### <a name="Step1">Steg 1: Installera exemplet</a>

Öppna exempel-WPF\VisionAPI-WPF-Samples.sln i ditt GitHub-skrivbord.

### <a name="Step2">Steg 2: Skapa exemplet</a>

* Tryck på Ctrl + Skift + B, eller klicka på Skapa på menyn menyfliksområdet och välj sedan skapa lösning.

### <a name="Step3">Steg 3: Kör exemplet</a>

1. När versionen är klar trycker du på **F5** eller klicka på **starta** på menyn menyfliksområdet om du vill köra exemplet.
2. Leta upp fönstret dator Vision API användaren gränssnitt med redigera textrutan läsning ”klistra in prenumerationen nyckeln här för att starta”.
Du kan välja att spara din prenumeration nyckel på din dator eller en bärbar dator genom att klicka på ”Spara nyckel”. Klicka på ”Ta bort nyckeln” om du vill ta bort den från din dator eller en bärbar dator när du vill ta bort nyckeln för prenumeration från systemet.

    ![Vision prenumeration nyckel](../Images/Vision_UI_Subscription.PNG)

3. Under ”Välj scenariot” använda en av sex scenarier och klicka sedan följa instruktionerna på skärmen. Microsoft tar emot de avbildningar du överför och använder dem för att förbättra datorn Vision API och tillhörande tjänster. Genom att skicka in en avbildning, bekräftar du att du har följt våra [Developer uppförandekod](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Analysera avbildningen gränssnitt](../Images/Analyze_Image_Example.PNG)

4. Det finns exempel bilder som ska användas med det här exempelprogrammet. Du hittar dessa avbildningar på framsidan API Windows Github-lagringsplatsen i den [datamappen](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). Observera användningen av dessa avbildningar licensieras enligt avtalet [licens AVBILDNINGEN](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Granska och lära dig</a>

Nu när du har ett program som körs, låt oss gå igenom hur den här appen exempel integreras med kognitiva Services-teknik. Det gör det enklare att fortsätta skapa på den här appen eller utveckla egna app med Microsoft datorn Vision API.

Den här appen exempel gör använder datorn Vision API klientbiblioteket, en tunn C# klienten Omslutning för Microsoft datorn Vision API. När du har skapat appen exempel som beskrivs ovan, har du klientbiblioteket från NuGet-paketet. Du kan granska källkoden klientbiblioteket i mappen med rubriken ”**klientbiblioteket**” under **Vision**, **Windows**, **klientbiblioteket**, som ingår i den hämtade filen databasen sagts ovan i krav.

Du kan också hitta information om hur du använder klientbiblioteket kod i Solution Explorer: Under **VisionAPI WPF_Samples**, expandera **AnalyzePage.xaml** att hitta **AnalyzePage.xaml.cs**, som används för att skicka en bild till avbildningen analys slutpunkten. Dubbelklicka på den. xaml.cs filer så att de öppna i nytt fönster i Visual Studio.

Granska hur visionen klientbiblioteket hämtar används i vårt exempelapp kan vi titta på två kodavsnitt från **AnalyzePage.xaml.cs**. Filen innehåller koden kommentarer som anger ”nyckel exempel kod startar här” och ”nyckel exempel kod slutar här” för att hitta koden kodavsnitt reproduceras nedan.

Analysera slutpunkten kan arbeta med en bild-URL eller binär data (i form av en oktett-ström) som indata. Först måste du söka efter en med hjälp av direktiv som gör att du använder klientbiblioteket Vision.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(...)**  Det här kodstycket visar hur du använder klientbiblioteket för att skicka din prenumeration nyckel och en lokalt lagrad bild att analysera slutpunkten för datorn Vision API-tjänsten.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(...)**  Det här kodstycket visar hur du använder klientbiblioteket för att skicka din prenumeration nyckel och en foto-URL för att analysera slutpunkten för datorn Vision API-tjänsten.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Andra sidor och slutpunkter** visas hur du interagerar med de övriga slutpunkterna som exponeras av datorn Vision API-tjänsten genom att titta på andra sidor i exemplet; exempelvis OCR slutpunkten visas som en del av koden i OCRPage.xaml.cs 

### <a name="Related">Närliggande information</a>
 * [Kom igång med Ansikts-API](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


