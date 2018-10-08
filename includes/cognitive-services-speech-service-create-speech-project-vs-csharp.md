---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454580"
---
1. Starta Visual Studio 2017.
 
1. Se till att arbetsbelastningen **.NET-skrivbordsmiljö** är tillgänglig. Välj **Verktyg** \> **Hämta verktyg och funktioner** på menyraden i Visual Studio för att öppna installationsprogrammet för Visual Studio. Om den här arbetsbelastningen redan är aktiverad stänger du dialogrutan. 

    Annars markerar du kryssrutan bredvid **.NET-skrivbordsutveckling** och klickar på knappen **Ändra** nere till höger i dialogrutan. Det tar en stund att installera den nya funktionen.

    ![Aktivera .NET-skrivbordsutveckling](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Skapa en ny Visual C#-konsolapp. I dialogrutan **Nytt projekt** expanderar du **Installerade** \> **Visual C#** \> **Windows Desktop** i den vänstra rutan och väljer sedan **Konsolapp (.NET Framework)**. Som projektnamn anger du *helloworld*.

    ![Skapa Visual C#-konsolapp (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Skapa Visual C#-konsolapp (.NET Framework)")

1. Installera och referera till [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj **Hantera NuGet-paket för lösningen**.

    ![Högerklicka på Hantera NuGet-paket för lösningen](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Hantera NuGet-paket för lösningen")

1. I fältet **Paketkälla** uppe till höger väljer du **nuget.org**. Sök efter paketet `Microsoft.CognitiveServices.Speech` och installera det i projektet **helloworld**.

    ![Installera NuGet-paketet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Installera NuGet-paket")

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

    ![Acceptera licensvillkoren](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Acceptera licensvillkoren")

    När paketet har installerats visas en bekräftelse i Package Manager-konsolen.

1. Skapa en plattformskonfiguration som matchar din datorarkitektur i Configuration Manager. Välj **Skapa** > **Configuration Manager**.

    ![Starta Configuration Manager](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Starta Configuration Manager")

1. Lägg till en ny plattform i dialogrutan **Configuration Manager**. I listrutan **Aktiv lösningsplattform** väljer du **Ny**.

    ![Lägg till en ny plattform under Configuration Manager-fönstret](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Lägg till en ny plattform under Configuration Manager-fönstret")

1. Om du kör 64-bitars Windows skapar du en ny plattformskonfiguration med namnet `x64`. Om du kör 32-bitars Windows skapar du en ny plattformskonfiguration med namnet `x86`.

    ![I 64-bitars Windows lägger du till en ny plattform med namnet ”x64”](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Lägg till plattformen x64")


