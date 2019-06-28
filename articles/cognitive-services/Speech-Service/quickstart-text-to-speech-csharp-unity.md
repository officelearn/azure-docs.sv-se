---
title: 'Snabbstart: Syntetisera tal, Unity - Speech Services'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett text till tal-program med Unity och tal-SDK för Unity (Beta). När du är klar kan du syntetisera tal från text i realtid till din enhets-API.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/26/2019
ms.author: yinhew
ms.openlocfilehash: 5240ea45097ce3c0ae7ccbc15a7f99b2f5990832
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467490"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity-beta"></a>Snabbstart: Syntetisera tal med Speech-SDK för Unity (Beta)

Snabbstarter kan också användas för [taligenkänning](quickstart-csharp-unity.md).

Använd den här guiden för att skapa en text till tal-program med hjälp av [Unity](https://unity3d.com/) och tal SDK för Unity (Beta).
När du är klar kan du syntetisera tal från text i realtid till din enhets-API.
Om du inte är bekant med Unity rekommenderar vi att du läser [användarhandboken för Unity](https://docs.unity3d.com/Manual/UnityManual.html) innan du börjar med programutvecklingen.

> [!NOTE]
> Speech SDK för Unity är för närvarande i betaversion.
> Den stöder Windows-skrivbordet (x86 och x64) eller Universal Windows Platform (x86, x64, ARM/ARM64) och Android (x86 ARM32/64).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

* [Unity 2018.3 eller senare](https://store.unity.com/) med [Unity 2019.1 lägger till stöd för UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * ARM64-support, installera den [valfria verktyg för ARM64 och SDK för Windows 10 för ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
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

Vi lägga till ett reducerat gränssnitt i vår scen som består av ett inmatningsfält för textinmatning för syntes, en knapp för att utlösa talsyntes och ett textfält för att visa resultatet.

* I [hierarkifönstret](https://docs.unity3d.com/Manual/Hierarchy.html) (som standard finns till vänster) visas en exempelscen som Unity skapade med det nya projektet.
* Klicka på den **skapa** överst i fönstret hierarkin och välj **UI** > **indata fältet**.
* Detta skapar tre spel objekt som visas i fönstret hierarki: en **indata fältet** objekt bäddas in i en **arbetsytan** objekt, och en **EventSystem** objekt.
* [Navigera-scen vyn](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en bra överblick över arbetsytan och inmatningsfält i den [scen visa](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Klicka på den **indata fältet** objekt i fönstret hierarki för att visa dess inställningar i den [Inspector fönstret](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ange den **Pos X** och **Pos Y** egenskaper så att **0**, så att fältet indata centreras i mitten av arbetsytan.
* Klicka på den **skapa** överst i fönstret hierarki igen och välj **UI** > **knappen** att skapa en knapp.
* Klicka på **knappobjektet** i hierarkifönstret för att visa dess inställningar i fönstret [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspektör, som standard till höger).
* Ange den **Pos X** och **Pos Y** egenskaper så att **0** och **-48**, och ange den **bredd** och **Höjd** egenskaper så att **160** och **30** så att knappen och fältet indata inte överlappar.
* Klicka på den **skapa** överst i fönstret hierarki igen och välj **UI** > **Text** att skapa ett textfält.
* Klicka på **textobjektet** i hierarkifönstret för att visa dess inställningar i fönstret [Inspector](https://docs.unity3d.com/Manual/UsingTheInspector.html) (Inspektör, som standard till höger).
* Ange den **Pos X** och **Pos Y** egenskaper så att **0** och **80**, och ange den **bredd** och  **Höjd** egenskaper så att **320** och **80** så att textfältet och fältet indata inte överlappar.
* Klicka på den **skapa** överst i fönstret hierarki igen och välj **ljud** > **ljudkälla** att skapa en ljudkälla.

När du är klar bör användargränssnittet ut ungefär som på den här skärmbilden:

[![Skärmbild av användargränssnittet för snabbstart i Unity Editor](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. I [projektfönstret](https://docs.unity3d.com/Manual/ProjectView.html) (som standard längst ned till vänster) klickar du på knappen **Skapa** och väljer sedan **C#-skript**. Ge skriptet namnet `HelloWorld`.

1. Redigera skriptet genom att dubbelklicka på det.

   > [!NOTE]
   > Du kan konfigurera vilken kodredigerare som startas under **Redigera** > **Inställningar**. Se [användarhandboken för Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt all kod med följande:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Leta upp och ersätta strängen `YourSubscriptionKey` med din prenumerationsnyckel för Speech Services.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i skriptet.

1. När du är tillbaka i Unity Editor måste skriptet läggas till som en komponent i ett av spelobjekten.

   * Klicka på **arbetsyteobjektet** i hierarkifönstret. Detta öppnar inställningen i den [Inspector fönstret](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
   * Klicka på knappen **Lägg till komponent** i fönstret Inspector (Inspektör) och sök sedan efter det HelloWorld-skript som vi skapade ovan och lägg till det.
   * Observera att Hello World-komponenten har fyra oinitierad egenskaper **utdatatext**, **indata fältet**, **tala knappen** och **ljud källa**, som matchar offentliga egenskaper av den `HelloWorld` klass.
     För att koppla dem klickar du på objektväljaren (den lilla cirkelikonen till höger om egenskapen) och väljer de textobjekt och knappobjekt som du skapade tidigare.

     > [!NOTE]
     > Indatafält och knappen har också en kapslad text-objektet. Kontrollera att du inte oavsiktligt väljer för text-utdata (eller byta namn på textobjekt som använder fältet Namn på i fönstret Inspector för att undvika sådan förväxling).

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

* Tryck på knappen **Play** (Spela upp) i verktygsfältet i Unity Editor (under menyraden).

* När appen startar ange text i fältet indata och klicka på knappen. Texten skickas till Speech Services och syntetiskt till tal som spelar upp på högtalaren.

  [![Skärmbild med den snabbstart som körs i Unity-spelfönstret](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Titta efter felsökningsmeddelanden i [konsolfönstret](https://docs.unity3d.com/Manual/Console.html).

* När du är klar synthesizing tal, klickar du på den **spela upp** i verktygsfältet Unity-Redigeraren för att stoppa appen.

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan även distribueras till Android, som en fristående Windows-app eller som ett UWP-program.
Se vår [exempellagringsplats](https://aka.ms/csspeech/samples) i mappen quickstart/csharp-unity som beskriver konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa rösttyper](how-to-customize-voice-font.md)
- [Post voice-exempel](record-custom-voice-samples.md)
