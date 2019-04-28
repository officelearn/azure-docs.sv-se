---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619776"
---
1. Starta Visual Studio 2017.

1. På menyraden i Visual Studio, Välj **Verktyg > Hämta verktyg** och se till att den **.NET-skrivbordsutveckling** arbetsbelastningen är tillgänglig. Om arbetsbelastningen inte har installerats ska du markera kryssrutan och sedan klicka på **Ändra** för att starta installationen. Det kan ta några minuter att ladda ned och installera.

   Om kryssrutan bredvid **.NET-skrivbordsutveckling** är markerad kan du stänga dialogrutan nu.

   ![Aktivera .NET-skrivbordsutveckling](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Nu ska vi skapa ett projekt. På menyraden väljer du **Arkiv > Nytt > Projekt**. När dialogrutan visas expanderar du **Installerade > Visual C# > Windows Desktop** i den vänstra rutan och väljer **Konsolapp (.NET Framework)**. Namnge projektet *helloworld*.

    ![Skapa Visual C#-konsolapp (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Skapa Visual C#-konsolapp (.NET Framework)")

1. Nu när projektet är konfigurerat kan vi behöva installera [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget) och referera till det i vår kod. Leta upp Solution Explorer och högerklicka på helloworld. På samma meny väljer du **Hantera NuGet-paket...** .

   ![Högerklicka på Hantera NuGet-paket för lösningen](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Hantera NuGet-paket för lösningen")

1. I det övre högra hörnet av NuGet Package Manager letar du upp listrutan **Paketkälla** och se till att **nuget.org** har valts. Välj **Bläddra** och sök efter paketet `Microsoft.CognitiveServices.Speech` och installera den senaste stabila versionen.

   ![Installera NuGet-paketet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Installera NuGet-paket")

1. Acceptera alla avtal och licenser för att starta installationen.

   ![Acceptera licensvillkoren](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Acceptera licensvillkoren")

    När paketet har installerats visas en bekräftelse i Package Manager-konsolen.

1. Nästa steg är att skapa en plattformskonfiguration som matchar arkitekturen på den dator som du använder för att skapa och köra konsolprogrammet. På menyraden väljer du **Skapa** > **Configuration Manager...**.

    ![Starta Configuration Manager](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Starta Configuration Manager")

1. I dialogrutan **Configuration Manager** letar du upp listrutan **Aktiv lösningsplattform** och väljer **Ny**.

    ![Lägg till en ny plattform under Configuration Manager-fönstret](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Lägg till en ny plattform under Configuration Manager-fönstret")

1. Om du använder 64-bitars Windows när du uppmanas att **skriva in eller välja den nya plattformen** väljer du `x64`. Om du kör 32-bitars Windows väljer du `x86`. När du är klar klickar du på **OK**.

    ![I 64-bitars Windows lägger du till en ny plattform med namnet ”x64”](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Lägg till plattformen x64")
