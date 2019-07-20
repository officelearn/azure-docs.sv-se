---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362629"
---
1. Öppna Visual Studio 2019.

1. I fönstret Starta väljer du **skapa ett nytt projekt**. 

1. Välj **konsol program (.NET Framework)** och välj sedan **Nästa**.

1. I **projekt namn**anger `helloworld`du och väljer sedan **skapa**.

1. I meny raden i Visual Studio väljer du **verktyg** > **Hämta verktyg och funktioner**och kontrollerar om arbets belastningen **.net Desktop Development** är tillgänglig. Om arbets belastningen inte har installerats markerar du kryss rutan och väljer sedan **ändra** för att starta installationen. Det kan ta några minuter att ladda ned och installera.

   Om kryssrutan bredvid **.NET-skrivbordsutveckling** är markerad kan du stänga dialogrutan nu.

   ![Aktivera .NET-skrivbordsutveckling](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

Nästa steg är att installera NuGet- [paketet för tal-SDK](https://aka.ms/csspeech/nuget)så att du kan referera till det i koden.

1. I Solution Explorer högerklickar du på `helloworld`och väljer **Hantera NuGet-paket** för att Visa NuGet Package Manager.

   ![NuGet Package Manager](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Leta upp List rutan **paket källa** i det övre högra hörnet och kontrol lera att **NuGet.org** är markerat.

1. I det övre vänstra hörnet väljer du **Bläddra**.

1. I rutan Sök skriver `Microsoft.CognitiveServices.Speech` du Package och trycker på RETUR.

1. Välj `Microsoft.CognitiveServices.Speech`och välj sedan **Installera** för att installera den senaste stabila versionen.

   ![Installera Microsoft. CognitiveServices. Speech NuGet-paketet](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Acceptera alla avtal och licenser för att starta installationen.

   När paketet har installerats visas en bekräftelse i fönstret **Package Manager-konsol** .

Skapa och kör konsol programmet nu genom att skapa en plattforms konfiguration som matchar datorns arkitektur.

1. Välj **build** > **Configuration Manager**på Meny raden. Den **Configuration Manager** dialogrutan visas.

   ![Configuration Manager dialog ruta](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. I list rutan **aktiv lösnings plattform** väljer du **ny**. Dialog rutan **ny lösnings plattform** visas.

1. I list rutan **Skriv eller Välj ny plattform** :
   - Om du kör 64-bitars Windows väljer du **x64**.
   - Om du kör 32-bitars Windows väljer du **x86**.

1. Välj **OK** och sedan **Stäng**.
