---
title: 'Snabb start: Översätt tal till tal, C# (UWP) – tal service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.author: jhakulin
ms.topic: include
ms.custom: devx-track-csharp
ms.openlocfilehash: a8262d8830f6a3297482429d88cc6152e1fdfd89
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926064"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Lägg till exempelkod

Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till koden för C#-bakomliggande implementering.

1. Öppna i **Solution Explorer** `MainPage.xaml` .

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen (mellan `<Grid>` och `</Grid>` ):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. Öppna käll filen bakomliggande kod i **Solution Explorer** `MainPage.xaml.cs` . (Det är grupperat under `MainPage.xaml` .)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. `SpeechTranslationFromMicrophone_ButtonClicked`Leta upp strängen i hanteraren i den här filen `YourSubscriptionKey` och ersätt den med din prenumerations nyckel.

1. `SpeechTranslationFromMicrophone_ButtonClicked`Leta upp strängen i hanteraren `YourServiceRegion` och ersätt den med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration.

1. Välj **Arkiv**  >  **Spara alla** på Meny raden för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge**  >  **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **Felsök**  >  **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP översättnings program i C# – snabb start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Välj **aktivera mikrofon**och när åtkomst behörighets förfrågan öppnas, Välj **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **Översätt tal från mikrofon inmatningen**och tala en engelsk fras eller mening i enhetens mikrofon. Programmet skickar din röst till tal tjänsten som översätter talet till text på ett annat språk (i det här fallet tyska). Tal tjänsten skickar den översatta texten tillbaka till programmet, som visar översättningen i fönstret.

   ![Användar gränssnitt för tal Översättning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
