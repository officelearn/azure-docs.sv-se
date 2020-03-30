---
title: 'Felsöka och iterera på Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Den här snabbstarten visar hur du använder Azure Dev Spaces och Visual Studio Code för att felsöka och snabbt iterera ett Java-program på Azure Kubernetes Service
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Java, Helm, servicenät, routning av servicenät, kubectl, k8s
manager: gwallace
ms.openlocfilehash: ac7a1b37b565f3589b7c049a3c1ed2a84972ded0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239736"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Snabbstart: Felsöka och iterera på Kubernetes med Visual Studio-kod och Java - Azure Dev Spaces

I den här snabbstarten konfigurerar du Azure Dev Spaces med ett hanterat Kubernetes-kluster och använder en Java-app i Visual Studio-kod för att iterativt utveckla och felsöka kod i behållare. Med Azure Dev Spaces kan du felsöka och testa alla komponenter i ditt program i Azure Kubernetes Service (AKS) med minimal konfiguration av utvecklingsdatorn. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 1.8.0+](https://aka.ms/azure-jdks).
- [Maven 3.5.0+](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) och Java [Debugger för Azure Dev Spaces-tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) för Visual Studio-kod.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes-tjänstkluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Följande kommandon skapar en resursgrupp med namnet *MyResourceGroup* och ett AKS-kluster som heter *MyAKS*.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev Spaces i AKS-klustret

Använd `use-dev-spaces` kommandot för att aktivera dev spaces i AKS-klustret och följ anvisningarna. Följande kommando aktiverar Dev Spaces i *MyAKS-klustret* i gruppen *MyResourceGroup* och skapar ett *standardutrymme* för utvecklare.

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

Öppna Visual Studio-kod, välj **Arkiv** och **öppna**, navigera till *katalogen dev-spaces/samples/java/getting-started/webfrontend* och välj **Öppna**.

Du har nu *webfrontend-projektet* öppet i Visual Studio Code. Om du vill köra programmet i ditt utvecklingsutrymme genererar du docker- och Helm-diagramresurserna med azure dev spaces-tillägget i kommandopaletten.

Om du vill öppna kommandopaletten i Visual Studio-kod väljer du **Visa** och sedan **kommandopalett**. Börja skriva `Azure Dev Spaces` och välj **Azure Dev Spaces: Förbered konfigurationsfiler för Azure Dev Spaces**.

![Förbereda konfigurationsfiler för Azure Dev Spaces](./media/common/command-palette.png)

När Visual Studio Code också uppmanar dig att konfigurera basavbildningar, exponerade portar och offentliga slutpunkter väljer du `Azul Zulu OpenJDK for Azure (Free LTS)` basavbildningen, `8080` för den exponerade porten och `Yes` aktiverar en offentlig slutpunkt.

![Välj basbild](media/get-started-java/select-base-image.png)

![Välj exponerad port](media/get-started-java/select-exposed-port.png)

![Välj offentlig slutpunkt](media/get-started-java/select-public-endpoint.png)

Det här kommandot förbereder ditt projekt för att köras i Azure Dev Spaces genom att generera ett Dockerfile- och Helm-diagram. Det genererar också en *VSCODE-katalog* med felsökningskonfiguration i roten av projektet.

> [!TIP]
> [Dockerfile- och Helm-diagrammet](how-dev-spaces-works-prep.md#prepare-your-code) för projektet används av Azure Dev Spaces för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet byggs och körs.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Skapa och kör kod i Kubernetes från Visual Studio Code

Välj **ikonen Felsökning** till vänster och välj **Starta Java-programmet (AZDS)** högst upp.

![Starta Java-programmet](media/get-started-java/debug-configuration.png)

Det här kommandot skapar och kör din tjänst i Azure Dev Spaces. **Terminalfönstret** längst ned visar utdata och URL:er för tjänsten som kör Azure Dev Spaces. **Felsökningskonsolen** visar loggutdata.

> [!Note]
> Om du inte ser några Azure Dev Spaces-kommandon i **kommandopaletten**kontrollerar du att du har installerat [Tillägget Visual Studio-kod för Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Kontrollera också att du har öppnat *dev-spaces/samples/java/getting-started/webfrontend-katalogen* i Visual Studio Code.

Du kan se tjänsten som körs genom att öppna den offentliga webbadressen.

Välj **Felsökning** och stoppa felsökning **för** att stoppa felsökningen.

## <a name="update-code"></a>Uppdatera kod

Om du vill distribuera en uppdaterad version av tjänsten kan du uppdatera alla filer i projektet och köra **om Starta Java-programmet (AZDS).** Ett exempel:

1. Om ditt program fortfarande körs väljer du **Felsökning** och sedan **Stoppa felsökning för** att stoppa det.
1. Uppdatera [linje 19 `src/main/java/com/ms/sample/webfrontend/Application.java` till:](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19)
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Spara ändringarna.
1. Kör **starta Java-programmet (AZDS)**.
1. Navigera till din körtjänst och observera dina ändringar.
1. Välj **Felsökning** och stoppa felsökning **för** att stoppa ditt program.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ställa in och använda brytpunkter för felsökning

Starta tjänsten med **Launch Java Program (AZDS).** Detta kör också din tjänst i felsökningsläge.

Navigera tillbaka till **Utforskarvyn** genom att välja **Visa** sedan **Utforskaren**. Öppna *src / main/java/ com / ms / sample/webfrontend/Application.java* och klicka någonstans på linje 19 för att sätta markören där. Om du vill ange en brytpunkt trycker du på **F9** eller väljer **Felsök** sedan **Växla brytpunkt**.

Öppna din tjänst i en webbläsare och märker att inget meddelande visas. Återgå till Visual Studio Code och observera linje 19 är markerad. Brytpunkten som du anger har pausat tjänsten på rad 19. Om du vill återuppta tjänsten trycker du på **F5** eller väljer **Felsökning** och sedan **fortsätter**. Gå tillbaka till din webbläsare och lägg märke till att meddelandet nu visas.

När du kör tjänsten i Kubernetes med en felsökare bifogad, har du full tillgång till felsökningsinformation som anropsstacken, lokala variabler och undantagsinformation.

Ta bort brytpunkten genom att sätta markören på linje 19 i *src/main/java/com/ms/sample/webfrontend/Application.java* och trycka på **F9**.

## <a name="update-code-from-visual-studio-code"></a>Uppdatera kod från Visual Studio-kod

Medan tjänsten körs i felsökningsläge uppdaterar du linje 19 i *src/main/java/com/ms/sample/webfrontend/Application.java*. Ett exempel:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Spara filen. Välj **Felsökning och** starta sedan om **felsökning** eller välj knappen Starta **om felsökning** i **verktygsfältet Felsökning.**

![Uppdatera felsökning](media/common/debug-action-refresh.png)

Öppna tjänsten i en webbläsare och lägg märke till att det uppdaterade meddelandet visas.

I stället för att återskapa och distribuera om en ny behållaravbildning varje gång kodredigeringar görs, kompilerar Azure Dev Spaces stegvis om koden i den befintliga behållaren för att tillhandahålla en snabbare redigerings-/felsökningsloop.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure Dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbete genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
