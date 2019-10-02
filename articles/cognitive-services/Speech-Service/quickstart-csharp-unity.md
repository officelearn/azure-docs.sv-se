---
title: 'Snabbstart: Identifiera tal, uppunions tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett tal-till-text-program med Unity och talet SDK för Unity. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: jhakulin
ms.openlocfilehash: a7ac6831fd21fcc0dc425b57f5d73d8c328f9350
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803475"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-unity"></a>Snabbstart: Identifiera tal med talet SDK för Unity

Snabb Starter är också tillgängliga för [text till tal](quickstart-text-to-speech-csharp-unity.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Använd den här guiden för att skapa ett tal-till-text-program med hjälp av [Unity](https://unity3d.com/) och talet SDK för Unity.
När du är färdig kan du prata med din enhet för att skriva tal till text i real tid.
Om du är nybörjare på enhets nivå föreslår vi att du studerar enhets bevarans [Användar handbok](https://docs.unity3d.com/Manual/UnityManual.html) innan du utvecklar ditt program.

> [!NOTE]
> Talet SDK för Unit stöder Windows Desktop (x86 och x64) eller Universell Windows-plattform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) och iOS (x64 simulator, ARM32 och ARM64)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

- [2018,3 eller senare](https://store.unity.com/) med [Unity 2019,1 lägga till stöd för UWP-arm64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Version 15,9 eller senare av Visual Studio 2017 är också acceptabelt.
- För Windows ARM64-stöd installerar du de [valfria build-verktygen för arm64 och Windows 10 SDK för arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).
- En prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
- Åtkomst till datorns mikrofon.

## <a name="create-a-unity-project"></a>Skapa ett Unity-projekt

1. Öppna Unity. Om du använder Unity för första gången visas fönstret **Unity Hub** *<version number>* . (Du kan också öppna Unity Hub direkt för att komma till det här fönstret.)

   [@no__t 1Unity Hub-fönster](media/sdk/qs-csharp-unity-hub.png)](media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Välj **Ny**. Fönstret **skapa ett nytt projekt med unity** *<version number>* visas.

   [@no__t – 1Create ett nytt projekt i Unity Hub](media/sdk/qs-csharp-unity-create-a-new-project.png)](media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. I **projekt namn**anger du **csharp-Unity**.
1. I **mallar**, om **3D** inte redan är markerat, markerar du det.
1. I **plats**väljer eller skapar du en mapp för att spara projektet i.
1. Välj **Skapa**.

Efter en stund visas fönstret Unity Editor.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Följ dessa steg om du vill installera tal-SDK för Unity:

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

1. Hämta och öppna [tal-SDK för](https://aka.ms/csspeech/unitypackage)enhets enhet, som paketeras som ett enhets paket för till gång (. unitypackage). När du öppnar till gångs paketet visas dialog rutan **Importera Unity-paket** .

   [dialog rutan @no__t 1Import union Package i Unity-redigeraren](media/sdk/qs-csharp-unity-01-import.png)](media/sdk/qs-csharp-unity-01-import.png#lightbox)
1. Se till att alla filer är markerade och välj **Importera**. Efter en liten stund importeras Unity Asset-paketet till projektet.

Mer information om hur du importerar till gångs paket i enighet finns i [Unity-dokumentationen](https://docs.unity3d.com/Manual/AssetPackages.html).

## <a name="add-ui"></a>Lägga till användargränssnitt

Nu ska vi lägga till ett minimalt gränssnitt i vår scen. Det här gränssnittet består av en knapp som utlöser tal igenkänning och ett textfält som visar resultatet. I fönstret [ **hierarki** ](https://docs.unity3d.com/Manual/Hierarchy.html)visas ett exempel på en scen som har skapats med det nya projektet.

1. Längst upp i **hierarkin** väljer du **skapa** > **UI** > -**knapp**.

   Den här åtgärden skapar tre spel objekt som du kan se i **hierarkifönstret** : ett **knapp** objekt, ett **arbets ytans** objekt som innehåller knappen och ett **Event Events** -objekt.

   [![Unity redigerings miljö](media/sdk/qs-csharp-unity-editor-window.png)](media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigera i vyn **scen** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) så att du har en lämplig vy över arbets ytan och knappen i vyn [ **scen** ](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. I fönstret [ **kontrollant** ](https://docs.unity3d.com/Manual/UsingTheInspector.html) (som standard till höger) anger du egenskaperna för **POS X** -och **POS Y** till **0**, så knappen centreras i mitten av arbets ytan.

1. I fönstret **hierarki** väljer du **skapa** > **UI** > **text** för att skapa ett **text** objekt.

1. I fönstret **kontrollant** ställer du in egenskaperna **för POS X** och **pos Y** på **0** och **120**och anger egenskaperna för **Bredd** och **höjd** till **240** och **120**. Dessa värden ser till att textfältet och knappen inte överlappar varandra.

När du är klar bör **scenen** se ut ungefär som den här skärm bilden:

[@no__t 1Scene-vyn i Unity-redigeraren](media/sdk/qs-csharp-unity-02-ui-inline.png)](media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

Följ dessa steg om du vill lägga till exempel skript koden för Unity-projektet:

1. I [projekt fönstret](https://docs.unity3d.com/Manual/ProjectView.html)väljer du **skapa** >  **C# -skript** för att lägga till C# ett nytt skript.

   [![Project-fönstret i Unity-redigeraren](media/sdk/qs-csharp-unity-project-window.png)](media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Ge skriptet namnet `HelloWorld`.

1. Dubbelklicka på `HelloWorld` om du vill redigera det nyligen skapade skriptet.

   > [!NOTE]
   > Om du vill konfigurera kod redigeraren så att den används av Unit för redigering väljer du **redigera** > **Inställningar**och går sedan till inställningarna för **externa verktyg** . Mer information finns i [användar handboken för Unity](https://docs.unity3d.com/Manual/Preferences.html).

1. Ersätt det befintliga skriptet med följande kod:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. Sök efter och ersätt strängen `YourSubscriptionKey` med din prenumerations nyckel för tal tjänster.

1. Sök efter och ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i skriptet.

Gå tillbaka till Unity-redigeraren och Lägg till skriptet som en komponent till ett av dina spel objekt:

1. I fönstret **hierarki** väljer du objektet **arbets yta** .

1. I fönstret **kontrollant** väljer du knappen **Lägg till komponent** .

   [![Inspector-fönstret i Unity-redigeraren](media/sdk/qs-csharp-unity-inspector-window.png)](media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. I list rutan söker du efter det `HelloWorld`-skript som vi skapade ovan och lägger till det. Ett **Hello World (skript)** visas i fönstret **kontrollant** , som visar två oinitierade egenskaper, **utdata-text** och **knappen starta åters**. Egenskaperna för den här enhets komponenten matchar de offentliga egenskaperna för klassen `HelloWorld`.

1. Välj objekt väljaren för **knappen starta åters** (den lilla cirkel ikonen till höger om egenskapen) och välj det **knapp** objekt som du skapade tidigare.

1. Välj objekt väljar egenskapen **utgående text** och välj **textobjektet som du** skapade tidigare.

   > [!NOTE]
   > Knappen har även ett kapslat textobjekt. Se till att du inte av misstag väljer att använda text utdata (eller byta namn på ett text objekt med fältet **namn** i fönstret **kontrollant** för att undvika förvirring).

## <a name="run-the-application-in-the-unity-editor"></a>Köra programmet i Unity Editor

Nu är du redo att köra programmet i Unity-redigeraren.

1. I verktygsfältet för Unity-redigeraren (under meny raden) väljer du knappen **spela upp** (en högerriktad triangel).

1. Gå till [ **spel** läge](https://docs.unity3d.com/Manual/GameView.html)och vänta tills **textobjektet** visar **knappen Klicka för att känna igen tal**. (Den **nya texten** visas när programmet inte har startats eller inte är klart att svara.)

1. Välj knappen och tala om en engelsk fras eller mening till datorns mikrofon. Ditt tal överförs till tal tjänsterna och skrivs till text som visas i vyn **spel** .

   [@no__t 1Game-vyn i Unity-redigeraren](media/sdk/qs-csharp-unity-03-output-inline.png)](media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. Kontrol lera [ **konsol** fönstret](https://docs.unity3d.com/Manual/Console.html) för fel söknings meddelanden. Om **konsol** fönstret inte visas går du till meny raden och väljer **fönster** > **Allmänt** > -**konsolen** för att visa den.

1. När du är klar med att känna igen tal väljer du knappen **spela upp** i verktygsfältet Unity Editor för att stoppa programmet.

## <a name="additional-options-to-run-this-application"></a>Ytterligare alternativ för att köra det här programmet

Det här programmet kan också distribueras till som en Android-app, en fristående Windows-app eller ett UWP-program.
Mer information finns i vår [exempel databas](https://aka.ms/csspeech/samples). I mappen `quickstart/csharp-unity` beskrivs konfigurationen för dessa ytterligare mål.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Träna en modell för Custom Speech](how-to-custom-speech-train-model.md)
