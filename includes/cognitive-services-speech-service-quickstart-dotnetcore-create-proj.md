---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66482375"
---
1. Starta Visual Studio 2019.

1. Kontrollera att arbetsbelastningen ** .NET cross-platform development** (Plattformsoberoende .NET-utveckling) är tillgänglig. Välj **Verktyg** > **Hämta verktyg och funktioner** på Menyraden i Visual Studio för att öppna Visual Studio-installationsprogrammet. Om den här arbetsbelastningen redan är aktiverad stänger du dialogrutan.

   ![Skärmbild av Visual Studio-installationsprogrammet med fliken Arbetsbelastningar markerad](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Annars markerar du rutan bredvid **.NET Core cross-platform development** (Plattformsoberoende .NET-utveckling) och väljer **Ändra** i det nedre högra hörnet av dialogrutan. Installationen av den nya funktionen tar en stund.

1. Skapa en ny Visual C# .NET-konsolapp. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C#** > **.NET Core** i den vänstra rutan. Välj sedan **Console App (.NET Core)**. Som projektnamn anger du *helloworld*.

   ![Skärmbild av dialogrutan Nytt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Skapa Visual C# Console App (.NET Core)")

1. Installera och referera till [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj **Hantera NuGet-paket för lösning**.

   ![Skärmbild av Solution Explorer, med Hantera NuGet-paket för lösning markerad](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Hantera NuGet-paket för lösning")

1. Välj **nuget.org**i fältet **Paketkälla** i det övre högra hörnet . Sök efter `Microsoft.CognitiveServices.Speech` paketet och installera det i **helloworld-projektet.**

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installera NuGet-paketet")

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

   ![Skärmbild av dialogrutan för att acceptera licensvillkoren](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Acceptera licensen")

När paketet har installerats visas en bekräftelse i Package Manager-konsolen.
