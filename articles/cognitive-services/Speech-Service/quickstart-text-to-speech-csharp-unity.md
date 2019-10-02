---
title: 'Snabbstart: Syntetiskt tal, Union tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett text till tal-program med Unity och talet SDK för Unity. När du är färdig kan du syntetisera tal från text i real tid till enhetens högtalare.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: be5f07b8ea58d0d62c70e0e9dc8ab187ce4a0f63
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803189"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity"></a>Snabbstart: Syntetisera tal med talet SDK för Unity

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-csharp-unity.md).

Använd den här guiden för att skapa ett text till tal-program med hjälp av [Unity](https://unity3d.com/) och talet SDK för Unity.
När du är färdig kan du syntetisera tal från text i real tid till enhetens högtalare.
Om du inte är bekant med Unity rekommenderar vi att du läser [användarhandboken för Unity](https://docs.unity3d.com/Manual/UnityManual.html) innan du börjar med programutvecklingen.

> [!NOTE]
> Det stöder Windows Desktop (x86 och x64) eller Universell Windows-plattform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

* [2018,3 eller senare](https://store.unity.com/) med [Unity 2019,1 lägga till stöd för UWP-arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Version 15,9 eller senare av Visual Studio 2017 är också acceptabelt.
* För Windows ARM64-stöd installerar du de [valfria build-verktygen för arm64 och Windows 10 SDK för arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* En prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-unity-project"></a>Skapa ett Unity-projekt

* Starta Unity, gå till fliken **Projekt** och välj **Nytt**.
* Ange **Projektnamn** som **csharp-unity**, **Mall** som **3D** och välj en plats.
  Välj sedan **Skapa projekt**.
* Efter en stund bör Unity Editor-fönstret visas.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* Speech SDK för Unity (betaversion) packas som ett Unity-resurspaket (.unitypackage).
  Ladda ned det [här](https://aka.ms/csspeech/unitypackage).
* Importera tal Speech SDK genom att välja **Resurser** > **Importera paket** > **Anpassat paket**.
  Mer information finns i [dokumentationen för Unity](https://docs.unity3d.com/Manual/AssetPackages.html).
* I filväljaren väljer du den Speech SDK .unitypackage-fil som du laddade ned ovan.
* Se till att alla filer är markerade och klicka på **Importera**:

  ![Skärmbild av Unity Editor vid import av Speech SDK Unity-resurspaketet](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>Lägga till användargränssnitt

Vi lägger till ett minimalt gränssnitt i vår scen, som består av ett indatafält för att ange texten för syntes, en knapp som utlöser tal syntes och ett textfält som visar resultatet.

* I [hierarkifönstret](https://docs.unity3d.com/Manual/Hierarchy.html) (som standard finns till vänster) visas en exempelscen som Unity skapade med det nya projektet.
* Klicka på knappen **skapa** längst upp i hierarkifönstret och välj **UI** > **indatafält**.
* Detta skapar tre spel objekt som du kan se i hierarkifönstret: ett **Indatamängds** objekt kapslat i ett **arbets ytans** objekt och ett **Event Events** -objekt.
* [Navigera i vyn scen](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en lämplig vy över arbets ytan och inmatade fält i [vyn scen](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Klicka på objektet **indatafält** i fönstret hierarki för att visa dess inställningar i [fönstret kontrollant](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ange egenskaperna för **POS X** och **POS Y** till **0**, så att inmatade fält centreras i mitten av arbets ytan.
* Klicka på knappen **skapa** överst i hierarkifönstret igen och välj **UI** > **om** du vill skapa en knapp.
* Klicka på **knappobjektet** i hierarkifönstret för att visa dess inställningar i fönstret [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspektör, som standard till höger).
* Ange egenskaperna för **POS X** och **POS Y** till **0** och **-48**och ange egenskaperna **width** och **height** till **160** och **30** för att se till att knappen och inmatade fält inte överlappar varandra.
* Klicka på knappen **skapa** överst i hierarkifönstret igen och välj **UI** > -**text** för att skapa ett textfält.
* Klicka på **textobjektet** i hierarkifönstret för att visa dess inställningar i fönstret [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspektör, som standard till höger).
* Ange egenskaperna för **POS X** och **POS Y** till **0** och **80**och ange egenskaperna **width** och **height** till **320** och **80** för att se till att textfältet och inmatade fält inte överlappar varandra.
* Klicka på knappen **skapa** överst i hierarkifönstret igen och välj ljud**källa** för **ljud** >  för att skapa en ljud källa.

När du är klar bör användargränssnittet ut ungefär som på den här skärmbilden:

[![Skärmbild av användargränssnittet för snabbstart i Unity Editor](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. I [projektfönstret](https://docs.unity3d.com/Manual/ProjectView.html) (som standard längst ned till vänster) klickar du på knappen **Skapa** och väljer sedan **C#-skript**. Ge skriptet namnet `HelloWorld`.

1. Redigera skriptet genom att dubbelklicka på det.

   > [!NOTE]
   > Du kan konfigurera vilken kodredigerare som startas under **Redigera** > **Inställningar**. Se [användarhandboken för Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt all kod med följande:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Leta upp och ersätt strängen `YourSubscriptionKey` med din prenumerations nyckel för tal tjänster.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i skriptet.

1. När du är tillbaka i Unity Editor måste skriptet läggas till som en komponent i ett av spelobjekten.

   * Klicka på **arbetsyteobjektet** i hierarkifönstret. Då öppnas inställningen i [fönstret kontrollant](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
   * Klicka på knappen **Lägg till komponent** i fönstret Inspector (Inspektör) och sök sedan efter det HelloWorld-skript som vi skapade ovan och lägg till det.
   * Observera att Hello World-komponenten har fyra oinitierade egenskaper, **utdata**, **inmatnings fält**, **Speak-knapp** och **ljud källa**som matchar de offentliga egenskaperna för klassen @no__t 4.
     För att koppla dem klickar du på objektväljaren (den lilla cirkelikonen till höger om egenskapen) och väljer de textobjekt och knappobjekt som du skapade tidigare.

     > [!NOTE]
     > Inmatade fält och knapp har också ett kapslat text objekt. Se till att du inte av misstag väljer att använda textutdata (eller byta namn på textobjekten med fältet namn i fönstret kontrollant för att undvika att förvirring).

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

* Tryck på knappen **Play** (Spela upp) i verktygsfältet i Unity Editor (under menyraden).

* När appen har startats anger du lite text i fältet inmatare och klickar på knappen. Texten skickas till tal tjänsterna och syntetiskt till tal, som spelas upp på din talare.

  [![Skärmbild med den snabbstart som körs i Unity-spelfönstret](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Titta efter felsökningsmeddelanden i [konsolfönstret](https://docs.unity3d.com/Manual/Console.html).

* När du är klar med att syntetisera talet klickar du på **uppspelnings** knappen i Unity editor-verktygsfältet för att stoppa appen.

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan även distribueras till Android, som en fristående Windows-app eller som ett UWP-program.
Se vår [exempellagringsplats](https://aka.ms/csspeech/samples) i mappen quickstart/csharp-unity som beskriver konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa röst teckensnitt](how-to-customize-voice-font.md)
- [Spela in röst exempel](record-custom-voice-samples.md)
