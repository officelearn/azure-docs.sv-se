---
title: 'Snabbstart: Plattformsinstallation för Tal-SDK för .NET Framework (Windows) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden om du vill konfigurera plattformen för C# under .NET Framework för Windows med taltjänsten SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: a09b969ee3e11aeb04f338cf035b21b5da9bd952
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925605"
---
Den här guiden visar hur du installerar [Tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för .NET Framework (Windows). Om du bara vill att paketnamnet ska `Install-Package Microsoft.CognitiveServices.Speech` komma igång på egen hand kör du i NuGet-konsolen.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Skapa ett Visual Studio-projekt och installera Speech SDK

Du måste installera [Paketet Speech SDK NuGet](https://aka.ms/csspeech/nuget) så att du kan referera till det i koden. För att göra det kan du först behöva skapa ett **helloworld-projekt.** Om du redan har ett projekt med **.NET-arbetsbelastningen för skrivbordsutveckling** tillgänglig kan du använda det projektet och hoppa till [Använd NuGet Package Manager för att installera Tal-SDK](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>Skapa helloworld-projekt

1. Öppna Visual Studio 2019.

1. Välj Skapa ett **nytt projekt**i startfönstret . 

1. I fönstret **Skapa ett nytt projekt** väljer du **Konsolapp (.NET Framework)** och väljer sedan **Nästa**.

1. I fönstret **Konfigurera det nya projektet** anger du *helloworld* i **Projektnamn**, väljer eller skapar katalogsökvägen på **Plats**och väljer sedan **Skapa**.

1. På menyraden i Visual Studio väljer du **Verktyg** > **hämta verktyg och funktioner**, som öppnar Visual Studio Installer och visar dialogrutan **Ändra.**

1. Kontrollera om **.NET-arbetsbelastningen för skrivbordsutveckling** är tillgänglig. Om arbetsbelastningen inte har installerats markerar du kryssrutan bredvid den och väljer sedan **Ändra** för att starta installationen. Det kan ta några minuter att ladda ned och installera.

   Om kryssrutan bredvid **.NET-skrivbordsutveckling** redan är markerad väljer du **Stäng** för att avsluta dialogrutan.

   ![Aktivera .NET-skrivbordsutveckling](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Stäng Visual Studio Installer.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>Använd NuGet Package Manager för att installera Tal-SDK

1. Högerklicka på **helloworld-projektet** i Solution Explorer och välj sedan **Hantera NuGet-paket** för att visa NuGet Package Manager.

   ![NuGet-pakethanteraren](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Leta reda på den övre högra hörnet i listrutan **Paketkälla** och se till att den **`nuget.org`** är markerad.

1. I det övre vänstra hörnet väljer du **Bläddra**.

1. Skriv *Microsoft.CognitiveServices.Speech* i sökrutan och välj **Retur**.

1. Välj paketet **Microsoft.CognitiveServices.Speech** i sökresultaten och välj sedan **Installera** för att installera den senaste stabila versionen.

   ![Installera paketet Microsoft.CognitiveServices.SpeechGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Acceptera alla avtal och licenser för att starta installationen.

   När paketet har installerats visas en bekräftelse i fönstret **Package Manager Console.**

### <a name="choose-target-architecture"></a>Välj målarkitektur

Skapa en plattformskonfiguration som matchar datorns arkitektur om du vill skapa och köra konsolprogrammet.

1. Välj **Bygg** > **configuration manager**på menyraden . Dialogrutan **Configuration Manager** visas.

   ![Dialogrutan Configuration Manager](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Välj **Nytt**i listrutan **Aktiv lösningsplattform** . Dialogrutan **Ny lösningsplattform** visas.

1. I listrutan **Typ eller välj den nya plattformen:**
   - Om du kör 64-bitars Windows väljer du **x64**.
   - Om du kör 32-bitars Windows väljer du **x86**.

1. Välj **OK** och **stäng**sedan .

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
