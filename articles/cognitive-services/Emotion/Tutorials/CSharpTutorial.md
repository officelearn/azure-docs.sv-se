---
title: 'Självstudie: Känsloigenkänning i ett ansikte på en bild – Känslo-API, C#'
titlesuffix: Azure Cognitive Services
description: Utforska en grundläggande Windows-app för att känna igen känslor som uttrycks i ansikten på en bild.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 01/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: f3a84a68718fba29e2a4b2fae057e68976119c95
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237032"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>Självstudie: Känsloigenkänning i ett ansikte på en bild.

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/). 

Utforska ett grundläggande Windows-program som använder Känslo-API för att känna igen känslor som uttrycks i ansiktena på en bild. Med exemplet nedan kan du skicka en bild-URL eller en lokalt lagrad fil. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app för Windows med hjälp av Känslo-API och WPF (Windows Presentation Foundation), en del av .NET Framework.

## <a name="Prerequisites">Förutsättningar</a>
#### <a name="platform-requirements"></a>Plattformskrav  
Exemplet nedan har utvecklats för .NET Framework med hjälp av [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Prenumerera på Känslo-API och få en prenumerationsnyckel  
Innan du skapar exemplet måste du prenumerera på Känslo-API, som ingår i Microsoft Cognitive Services. Se [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Både den primära och sekundära nyckeln kan användas i den här självstudien. Se till att följa bästa praxis för att hålla din API-nyckelhemlighet säker.  

#### <a name="get-the-client-library-and-example"></a>Hämta klientbiblioteket och exempel  
Du kan ladda ned Känslo-API-klientbiblioteket via [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Den nedladdade zip-filen måste extraheras till en mapp som du väljer. Många användare väljer mappen för Visual Studio 2015.
## <a name="Step1">Steg 1: Öppna exemplet</a>
1.  Starta Microsoft Visual Studio 2015 och klicka på **Arkiv**, välj **Öppna** och välj sedan **Projekt/lösning**.
2.  Bläddra till den mapp där du sparade de nedladdade Känslo-API-filerna. Klicka på **Känsla**, sedan på **Windows** och sedan på mappen **Sample-WPF**.
3.  Dubbelklicka för att öppna Visual Studio 2015-lösningsfilen (.sln) med namnet **EmotionAPI-WPF-Samples.sln**. Då öppnas lösningen i Visual Studio.

## <a name="Step2">Steg 2: Skapa exemplet</a>
1. I **Solution Explorer** högerklickar du på **Referenser** och väljer **Hantera NuGet-paket**.

  ![Öppna NuGet-pakethanteraren](../Images/EmotionNuget.png)

2.  Fönstret för **NuGet-pakethanteraren** öppnas. Välj först **Bläddra** i det övre vänstra hörnet och skriv sedan ”Newtonsoft.Json” i sökrutan. Välj paketet **Newtonsoft.Json** och klicka på **Installera**.  

  ![Bläddra till NuGet-paketet](../Images/EmotionNugetBrowse.png)  

3.  Tryck på Ctrl + Skift + B eller klicka på **Skapa** i menyfliksområdet och välj sedan **Skapa lösning**.

## <a name="Step3">Steg 3: Kör exemplet</a>
1.  När skapandet är klart trycker du på **F5** eller klickar på **Starta** i menyfliksområdet för att köra exemplet.
2.  Leta upp fönstret för Känslo-API med den **textruta** där det står ”**Paste your subscription key here to start**” (Klistra in din prenumerationsnyckel här för att starta). Klistra in din prenumerationsnyckel i textrutan såsom det visas på skärmbilden nedan. Du kan välja att spara prenumerationsnyckeln på en dator eller bärbar dator genom att klicka på knappen ”Save Key” (Spara nyckel). När du vill ta bort prenumerationsnyckeln från systemet klickar du på ”Delete key” (Ta bort nyckel) för att ta bort den från den stationära eller bärbara datorn.

  ![Gränssnitt för känslofunktioner](../Images/EmotionKey.png)

3.  Under ”**Select Scenario**” (Välj Scenario) klickar du för att använda endera av de två scenarierna ”**Detect emotion using a stream**” (Identifiera känslor med hjälp av en ström) eller ”**Detect emotion using a URL**” (Identifiera känslor med hjälp av en URL) och följer sedan instruktionerna på skärmen. Microsoft tar emot de bilder du laddar upp och kan använda dem för att förbättra Känslo-API och relaterade tjänster. Genom att skicka in en bild bekräftar du att du har följt vår [uppförandekod för utvecklare](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Det finns exempelbilder att använda med det här exempelprogrammet. Du hittar dessa bilder på [GitHub-lagringsplatsen för Ansikts-API](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) under mappen **Data**. Observera att användningen av dessa bilder är licensierad under Fair Use-avtalet, vilket innebär att de får användas för testning i det här exemplet men inte för publicering.

## <a name="Review">Granska och lär</a>
Nu när programmet körs ska vi se hur exempelappen integreras med Microsoft Cognitive Services. Det här gör att du enklare kan bygga vidare på appen eller utveckla en egen app med hjälp av Microsofts Känslo-API.

Den här exempelappen använder Känslo-API-klientbiblioteket, en tunn C#-klientadapter för Microsofts Känslo-API. När du har skapat exempelappen enligt beskrivningen ovan fick du klientbiblioteket från ett NuGet-paket. Du kan granska klientbibliotekets källkod i mappen med namnet ”[Client Library](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)” under **Emotion**, **Windows**, **Client Library**, som är en del av den nedladdade fillagringsplatsen som nämnts ovan i [Krav](#Prerequisites).

Du kan även ta reda på hur du använder klientbibliotekskoden i **Solution Explorer**: under **EmotionAPI-WPF_Samples** expanderar du **DetectEmotionUsingStreamPage.xaml** och letar upp **DetectEmotionUsingStreamPage.xaml.cs**, som används för att bläddra till en lokalt lagrad fil, eller expanderar **DetectEmotionUsingURLPage.xaml** och letar upp  **DetectEmotionUsingURLPage.xaml.cs**, som används när du laddar upp en bild-URL. Dubbelklicka på .xaml.cs-filerna så att de öppnas i nya fönster i Visual Studio.

Vi tittar på hur känsloklientbiblioteket används i exempelappen genom att gå igenom två kodavsnitt från **DetectEmotionUsingStreamPage.xaml.cs** och **DetectEmotionUsingURLPage.xaml.cs**. Varje fil innehåller kodkommentarer som anger ”KEY SAMPLE CODE STARTS HERE” (PRIMÄR EXEMPELKOD STARTAR HÄR) och ”KEY SAMPLE CODE ENDS HERE” (PRIMÄR EXEMPELKOD SLUTAR HÄR) för att hjälpa dig att hitta de kodavsnitt som reproduceras nedan.

Känslo-API kan arbeta med en bild-URL eller binära bilddata (i form av en oktettström) som indata. De två alternativen granskas nedan. I båda fallen letar du först upp ett using-direktiv som gör att du kan använda känsloklientbiblioteket.
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

Det här kodavsnittet visar hur du använder klientbiblioteket för att skicka din prenumerationsnyckel och en foto-URL till Känslo-API-tjänsten.

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

Nedan visas hur du skickar din prenumerationsnyckel och en lokalt lagrad bild till Känslo-API.


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
