---
title: 'Felsöka och iterera på Kubernetes: Visual Studio Code & Java'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Den här snabb starten visar hur du använder Azure dev Spaces och Visual Studio Code för att felsöka och snabbt iterera ett Java-program i Azure Kubernetes-tjänsten
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, behållare, Java, Helm, service nät, service nät-routning, kubectl, K8s
manager: gwallace
ms.openlocfilehash: 8ceb48bf60438442b63fab698091fdb5064793af
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025205"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-with-visual-studio-code-and-java---azure-dev-spaces"></a>Snabb start: Felsöka och iterera på Kubernetes med Visual Studio Code och Java-Azure dev Spaces

I den här snabb starten ställer du in Azure dev Spaces med ett hanterat Kubernetes-kluster och använder en Java-app i Visual Studio Code för att skapa och felsöka kod i behållare iterativt. Med Azure dev Spaces kan du felsöka och testa alla komponenter i ditt program i Azure Kubernetes service (AKS) med minimal utvecklings maskin installation. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Java Development Kit (JDK) 1.8.0 +](https://aka.ms/azure-jdks).
- [Maven 3.5.0 +](https://maven.apache.org/download.cgi).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) och [Java-felsökning för Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds) -tillägg för Visual Studio Code.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Följande kommandon skapar en resurs grupp med namnet *MyResourceGroup* och ett AKS-kluster som kallas *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure dev Spaces i ditt AKS-kluster

Använd kommandot `use-dev-spaces` för att aktivera dev Spaces i ditt AKS-kluster och följa anvisningarna. Följande kommando aktiverar dev Spaces i *MyAKS* -klustret i gruppen *MyResourceGroup* och skapar ett *standard* dev-utrymme.

> [!NOTE]
> Kommandot `use-dev-spaces` installerar även Azure dev Spaces CLI om det inte redan är installerat. Du kan inte installera Azure dev Spaces CLI i Azure Cloud Shell.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS


'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>Hämta exempel program kod

I den här artikeln använder du [exempel programmet Azure dev Spaces](https://github.com/Azure/dev-spaces) för att demonstrera användningen av Azure dev Spaces.

Klona programmet från GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Förbereda exempel programmet i Visual Studio Code

Öppna Visual Studio Code, Välj **Arkiv** och sedan **Öppna**, navigera till katalogen *dev-Spaces/samples/Java/kom igång/webfrontend* och välj **Öppna**.

Du har nu öppnat *webfrontend* -projektet i Visual Studio Code. Om du vill köra programmet i ditt dev-utrymme genererar du Docker-och Helm-diagrammets till gångar med tillägget Azure dev Spaces i paletten Command.

Om du vill öppna kommando-paletten i Visual Studio Code väljer du **Visa** och sedan **kommando-palett**. Börja skriva `Azure Dev Spaces` och välj **Azure dev Spaces: Förbered konfigurationsfiler för Azure dev Spaces**.

![Förbereda konfigurationsfiler för Azure dev Spaces](./media/common/command-palette.png)

När Visual Studio Code uppmanas du också att konfigurera dina bas avbildningar, exponerade portar och offentliga slut punkter, välja `Azul Zulu OpenJDK for Azure (Free LTS)` för bas avbildningen, `8080` för exponerad port och `Yes` för att aktivera en offentlig slut punkt.

![Välj en bas avbildning](media/get-started-java/select-base-image.png)

![Välj exponerad port](media/get-started-java/select-exposed-port.png)

![Välj offentlig slut punkt](media/get-started-java/select-public-endpoint.png)

Det här kommandot förbereder projektet att köras i Azure dev Spaces genom att generera ett Dockerfile-och Helm-diagram. Den genererar också en *. VSCode* -katalog med fel söknings konfiguration i roten för ditt projekt.

> [!TIP]
> [Dockerfile-och Helm-diagrammet](how-dev-spaces-works.md#prepare-your-code) för ditt projekt används av Azure dev Spaces för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet skapas och körs.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Skapa och kör kod i Kubernetes från Visual Studio Code

Välj ikonen **Felsök** till vänster och välj **starta Java-program (AZDS)** överst.

![Starta Java-program](media/get-started-java/debug-configuration.png)

Det här kommandot skapar och kör tjänsten i Azure dev Spaces. **Terminalfönstret** längst ned visar Bygg utdata och URL: er för din tjänst som kör Azure dev Spaces. **Fel söknings konsolen** visar loggens utdata.

> [!Note]
> Om du inte ser några Azure dev Spaces-kommandon i **paletten**, kontrol lera att du har installerat [Visual Studio Code-tillägget för Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Kontrol lera också att du *har öppnat katalogen dev-Spaces/samples/Java/Hämta/webfrontend* i Visual Studio Code.

Du kan se den tjänst som körs genom att öppna den offentliga URL: en.

Välj **Felsök** och **stoppa** fel sökningen för att stoppa fel söknings programmet.

## <a name="update-code"></a>Uppdatera kod

Om du vill distribuera en uppdaterad version av tjänsten kan du uppdatera alla filer i projektet och köra **starta Java-program (AZDS)** igen. Ett exempel:

1. Om programmet fortfarande körs väljer du **Felsök** och stoppar sedan **fel sökningen** för att stoppa det.
1. Uppdatera [rad 19 i `src/main/java/com/ms/sample/webfrontend/Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) till:
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Spara ändringarna.
1. Kör om **starta Java-program (AZDS)** .
1. Navigera till den tjänst som körs och observera dina ändringar.
1. Välj **Felsök** och **stoppa fel sökningen** för att stoppa programmet.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Inställning och användning av Bryt punkter för fel sökning

Starta tjänsten med **starta Java-program (AZDS)** . Detta kör också tjänsten i fel söknings läge.

Gå tillbaka till **Explorer** -vyn genom att välja **Visa** och sedan **Utforskaren**. Öppna *src/main/Java/com/MS/Sample/webfrontend/Application. java* och klicka någonstans på rad 19 för att placera markören där. Om du vill ange en Bryt punkt trycker du på **F9** eller väljer **Felsök** och sedan **Växla Bryt punkt**.

Öppna din tjänst i en webbläsare och Observera att inget meddelande visas. Gå tillbaka till Visual Studio Code och Observera att rad 19 är markerad. Den Bryt punkt som du har angett har pausat tjänsten på rad 19. Om du vill återuppta tjänsten trycker du på **F5** eller väljer **Felsök** och sedan **Fortsätt**. Gå tillbaka till webbläsaren och Observera att meddelandet visas nu.

När du kör tjänsten i Kubernetes med en fel sökare ansluten har du fullständig åtkomst till felsöknings information som anrops stack, lokala variabler och undantags information.

Ta bort Bryt punkten genom att placera markören på rad 19 i *src/main/Java/com/MS/Sample/webfrontend/Application. java* och trycka på **F9**.

## <a name="update-code-from-visual-studio-code"></a>Uppdatera kod från Visual Studio Code

När tjänsten körs i fel söknings läge uppdaterar du rad 19 i *src/main/Java/com/MS/Sample/webfrontend/Application. java*. Ett exempel:
```java
return "Hello from webfrontend in Azure while debugging!";
```

Spara filen. Välj **Felsök** och **starta om fel sökning** eller klicka på knappen **starta om fel sökning** i **verktygsfältet Felsök**.

![Uppdatera fel sökning](media/common/debug-action-refresh.png)

Öppna din tjänst i en webbläsare och Observera att det uppdaterade meddelandet visas.

I stället för att bygga om och omdistribuera en ny behållar avbildning varje tids kods redigering görs, kompilerar Azure dev Spaces stegvis om koden i den befintliga behållaren för att ge en snabbare redigerings-/fel söknings slinga.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure dev Spaces hjälper dig att utveckla mer komplexa program över flera behållare och hur du kan förenkla samarbets utveckling genom att arbeta med olika versioner eller grenar av koden i olika utrymmen.

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-java.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
