---
title: 'Snabb start: Översätt tal till tal, C# (UWP) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: a0f62db319d54c2db71a86f621985a304dbbb4d2
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925714"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=uwp)

## <a name="add-sample-code"></a>Lägga till exempelkod

Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C# koden för bakomliggande implementering.

1. Öppna `MainPage.xaml`i **Solution Explorer**.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen (mellan `<Grid>` och `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. Öppna käll filen för bakomliggande kod `MainPage.xaml.cs`i **Solution Explorer**. (Den grupperas under `MainPage.xaml`.)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. Leta upp strängen `YourSubscriptionKey`i `SpeechTranslationFromMicrophone_ButtonClicked` hanteraren i den här filen och ersätt den med din prenumerations nyckel.

1. Leta upp strängen `YourServiceRegion`i `SpeechTranslationFromMicrophone_ButtonClicked` hanteraren och ersätt den med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. (Använd till exempel `westus` för den kostnads fria prov prenumerationen.)

1. I meny raden väljer du **arkiv** > **Spara alla** för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP översättnings C# program i-snabb start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Välj **aktivera mikrofon**och när åtkomst behörighets förfrågan öppnas, Välj **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **Översätt tal från mikrofon inmatningen**och tala en engelsk fras eller mening i enhetens mikrofon. Programmet skickar din röst till tal tjänsten som översätter talet till text på ett annat språk (i det här fallet tyska). Tal tjänsten skickar den översatta texten tillbaka till programmet, som visar översättningen i fönstret.

   ![Användar gränssnitt för tal Översättning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
