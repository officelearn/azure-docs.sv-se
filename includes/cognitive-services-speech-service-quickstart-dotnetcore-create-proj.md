---
author: erhopf
ms.service: cognitive-services
ms.topic: include
origin.date: 12/12/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 6e49db90aa9e9f933a190425afbafd15e0057fca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621137"
---
1. Starta Visual Studio 2017.

1. Kontrollera att arbetsbelastningen  **.NET cross-platform development** (Plattformsoberoende .NET-utveckling) är tillgänglig. Välj **Verktyg** > **Get Tools and Features** (Hämta verktyg och funktioner) på menyraden i Visual Studio för att öppna installationsprogrammet för Visual Studio. Om den här arbetsbelastningen redan är aktiverad stänger du dialogrutan.

   ![Skärmbild av Visual Studio-installationsprogrammet med fliken Arbetsbelastningar markerad](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Annars markerar du rutan bredvid **.NET Core cross-platform development** (Plattformsoberoende .NET-utveckling) och väljer **Ändra** i det nedre högra hörnet av dialogrutan. Installationen av den nya funktionen tar en stund.

1. Skapa en ny Visual C# .NET-konsolapp. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C#** > **.NET Core** i den vänstra rutan. Välj sedan **Console App (.NET Core)**. För projektnamnet anger du *helloworld*.

   ![Skärmbild av dialogrutan Nytt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Skapa Visual C#-konsolapp (.NET Core)")

1. Installera och referera till [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj sedan **Hantera NuGet-paket för lösningen**.

   ![Skärmbild av Solution Explorer, med alternativet Hantera NuGet-paket för lösningen markerat](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Hantera NuGet-paket för lösning")

1. I fältet för **paketkälla** uppe till höger väljer du **nuget.org**. Sök efter `Microsoft.CognitiveServices.Speech`-paketet och installera det i **helloworld**-projektet.

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installera NuGet-paket")

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

   ![Skärmbild av dialogrutan för att acceptera licensvillkoren](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Acceptera licensen")

När paketet har installerats visas en bekräftelse i Package Manager-konsolen.
