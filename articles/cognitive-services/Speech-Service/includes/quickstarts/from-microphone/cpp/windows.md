---
title: 'Snabb start: identifiera tal från en mikrofon C++ , (Windows) – tal-service'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar tal i C++ på Windows Desktop med hjälp av Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: 416c2893ebf07cd638f3f1c06c709db2586245d1
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925477"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=windows)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande kodfragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med **regions-ID** : t från den [region](https://aka.ms/speech/sdkregion) som är associerad med din prenumeration (till exempel `westus` för den kostnads fria utvärderings prenumerationen).

1. Välj **arkiv** > **Spara alla**på Meny raden.

> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**).

1. Säg en engelsk fras eller en mening. Programmet skickar din röst till tal tjänsten, som översätter till text och skickar tillbaka den till programmet för visning.

   ![Konsol utdata efter lyckad igenkänning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]