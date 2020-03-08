---
title: 'Snabb start: syntetiskt tal C# , (Unit) – tal service'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett text till tal-program med Unity och talet SDK för Unity. När du är färdig kan du syntetisera tal från text i real tid till enhetens högtalare.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 73a4477547c562a9960fe72b352e906676eadae5
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925710"
---
> [!NOTE]
> Uniting stöder Windows Desktop (x86 och x64) eller Universell Windows-plattform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=unity)

## <a name="add-a-ui"></a>Lägg till ett användar gränssnitt

Vi lägger till ett minimalt gränssnitt i vår scen som består av ett indatafält för att ange texten för syntes, en knapp för att utlösa tal syntes och ett textfält som visar resultatet.

* I [fönstret hierarki](https://docs.unity3d.com/Manual/Hierarchy.html) (som standard till vänster) visas en exempel scen som har skapats med det nya projektet.
* Välj knappen **skapa** överst i **hierarkifönstret** och välj **UI** > **indatafält**.
* Med det här alternativet skapas tre spel objekt som du kan se i **hierarkifönstret** : ett **indatamängds** objekt kapslat i ett **arbets ytans** objekt och ett **Event Events** -objekt.
* [Navigera i vyn scen](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en lämplig vy över arbets ytan och inmatade fält i [vyn scen](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Välj objektet **indatafält** i fönstret **hierarki** för att visa dess inställningar i [fönstret kontrollant](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ange egenskaperna **POS X** och **POS Y** till **0** så att inmatade fält centreras i mitten av arbets ytan.
* Välj knappen **skapa** överst i **hierarkifönstret** igen. Välj **UI** > **knappen** för att skapa en knapp.
* Välj objektet **knapp** i fönstret **hierarki** för att visa dess inställningar i [fönstret kontrollant](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ange egenskaperna för **POS X** och **POS Y** till **0** och **-48**. Ange egenskaperna **width** och **height** till **160** och **30** för att se till att knappen och inmatade fält inte överlappar varandra.
* Välj knappen **skapa** överst i **hierarkifönstret** igen. Välj **UI** > **text** för att skapa ett textfält.
* Välj **textobjektet** i fönstret **hierarki** för att visa dess inställningar i [fönstret kontrollant](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ange egenskaperna för **POS X** och **POS Y** till **0** och **80**. Ange egenskaperna **width** och **height** till **320** och **80** för att se till att textfältet och inmatade fält inte överlappar varandra.
* Välj knappen **skapa** överst i **hierarkifönstret** igen. Välj **ljud** > **ljud källa** för att skapa en ljud källa.

När du är klar bör användar gränssnittet se ut ungefär som den här skärm bilden:

[![Skärmbild av användargränssnittet för snabbstart i Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Välj knappen **skapa** i [fönstret projekt](https://docs.unity3d.com/Manual/ProjectView.html) (som standard längst ned till vänster) och välj sedan  **C# skript**. Ge skriptet namnet `HelloWorld`.

1. Redigera skriptet genom att dubbelklicka på det.

   > [!NOTE]
   > Du kan konfigurera vilken kod redigerare som startas genom att välja **redigera** > **Inställningar**. Mer information finns i [användar handboken för Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt all kod med följande:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Leta upp och ersätt strängen `YourSubscriptionKey` med prenumerations nyckeln för röst tjänsten.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. Regionen är till exempel `westus` om du använder den kostnads fria utvärderings versionen.

1. Spara ändringarna i skriptet.

1. I Unity-redigeraren lägger du till skriptet som en komponent i ett av dina spel objekt.

   * Välj objektet **arbets yta** i fönstret **hierarki** för att öppna inställningen i [fönstret kontrollant](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
   * Välj knappen **Lägg till komponent** i fönstret **kontrollant** . Sök sedan efter `HelloWorld` skriptet som vi skapade tidigare och lagt till det.
   * Komponenten HelloWorld har fyra oinitierade egenskaper, **utdata**, **inmatnings fält**, Speak- **knapp** och **ljud källa**som matchar de offentliga egenskaperna i `HelloWorld`-klassen.
     För att ansluta dem till, väljer du objekt väljaren (den lilla cirkel ikonen till höger om egenskapen). Välj de text-och knapp objekt som du skapade tidigare.

     > [!NOTE]
     > Inmatade fält och knapp har också ett kapslat text objekt. Se till att du inte oavsiktligt väljer att skriva ut text. Alternativt kan du byta namn på de text objekt som använder fältet **namn** i fönstret **Granska** för att undvika förvirring.

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

* Välj knappen **spela upp** i verktygsfältet Unity Editor som finns under meny raden.
* När appen har startats anger du lite text i fältet inmatare och klickar på knappen. Texten skickas till tal tjänsten och syntetiskt till tal som spelas upp på din talare.

  [![skärm bild av snabb starten i fönstret Unity Game](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Kontrol lera [konsol fönstret](https://docs.unity3d.com/Manual/Console.html) för fel söknings meddelanden.

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan också distribueras till Android som en fristående Windows-app eller ett UWP-program.
Se [exempel lagrings platsen](https://aka.ms/csspeech/samples) i mappen snabb start/csharp-Unity som beskriver konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
