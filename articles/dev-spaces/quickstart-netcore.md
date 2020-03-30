---
title: 'Felsöka och iterera på Kubernetes: Visual Studio-kod & .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Den här snabbstarten visar hur du använder Azure Dev Spaces och Visual Studio Code för att felsöka och snabbt iterera ett .NET Core-program på Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, servicenät, routning av tjänstnät, kubectl, k8s
manager: gwallace
ms.openlocfilehash: b38562879fa67d7ee82e3251ea2fcaa57a2075d6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240215"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>Snabbstart: Felsökning och iterera på Kubernetes: Visual Studio-kod och .NET Core - Azure Dev Spaces

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Iterativt utveckla kod i behållare med Visual Studio Code.
- Felsök koden i ditt utvecklingsutrymme från Visual Studio Code.

Med Azure Dev Spaces kan du också felsöka och iterera med:
- [Java- och Visual Studio-kod](quickstart-java.md)
- [Nod.js och Visual Studio-kod](quickstart-nodejs.md)
- [.NET Core och Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration. Om du inte har någon, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Visual Studio-kod installerad](https://code.visualstudio.com/download).
- [Azure Dev Spaces-](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) och [C#-tilläggen](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio-kod har installerats.
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes-tjänstkluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Kommandona nedan skapar en resursgrupp som heter *MyResourceGroup* och ett AKS-kluster som heter *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev Spaces i AKS-klustret

Använd `use-dev-spaces` kommandot för att aktivera dev spaces i AKS-klustret och följ anvisningarna. Kommandot nedan aktiverar Dev Spaces i *MyAKS-klustret* i gruppen MyResourceGroup och skapar ett *standardutrymme* för dev.The below command enables Dev Spaces on the MyAKS cluster in the *MyResourceGroup group* and creates a default dev space.

> [!NOTE]
> Kommandot `use-dev-spaces` kommer också att installera Azure Dev Spaces CLI om det inte redan är installerat. Du kan inte installera AZURE Dev Spaces CLI i Azure Cloud Shell.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

```output
'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Hämta exempelprogramkod

I den här artikeln använder du [exempelprogrammet Azure Dev Spaces](https://github.com/Azure/dev-spaces) för att demonstrera med Azure Dev Spaces.

Klona programmet från GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Förbereda exempelprogrammet i Visual Studio-kod

Öppna Visual Studio-kod, klicka på *Arkiv* och sedan *Öppna...*, navigera till *dev-spaces/samples/dotnetcore/getting-started/webfrontend-katalogen* och klicka på *Öppna*.

Du har nu *webfrontend-projektet* öppet i Visual Studio Code. Om du vill köra programmet i ditt utvecklingsutrymme genererar du docker- och Helm-diagramresurserna med azure dev spaces-tillägget i kommandopaletten.

Om du vill öppna kommandopaletten i Visual Studio-kod klickar du på *Visa* och sedan *kommandopalett*. Börja skriva `Azure Dev Spaces` och `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`klicka på .

![Förbereda konfigurationsfiler för Azure Dev Spaces](./media/common/command-palette.png)

När Visual Studio Code också uppmanar dig att `Yes` konfigurera den offentliga slutpunkten väljer du att aktivera en offentlig slutpunkt.

![Välj offentlig slutpunkt](media/common/select-public-endpoint.png)

Det här kommandot förbereder ditt projekt för att köras i Azure Dev Spaces genom att generera ett Dockerfile- och Helm-diagram. Det genererar också en *VSCODE-katalog* med felsökningskonfiguration i roten av projektet.

> [!TIP]
> [Dockerfile- och Helm-diagrammet](how-dev-spaces-works-prep.md#prepare-your-code) för projektet används av Azure Dev Spaces för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet byggs och kördes.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Skapa och kör kod i Kubernetes från Visual Studio Code

Klicka på *debug* ikonen till vänster och klicka på *.NET Core Launch (AZDS)* högst upp.

![](media/get-started-netcore/debug-configuration.png)

Det här kommandot skapar och kör din tjänst i Azure Dev Spaces i felsökningsläge. *Terminalfönstret* längst ned visar utdata och URL:er för din tjänst som körs i Azure Dev Spaces. *Felsökningskonsolen* visar loggutdata.

> [!Note]
> Om du inte ser några Azure Dev Spaces-kommandon i *kommandopaletten*kontrollerar du att du har installerat [Tillägget Visual Studio-kod för Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Kontrollera också att du har öppnat *katalogen dev-spaces/samples/dotnetcore/getting-started/webfrontend* i Visual Studio Code.

Du kan se tjänsten som körs genom att öppna den offentliga webbadressen.

> [!Note]
> Inledningsvis kan den offentliga webbadressen visa ett *fel i den felaktiga gatewayen.* Vänta några sekunder innan du uppdaterar webbsidan, och du bör se din tjänst körs.

Klicka på *Felsöka* och stoppa *felsökning* för att stoppa felsökningen.

## <a name="update-code"></a>Uppdatera kod

Om du vill distribuera en uppdaterad version av tjänsten kan du uppdatera alla filer i projektet och köra *om .NET Core Launch (AZDS)*. Ett exempel:

1. Om programmet fortfarande körs klickar du på *Felsök* sedan *Stoppa felsökning för* att stoppa det.
1. Uppdatera [linje 22 `Controllers/HomeController.cs` till:](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22)
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. Spara ändringarna.
1. Kör *om .NET Core Launch (AZDS)*.
1. Navigera till din tjänst som körs och klicka på *Om*.
1. Observera dina ändringar.
1. Klicka på *Felsöka* och stoppa *felsökning* för att stoppa ditt program.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ställa in och använda brytpunkter för felsökning

Starta tjänsten i felsökningsläge med *.NET Core Launch (AZDS)*.

Navigera tillbaka till *Utforskarvyn* genom att klicka på *Visa* och sedan *Explorer*. Öppna `Controllers/HomeController.cs` och klicka någonstans på linje 22 för att sätta markören där. Om du vill ange en brytpunkt hit *F9* eller klicka på *Felsök* sedan *Växla brytpunkt*.

Öppna din tjänst i en webbläsare och märker att inget meddelande visas. Återgå till Visual Studio-kod och observera linje 20 är markerad. Brytpunkten som du anger har pausat tjänsten på rad 20. Om du vill återuppta tjänsten trycker du på *F5* eller klickar på *Felsök* sedan *Fortsätt*. Gå tillbaka till din webbläsare och lägg märke till att meddelandet nu visas.

När du kör tjänsten i Kubernetes med en felsökare bifogad, har du full tillgång till felsökningsinformation som anropsstacken, lokala variabler och undantagsinformation.

Ta bort brytpunkten genom att sätta markören på linje 22 i `Controllers/HomeController.cs` och trycka på *F9*.

## <a name="update-code-from-visual-studio-code"></a>Uppdatera kod från Visual Studio-kod

När tjänsten körs i felsökningsläge uppdaterar du linje `Controllers/HomeController.cs`22 i . Ett exempel:

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

Spara filen. Klicka på *Felsökning* och starta *om felsökning* eller klicka på knappen Starta *om felsökning* i *verktygsfältet Felsökning.*

![](media/common/debug-action-refresh.png)

Öppna tjänsten i en webbläsare och lägg märke till att det uppdaterade meddelandet visas.

I stället för att återskapa och distribuera om en ny behållaravbildning varje gång kodredigeringar görs, kompilerar Azure Dev Spaces stegvis om koden i den befintliga behållaren för att tillhandahålla en snabbare redigerings-/felsökningsloop.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen. 

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
