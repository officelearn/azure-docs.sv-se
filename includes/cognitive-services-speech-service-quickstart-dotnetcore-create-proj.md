---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: d5b95c8d71cf3d4830a2fe5eb6442ef479c9fab6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91654462"
---
1. Starta Visual Studio 2019.

1. Kontrollera att arbetsbelastningen ** .NET cross-platform development** (Plattformsoberoende .NET-utveckling) är tillgänglig. Välj **verktyg**  >  **Hämta verktyg och funktioner** från meny raden i Visual Studio för att öppna Visual Studio Installer. Om den här arbetsbelastningen redan är aktiverad stänger du dialogrutan.

   ![Skärmbild av Visual Studio-installationsprogrammet med fliken Arbetsbelastningar markerad](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   Annars markerar du rutan bredvid **.NET Core cross-platform development** (Plattformsoberoende .NET-utveckling) och väljer **Ändra** i det nedre högra hörnet av dialogrutan. Installationen av den nya funktionen tar en stund.

1. Skapa en ny Visual C# .NET-konsolapp. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C#** > **.NET Core** i den vänstra rutan. Välj sedan **Console App (.NET Core)**. Som projektnamn anger du *helloworld*.

   ![Skärmbild av dialogrutan Nytt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Skapa Visual C#-konsol program (.NET Core)")

1. Installera och referera till [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget). I Solution Explorer högerklickar du på lösningen och väljer **Hantera NuGet-paket för lösningen**.

   ![Skärm bild av Solution Explorer med hantera NuGet-paket för lösning markerad](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Hantera NuGet-paket för lösningen")

1. I det övre högra hörnet i fältet **paket källa** väljer du **NuGet.org**. Sök efter `Microsoft.CognitiveServices.Speech` paketet och installera det i projektet **HelloWorld** .

   ![Skärm bild som visar dialog rutan "Hantera paket för lösning".](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installera NuGet-paket")

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

   ![Skärmbild av dialogrutan för att acceptera licensvillkoren](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Godkänn licensen")

När paketet har installerats visas en bekräftelse i Package Manager-konsolen.
