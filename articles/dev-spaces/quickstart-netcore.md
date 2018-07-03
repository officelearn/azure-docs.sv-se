---
title: Skapa ett Kubernetes dev utrymme i molnet | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: ghogen
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: ghogen
ms.date: 06/06/2018
ms.topic: quickstart
description: Snabb Kubernetes-utveckling med behållare och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
manager: douge
ms.openlocfilehash: 21b94544105f55cbb8cb77c28d8c546ffcf7f8c0
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945864"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-net-core-and-vs-code"></a>Snabbstart: Skapa ett Kubernetes dev-utrymme med Azure Dev Spaces (.NET Core och VS Code)

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Utveckla kod iterativt i behållare med VS Code och kommandoraden.
- Felsök koden i ditt dev-utrymme från VS Code

> [!Note]
> **Om du fastnar** du kan när som helst referera till avsnittet [Felsökning](troubleshooting.md) eller lägga upp en kommentar på den här sidan. Du kan också försöka med den mer ingående [självstudien](get-started-netcore.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- Ett [Kubernetes-kluster](https://ms.portal.azure.com/#create/microsoft.aks) som kör Kubernetes 1.9.6, i regionerna Usa, östra, Europa, västra eller Kanada, östra med **Http-programroutning** aktiverat.

  ![Se till att aktivera HTTP-programroutning.](media/common/Kubernetes-Create-Cluster-3.PNG)

- [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="set-up-azure-dev-spaces"></a>Konfigurera Azure Dev Spaces

1. Installera [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) (version 2.0.38 eller högre).
1. Ställ in Dev Spaces på ditt AKS-kluster: `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Ladda ned [Azure Dev Spaces-tillägget](https://aka.ms/get-azds-code) för VS Code.
1. Installera tillägget: `code --install-extension path-to-downloaded-extension/azds-0.1.1.vsix`

## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes

1. Hämta exempelkoden från GitHub: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Ändra katalogen till mappen webfrontend: `cd dev-spaces/samples/dotnetcore/getting-started/webfrontend`
1. Skapa Docker- och Helm-diagramtillgångar: `azds prep --public`
1. Skapa och kör din kod i AKS. I terminalfönstret från **webfrontend-mappen** kör du det här kommandot: `azds up`
1. Skanna konsolens utdata för information om den URL som skapades av `up`-kommandot. Den kommer att vara i formen: 

   `Service 'webfrontend' port 'http' is available at <url>` 

   Öppna webbadressen i ett webbläsarfönster. Du bör nu se hur webbappen läses in. 
   
   > [!Note]
   > Första gången det körs kan det ta några minuter innan DNS är redo. Om den offentliga URL:en inte fungerar kan du använda den alternativa http://localhost:<portnumber>-URL:en som visas i konsolens utdata. Om du använder localhost-URL:en kan det verka som om behållaren körs lokalt, men i själva verket körs den i AKS. För enkelhetens skull och för att underlätta interaktionen med tjänsten från den lokala datorn skapar Azure Dev Spaces en tillfällig SSH-tunnel för behållaren som körs i Azure. Du kan komma tillbaka och testa den offentliga URL:en senare när DNS-posten är färdig.

### <a name="update-a-content-file"></a>Uppdatera en innehållsfil

1. Leta upp en fil som `./Views/Home/Index.cshtml` och gör en ändring i HTML-koden. Ändra till exempel rad 70, `<h2>Application uses</h2>`, till något som: `<h2>Hello k8s in Azure!</h2>`
1. Spara filen. Efter en liten stund visas ett meddelande i terminalfönstret som meddelar att en fil i den aktiva behållaren har uppdaterats.
1. Gå till webbläsaren och uppdatera sidan. Den uppdaterade HTML-koden bör visas på webbsidan.

Vad hände? Redigering av innehållsfiler som HTML och CSS kräver inte omkompilering i en .NET Core-webbapp. Ett aktivt `azds up`-kommando synkroniserar automatiskt ändrade innehållsfiler i behållaren som körs i Azure, så att du genast kan se dina innehållsändringar.

### <a name="update-a-code-file"></a>Uppdatera en kodfil
Uppdateringar av kodfiler kräver lite mer arbete eftersom .NET Core-appar måste återskapas och skapa uppdaterade binärfiler för programmet.

1. Tryck på `Ctrl+C` (för att stoppa `azds up`) i terminalfönstret.
1. Öppna kodfilen `Controllers/HomeController.cs` och ändra meddelandet som ska visas på sidan Om: `ViewData["Message"] = "Your application description page.";`
1. Spara filen.
1. Kör `azds up` i terminalfönstret. 

Det här kommandot återskapar behållaravbildningen och distribuerar Helm-diagrammet på nytt. Bekräfta kodändringarna i det aktiva programmet genom att gå till menyn Om i webbappen.

Det finns dock en ännu *snabbare kodutvecklingsmetod*, som vi ska titta närmare på i nästa avsnitt. 

## <a name="debug-a-container-in-kubernetes"></a>Felsöka en behållare i Kubernetes

I det här avsnittet ska du använda VS Code för att direkt felsöka din behållare som körs i Azure. Du får också lära dig hur du kan få en snabbare redigera-kör-test-loop.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initiera felsökningstillgångar med VS Code-tillägget
Först måste du konfigurera ditt kodprojekt så att VS Code kommunicerar med utvecklarmiljön i Azure. VS Code-tillägget för Azure Dev Spaces har ett hjälpkommando för att konfigurera felsökningskonfigurationen. 

Öppna **Kommandopaletten** (med hjälp av menyn **Visa | Kommandopalett**) och använd automatisk komplettering för att ange och välja det här kommandot: `Azure Dev Spaces: Create configuration files for connected development`. 

Då läggs felsökningskonfigurationen för Azure Dev Spaces till under mappen `.vscode`.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Välj AZDS-felsökningskonfigurationen
1. Du öppnar felsökningsvyn genom att klicka på felsökningsikonen i **aktivitetsfältet** längs kanten i VS Code.
1. Välj **.NET Core Launch (AZDS)** (.NET Core-start (AZDS)) som den aktiva felsökningskonfigurationen.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Om du inte ser några Azure Dev Spaces-kommandon på kommandopaletten kontrollerar du att du har installerat VS Code-tillägget för Azure Dev Spaces. Kontrollera att arbetsytan som du öppnade i VS Code är mappen som innehåller azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Felsöka behållaren i Kubernetes
Tryck på **F5** för att felsöka koden i Kubernetes.

Precis som med `up`-kommandot, synkroniseras koden med utvecklarmiljön och en behållare skapas och distribueras till Kubernetes. Men den här gången är felsökaren kopplad till fjärrbehållaren.

> [!Tip]
> Statusfältet i VS Code innehåller en klickbar URL.

Lägg till en brytpunkt i en kodfil på serversidan, t.ex. i funktionen `Index()` i källfilen `Controllers/HomeController.cs`. Brytpunkten aktiveras när du uppdaterar sidan i webbläsaren.

Du har fullständig åtkomst till felsökningsinformation precis som när koden körs lokalt, t.ex. anropsstack, lokala variabler, undantagsinformation och så vidare.

### <a name="edit-code-and-refresh"></a>Redigera kod och uppdatera
Gör en kodändring när felsökaren är aktiv. Ändra till exempel meddelandet på sidan Om i `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Spara filen och klicka på knappen **Uppdatera** i **fönstret Felsökningsåtgärder**. 

![](media/get-started-netcore/debug-action-refresh.png)

I stället för att återskapa och distribuera om en ny behållaravbildning varje gång koden ändras, vilket ofta tar lång tid, kompilerar Azure Dev Spaces om koden inkrementellt i befintliga behållaren för snabbare redigerings- och felsökningsförlopp.

Uppdatera webbappen i webbläsaren och gå till sidan Om. Nu bör ditt anpassade meddelande visas i användargränssnittet.

**Nu vet du hur du snabbt kan arbeta med kod och felsöka direkt i Kubernetes!**

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure Dev Spaces hjälper dig utveckla mer komplexa appar över flera behållare och hur du kan förenkla utveckling i samarbete genom att arbeta med olika versioner eller grenar av kod i olika utrymmen. 

> [!div class="nextstepaction"]
> [Arbeta med flera behållare och utveckling i team](get-started-netcore.md#call-a-service-running-in-a-separate-container)