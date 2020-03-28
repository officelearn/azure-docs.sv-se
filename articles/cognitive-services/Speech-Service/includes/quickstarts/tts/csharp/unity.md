---
title: 'Snabbstart: Syntetisera tal, C# (Unity) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden om du vill skapa ett text-till-tal-program med Unity och Tal SDK för enhet. När du är klar kan du syntetisera tal från text i realtid till enhetens högtalare.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 73a4477547c562a9960fe72b352e906676eadae5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925710"
---
> [!NOTE]
> Unity stöder Windows Desktop (x86 och x64) eller Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64).

## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=unity)

## <a name="add-a-ui"></a>Lägga till ett användargränssnitt

Vi lägger till ett minimalt användargränssnitt i vår scen som består av ett indatafält för att ange texten för syntes, en knapp för att utlösa talsyntes och ett textfält för att visa resultatet.

* I [hierarkifönstret](https://docs.unity3d.com/Manual/Hierarchy.html) (som standard till vänster) visas en exempelscen som Unity har skapats med det nya projektet.
* Välj knappen **Skapa** högst upp i **hierarkifönstret** och välj**inmatningsfält för** **användargränssnitt** > .
* Med det här alternativet skapas tre spelobjekt som du kan se i **hierarkifönstret:** ett **indatafältsobjekt** som är kapslat i ett **Canvas-objekt** och ett **EventSystem-objekt.**
* [Navigera i scenvyn](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en bra vy över arbetsytan och inmatningsfältet i [scenvyn](https://docs.unity3d.com/Manual/UsingTheSceneView.html).
* Markera **indatafältsobjektet** i **hierarkifönstret** om du vill visa inställningarna i [fönstret Inspektör](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ange egenskaperna **Pos X** och **Pos Y** till **0** så att inmatningsfältet centrerats mitt på arbetsytan.
* Välj knappen **Skapa** högst upp i **hierarkifönstret** igen. Välj **gränssnittsknapp** > **Button** om du vill skapa en knapp.
* Markera **button-objektet** i **hierarkifönstret** om du vill visa dess inställningar i [fönstret Inspektör](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ställ in egenskaperna **Pos X** och **Pos Y** på **0** och **-48**. Ange egenskaperna **Bredd** och **höjd** till **160** och **30** för att säkerställa att knappen och inmatningsfältet inte överlappar varandra.
* Välj knappen **Skapa** högst upp i **hierarkifönstret** igen. Välj **gränssnittstext** > **Text** om du vill skapa ett textfält.
* Markera **textobjektet** i **hierarkifönstret** om du vill visa dess inställningar i [fönstret Inspektör](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
* Ställ in egenskaperna **Pos X** och **Pos Y** på **0** och **80**. Ange egenskaperna **Bredd** och **höjd** till **320** och **80** för att säkerställa att textfältet och inmatningsfältet inte överlappar varandra.
* Välj knappen **Skapa** högst upp i **hierarkifönstret** igen. Välj > **Ljudljudskälla** om du vill skapa en ljudkälla. **Audio**

När du är klar bör användargränssnittet se ut ungefär som den här skärmbilden:

[![Skärmbild av snabbstartsanvändargränssnittet i Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. I [projektfönstret](https://docs.unity3d.com/Manual/ProjectView.html) (som standard längst ned till vänster) markerar du knappen **Skapa** och väljer sedan **C#-skript**. Ge skriptet namnet `HelloWorld`.

1. Redigera skriptet genom att dubbelklicka på det.

   > [!NOTE]
   > Du kan konfigurera vilken kodredigerare som startas genom att välja **Redigera** > **inställningar**. Mer information finns i [enhetsanvändarhandboken](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt all kod med följande:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. Leta reda på `YourSubscriptionKey` och ersätta strängen med prenumerationsnyckeln för Taltjänsten.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. Regionen är `westus` till exempel om du använder den kostnadsfria utvärderingsversionen.

1. Spara ändringarna i skriptet.

1. Lägg till skriptet som en komponent i ett av dina spelobjekt i Unity Editor.

   * Markera **objektet Canvas** i **fönstret Hierarki** om du vill öppna inställningen i [fönstret Inspektör](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger).
   * Välj knappen **Lägg till komponent** i fönstret **Inspektör.** Sök sedan `HelloWorld` efter skriptet vi tidigare skapat och lägg till det.
   * HelloWorld-komponenten har fyra oinitierade egenskaper, **Utdatatext,** **inmatningsfält,** **läsknapp** och **ljudkälla**, som matchar de offentliga egenskaperna för `HelloWorld` klassen.
     Om du vill koppla in dem markerar du objektväljaren (ikonen för den lilla cirkeln till höger om egenskapen). Markera text- och knappobjekt som du skapade tidigare.

     > [!NOTE]
     > Inmatningsfältet och knappen har också ett kapslat textobjekt. Se till att du inte av misstag väljer den för textutdata. Du kan också byta namn på textobjekten som använder fältet **Namn** i fönstret **Inspektör** för att undvika förvirringen.

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

* Välj knappen **Spela** upp i verktygsfältet Enhetsredigerare som finns under menyraden.
* När appen har lanserats anger du text i inmatningsfältet och väljer knappen. Texten överförs till taltjänsten och syntetiseras till tal, som spelas upp på högtalaren.

  [![Skärmbild av snabbstarten i Unity-spelet i fönstret Unity](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* Sök efter felsökningsmeddelanden i [konsolfönstret.](https://docs.unity3d.com/Manual/Console.html)

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan också distribueras till Android som en fristående Windows-app eller ett UWP-program.
Se [exempeldatabasen](https://aka.ms/csspeech/samples) i mappen quickstart/csharp-unity som beskriver konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
