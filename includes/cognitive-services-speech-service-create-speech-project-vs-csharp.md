---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71327061"
---
Om du vill skapa ett Visual Studio-projekt för Windows-utveckling måste du skapa projektet, konfigurera Visual Studio för .NET-skrivbordsutveckling, installera Speech SDK och välja målarkitektur.

### <a name="create-the-project-and-add-the-workload"></a>Skapa projektet och lägg till arbetsbelastningen

Starta, skapa projektet i Visual Studio och se till att Visual Studio är konfigurerat för .NET-skrivbordsutveckling:

1. Öppna Visual Studio 2019.

1. Välj Skapa ett **nytt projekt**i startfönstret . 

1. I fönstret **Skapa ett nytt projekt** väljer du **Konsolapp (.NET Framework)** och väljer sedan **Nästa**.

1. I fönstret **Konfigurera det nya projektet** anger du *helloworld* i **Projektnamn**, väljer eller skapar katalogsökvägen på **Plats**och väljer sedan **Skapa**.

1. På menyraden i Visual Studio väljer du **Verktyg** > **hämta verktyg och funktioner**, som öppnar Visual Studio Installer och visar dialogrutan **Ändra.**

1. Kontrollera om **.NET-arbetsbelastningen för skrivbordsutveckling** är tillgänglig. Om arbetsbelastningen inte har installerats markerar du kryssrutan bredvid den och väljer sedan **Ändra** för att starta installationen. Det kan ta några minuter att ladda ned och installera.

   Om kryssrutan bredvid **.NET-skrivbordsutveckling** redan är markerad väljer du **Stäng** för att avsluta dialogrutan.

   ![Aktivera .NET-skrivbordsutveckling](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Stäng Visual Studio Installer.

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Nästa steg är att installera [Paketet Speech SDK NuGet](https://aka.ms/csspeech/nuget), så att du kan referera till det i koden.

1. Högerklicka på **helloworld-projektet** i Solution Explorer och välj sedan **Hantera NuGet-paket** för att visa NuGet Package Manager.

   ![NuGet-pakethanteraren](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Leta reda på den övre högra hörnet i listrutan **Paketkälla** och se till att **nuget.org** är markerat.

1. I det övre vänstra hörnet väljer du **Bläddra**.

1. Skriv *Microsoft.CognitiveServices.Speech* i sökrutan och välj **Retur**.

1. Välj paketet **Microsoft.CognitiveServices.Speech** i sökresultaten och välj sedan **Installera** för att installera den senaste stabila versionen.

   ![Installera paketet Microsoft.CognitiveServices.SpeechGet](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Acceptera alla avtal och licenser för att starta installationen.

   När paketet har installerats visas en bekräftelse i fönstret **Package Manager Console.**

### <a name="choose-the-target-architecture"></a>Välj målarkitektur

Nu, för att bygga och köra konsolprogrammet, skapa en plattformskonfiguration som matchar datorns arkitektur.

1. Välj **Bygg** > **configuration manager**på menyraden . Dialogrutan **Configuration Manager** visas.

   ![Dialogrutan Configuration Manager](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Välj **Nytt**i listrutan **Aktiv lösningsplattform** . Dialogrutan **Ny lösningsplattform** visas.

1. I listrutan **Typ eller välj den nya plattformen:**
   - Om du kör 64-bitars Windows väljer du **x64**.
   - Om du kör 32-bitars Windows väljer du **x86**.

1. Välj **OK** och **stäng**sedan .
