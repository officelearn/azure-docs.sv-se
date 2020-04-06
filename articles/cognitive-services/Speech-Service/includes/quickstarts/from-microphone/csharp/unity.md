---
title: 'Snabbstart: Känna igen tal från en mikrofon, C# (Unity)- Taltjänst'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 6b3c8c62382d548d8a1bc7d5f611cdfbe2fd72fb
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671394"
---
> [!NOTE]
> Speech SDK för unity stöder Windows Desktop (x86 och x64) eller Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64)

## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=unity&pivots=programming-language-csharp)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

Om du redan har gjort det här, bra. Låt oss fortsätta.

## <a name="create-a-unity-project"></a>Skapa ett Unity-projekt

1. Öppna Unity. Om du använder Unity för första gången visas **unity hub-fönstret.** *<version number>* (Du kan också öppna Unity Hub direkt för att komma till det här fönstret.)

   [![Fönstret Enhetshubben](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Välj **Ny**. Fönstret **Skapa ett nytt projekt med Enhet** *<version number>* visas.

   [![Skapa ett nytt projekt i Unity Hub](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. Ange **csharp-unity**i **Projektnamn**.
1. Markera den om **3D** inte redan är markerad i **Mallar**.
1. Markera eller skapa en mapp som projektet ska sparas i i **Plats.**
1. Välj **Skapa**.

Efter lite tid visas fönstret Unity Editor.



## <a name="add-ui"></a>Lägga till användargränssnitt

Nu ska vi lägga till ett minimalt användargränssnitt till vår scen. Det här användargränssnittet består av en knapp för att utlösa taligenkänning och ett textfält för att visa resultatet. I [ **fönstret Hierarki** ](https://docs.unity3d.com/Manual/Hierarchy.html)visas en exempelscen som Unity har skapats med det nya projektet.

1. Högst upp i **hierarkifönstret** väljer du **Knappen Skapa** > **användargränssnitt** > **.**

   Den här åtgärden skapar tre spelobjekt som du kan se i **hierarkifönstret:** ett **Knapp-objekt,** ett **Canvas-objekt** som innehåller knappen och ett **EventSystem-objekt.**

   [![Unity Editor miljö](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigera i **scenvyn** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en bra bild av arbetsytan och knappen i [ **scenvyn** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. I [fönstret **Inspektör** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger) ställer du in egenskaperna **Pos X** och **Pos Y** till **0**, så att knappen är centrerad i mitten av arbetsytan.

1. I fönstret **Hierarki** väljer du **Skapa** > **gränssnittstext** > **Text** för att skapa ett **textobjekt.**

1. I fönstret **Inspektör** ställer du in egenskaperna **Pos X** och **Pos Y** till **0** och **120**och ställer in egenskaperna **Bredd** och **höjd** på **240** och **120**. Dessa värden säkerställer att textfältet och knappen inte överlappar varandra.

När du är klar bör **scenvyn** se ut ungefär som den här skärmbilden:

[![Scenvy i Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

Så här lägger du till exempelskriptkoden för Unity-projektet:

1. I [projektfönstret](https://docs.unity3d.com/Manual/ProjectView.html)väljer du **Skapa** > **C#-skript** för att lägga till ett nytt C#-skript.

   [![Projektfönstret i Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Ge skriptet namnet `HelloWorld`.

1. Dubbelklicka `HelloWorld` för att redigera det nyskapade skriptet.

   > [!NOTE]
   > Om du vill konfigurera kodredigeraren så att den ska användas av Unity för redigering väljer du **Redigera** > **inställningar**och går sedan till inställningarna för **externa verktyg.** Mer information finns i [enhetsanvändarhandboken](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt det befintliga skriptet med följande kod:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Hitta och ersätta `YourSubscriptionKey` strängen med prenumerationsnyckeln för Taltjänsten.

1. Hitta och ersätta `YourServiceRegion` strängen med **regionidentifieraren** från [den region som](https://aka.ms/speech/sdkregion) är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i skriptet.

Gå nu tillbaka till Unity Editor och lägg till skriptet som en komponent i ett av dina spelobjekt:

1. Markera **canvasobjektet** i **fönstret Hierarki.**

1. Välj knappen **Lägg till komponent** i fönstret **Inspektör.**

   [![Fönstret Inspektör i Enhetsredigeraren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. I listrutan söker du efter `HelloWorld` skriptet vi skapade ovan och lägger till det. Avsnittet **Hello World (Script)** visas i fönstret **Inspektör,** med två oinitierade egenskaper, **Utdatatext** och **knappen Starta om igen**. Dessa Unity-komponentegenskaper matchar `HelloWorld` klassens offentliga egenskaper.

1. Markera objektväljaren **Start Reco Button** (ikonen för den lilla cirkeln till höger om egenskapen) och välj **det Knappobjekt** som du skapade tidigare.

1. Markera objektväljaren för objektväljaren **för utdatatext** och välj det **textobjekt** som du skapade tidigare.

   > [!NOTE]
   > Knappen har även ett kapslat textobjekt. Se till att du inte av misstag väljer den för textutdata (eller byter namn på ett av textobjekten med hjälp av fältet **Namn** i fönstret **Inspektör** för att undvika förvirring).

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

Nu är du redo att köra programmet i Unity Editor.

1. I verktygsfältet Unity Editor (under menyraden) väljer du knappen **Spela upp** (en höger-pekstriangel).

1. Gå till [ **spelvyn** ](https://docs.unity3d.com/Manual/GameView.html)och vänta **tills textobjektet** visas **Klicka-knappen för att känna igen tal**. (Den visar **ny text** när programmet inte har startat eller inte är redo att svara.)

1. Markera knappen och säg en engelsk fras eller mening till datorns mikrofon. Ditt tal överförs till taltjänsten och transkriberas till text, som visas i **spelvyn.**

   [![Spelvy i Unity Editor](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Sök efter felsökningsmeddelanden i [ **konsolfönstret.** ](https://docs.unity3d.com/Manual/Console.html) Om **konsolfönstret** inte visas går du till menyraden och väljer **Fönster** > **allmänt** > **konsol** för att visa det.

1. När du är klar med att känna igen tal väljer du knappen **Spela** upp i verktygsfältet Unity Editor för att stoppa programmet.

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan också distribueras till som en Android-app, en fristående Windows-app eller ett UWP-program.
Mer information finns i vårt [exempel på databas](https://aka.ms/csspeech/samples). Mappen `quickstart/csharp-unity` beskriver konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](../footer.md)]
