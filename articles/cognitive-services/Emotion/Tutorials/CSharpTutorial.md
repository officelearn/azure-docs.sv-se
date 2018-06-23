---
title: Känslo -API C# kursen | Microsoft Docs
description: Utforska en grundläggande Windows-app som använder kognitiva Services Känslo-API för att identifiera emotikoner uttryckt genom ytor i en bild.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352461"
---
# <a name="emotion-api-in-c35-tutorial"></a>Känslo-API i C&#35; självstudiekursen

> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Utforska ett grundläggande Windows-program som använder Känslo-API för att identifiera emotikoner uttryckt genom ytor i en bild. Den under exempel kan du skicka en bild-URL eller en lokalt lagrad fil. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app för Windows med Känslo-API och WPF (Windows Presentation Foundation), en del av .NET Framework.

## <a name="Prerequisites">Förutsättningar</a>
#### <a name="platform-requirements"></a>Plattformskrav  
I exemplet nedan har utvecklats för .NET Framework med [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Prenumerera på Känslo-API och få en nyckel för prenumeration  
Innan du skapar exemplet måste du prenumerera på Känslo-API som ingår i kognitiva Microsoft-tjänster. Se [prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Både den primära och sekundära nyckeln kan användas i den här självstudiekursen. Se till att följa de bästa metoderna för att hålla din API-nyckeln hemlig och säkra.  

#### <a name="get-the-client-library-and-example"></a>Installera klienten för bibliotek och exempel  
Du kan ladda ned Känslo-API-klientbiblioteket via [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Hämtade zip-filen måste extraheras till en valfri mapp, många användare väljer du mappen för Visual Studio 2015.
## <a name="Step1">Steg 1: Öppna exemplet</a>
1.  Starta Microsoft Visual Studio 2015 och på **filen**väljer **öppna**, sedan **projekt/lösning**.
2.  Bläddra till mappen där du sparade de hämtade Känslo-API-filerna. Klicka på **Känslo**, sedan **Windows**, och sedan den **exempel WPF** mapp.
3.  Dubbelklicka om du vill öppna Visual Studio 2015-lösning (.sln)-fil med namnet **EmotionAPI-WPF-Samples.sln**. Lösningen öppnas i Visual Studio.

## <a name="Step2">Steg 2: Skapa exemplet</a>
1. I **Solution Explorer**, högerklicka på **referenser** och välj **hantera NuGet-paket**.

  ![Öppna Nuget Package Manager](../Images/EmotionNuget.png)

2.  Den **NuGet Package Manager** öppnas. Först välja **Bläddra** i det övre vänstra hörnet och sedan i sökrutan skriver ”Newtonsoft.Json”, Välj den **Newtonsoft.Json** paketet och klickar på **installera**.  

  ![Bläddra till NuGet-paket](../Images/EmotionNugetBrowse.png)  

3.  Tryck på Ctrl + Skift + B, eller klicka på **skapa** på menyn menyfliksområdet väljer **skapa lösning**.

## <a name="Step3">Steg 3: Kör exemplet</a>
1.  När versionen är klar trycker du på **F5** eller klicka på **starta** på menyn menyfliksområdet om du vill köra exemplet.
2.  Leta upp fönstret Känslo-API med den **textruta** läsning ”**klistra in prenumerationen nyckeln här för att starta**”. Klistra in din prenumeration nyckel i textrutan som visas i nedan skärmbild. Du kan välja att spara din prenumeration nyckel på din dator eller en bärbar dator genom att klicka på ”Spara nyckel”. Klicka på ”Ta bort nyckeln” om du vill ta bort den från din dator eller en bärbar dator när du vill ta bort nyckeln för prenumeration från systemet.
  
  ![Känslo funktioner gränssnitt](../Images/EmotionKey.png)

3.  Under ”**Välj scenariot**” Klicka om du vill använda någon av de två scenarierna ”**identifiera känslo med hjälp av en dataström**” eller ”**identifiera känslo med en URL**”, följ instruktionerna på skärmen. Microsoft tar emot de avbildningar du överför och använder dem för att förbättra Känslo-API och tillhörande tjänster. Genom att skicka in en avbildning, bekräftar du att du har följt våra [Developer uppförandekod](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Det finns exempel bilder som ska användas med det här exempelprogrammet. Du hittar dessa avbildningar på [Ansikts-API Github-repo](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) under den **Data** mapp. Observera användningen av dessa avbildningar är licensierad under verkliga använda avtalet, vilket innebär att de är OK om du vill använda för att testa det här exemplet, men inte för att publicera.

## <a name="Review">Granska och lära dig</a>
Nu när du har ett program som körs, låt oss gå igenom hur exempel appen kan integreras med kognitiva Microsoft-tjänster. Det gör det enklare att fortsätta skapa på den här appen eller utveckla egna app med Microsoft Känslo-API. 

Den här appen exempel gör användning av klientbiblioteket Känslo-API, en tunn C# klienten Omslutning för Microsoft Känslo-API. När du har skapat appen exempel som beskrivs ovan, har du klientbiblioteket från NuGet-paketet. Du kan granska källkoden klientbiblioteket i mappen med rubriken ”[klientbiblioteket](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)” under **Känslo**, **Windows**, **klientbiblioteket** , som är en del av den hämta filen databasen sagts ovan i [krav](#Prerequisites).
 
Du kan också hitta information om hur du använder klientbiblioteket koden i **Solution Explorer**: Under **EmotionAPI WPF_Samples**, expandera **DetectEmotionUsingStreamPage.xaml** till Leta upp **DetectEmotionUsingStreamPage.xaml.cs**, som används för att bläddra till en lokalt lagrad fil eller expandera **DetectEmotionUsingURLPage.xaml** att hitta  **DetectEmotionUsingURLPage.xaml.cs**, som används när du överför en bild-URL. Dubbelklicka på den. xaml.cs filer så att de öppna i nytt fönster i Visual Studio. 

Granska hur klientbiblioteket Känslo får användas i vårt exempelapp kan vi titta på två kodavsnitt från **DetectEmotionUsingStreamPage.xaml.cs** och **DetectEmotionUsingURLPage.xaml.cs**. Varje fil innehåller koden kommentarer som anger ”nyckel exempel kod startar här” och ”nyckel exempel kod slutar här” för att hitta koden kodavsnitt reproduceras nedan.

Känslo-API kan arbeta med en bild-URL eller binär data (i form av en oktett-ström) som indata. De två alternativen granskas nedan. I båda fallen måste du först söka efter en med hjälp av direktiv som gör att du använder klientbiblioteket Känslo. 
```csharp

            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE STARTS HERE 
            // Use the following namespace for EmotionServiceClient 
            // ----------------------------------------------------------------------- 
            using Microsoft.ProjectOxford.Emotion; 
            using Microsoft.ProjectOxford.Emotion.Contract; 
            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs 

Det här kodstycket visar hur du använder klientbiblioteket för att skicka din prenumeration nyckel och en foto-URL till Känslo-API-tjänsten. 

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs 

Nedan är hur du skickar in din prenumeration nyckel och en lokalt lagrad bild Känslo-API: et. 


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
