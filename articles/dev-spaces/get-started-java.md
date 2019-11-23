---
title: 'Create a Kubernetes dev space: Visual Studio Code & Java'
services: azure-dev-spaces
author: stepro
ms.author: stephpr
ms.date: 09/26/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 1dc7005ac0d1cb520e4c0452196a2b47665f9b5c
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325836"
---
# <a name="create-a-kubernetes-dev-space-visual-studio-code-and-java-with-azure-dev-spaces"></a>Create a Kubernetes dev space: Visual Studio Code and Java with Azure Dev Spaces

I den här guiden får du lära dig hur du:

- Skapa en Kubernetes-baserad miljö i Azure som är optimerad för utveckling, en _utvecklarmiljö_.
- Utveckla kod iterativt i containrar med VS Code och kommandoraden.
- Effektivt utvecklar och testar din kod i en teammiljö.

> [!Note]
> **If you get stuck** at any time, see the [Troubleshooting](troubleshooting.md) section.

## <a name="install-the-azure-cli"></a>Installera Azure CLI
Azure Dev Spaces kräver minimal konfiguration av den lokala datorn. Merparten av utvecklarmiljöns konfiguration lagras i molnet och kan delas med andra användare. Börja genom att ladda ned och köra [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI
Logga in i Azure. Skriv in följande kommando i ett terminalfönster:

```cmd
az login
```

> [!Note]
> Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Om du har flera Azure-prenumerationer ...
Du kan visa dina prenumerationer genom att köra: 

```cmd
az account list
```
Leta upp den prenumeration som har `isDefault: true` i JSON-utdata.
Om det här inte är den prenumeration som du vill använda kan du ändra standardprenumerationen:

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Skapa ett Kubernetes-kluster som är aktiverat för Azure Dev Spaces

At the command prompt, create the resource group in a [region that supports Azure Dev Spaces][supported-regions].

```cmd
az group create --name MyResourceGroup --location <region>
```

Skapa ett Kubernetes-kluster med följande kommando:

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Det tar några minuter att skapa klustret.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurera ditt AKS-kluster för att använda Azure Dev Spaces

Ange följande Azure CLI-kommando med hjälp av den resursgrupp som innehåller ditt AKS-kluster och AKS-klusternamn. Kommandot konfigurerar ditt kluster med stöd för Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> Azure Dev Spaces-konfigurationsprocessen tar bort namnområdet `azds` i klustret, om det finns.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Använda Kubernetes-felsökning för VS Code
Avancerade funktioner som Kubernetes-felsökning är tillgängliga för .NET Core- och Node.js-utvecklare som använder VS Code.

1. Installera [VS Code](https://code.visualstudio.com/Download) om du inte redan har det.
1. Ladda ned och installera [VS Azure Dev Spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Klicka på Installera en gång på tilläggets Marketplace-sida och igen i VS Code. 

För att kunna felsöka Java-program med Azure Dev Spaces laddar du ned och installerar [Java-felsökaren för Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds)-tillägget för VS Code. Klicka på Installera en gång på tilläggets Marketplace-sida och igen i VS Code.

## <a name="create-a-web-app-running-in-a-container"></a>Skapa en webbapp som körs i en container

I det här avsnittet skapar du en Java-webbapp och kör den i en container i Kubernetes.

### <a name="create-a-java-web-app"></a>Skapa en Java-webbapp
Ladda ned koden från GitHub genom att gå till https://github.com/Azure/dev-spaces och välja **Klona eller Ladda ned** för att ladda ned GitHub-databasen till den lokala miljön. Koden för den här guiden finns i `samples/java/getting-started/webfrontend`.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Förbereda kod för Docker- och Kubernetes-utveckling
Hittills har du en grundläggande webbapp som kan köras lokalt. Du kommer nu använda den i en container genom att skapa tillgångar som definierar appens container och hur den kommer att distribueras till Kubernetes. Den här uppgiften är enkel att utföra med Azure Dev Spaces: 

1. Starta VS Code och öppna mappen `webfrontend`. (Du kan ignorera eventuella standarduppmaningar om att lägga till felsökningstillgångar eller återställa projektet.)
1. Öppna den integrerade terminalen i VS Code (med hjälp av menyn **Visa > Integrerade terminal**).
1. Kör det här kommandot (se till att **webfrontend** är din aktuella mapp):

    ```cmd
    azds prep --public
    ```

Kommandot `azds prep` i Azure CLI genererar Docker- och Kubernetes-tillgångar med standardinställningarna:
* `./Dockerfile` beskriver appens containeravbildning och hur källkoden byggs och körs i containern.
* Ett [Helm-diagram](https://docs.helm.sh) under `./charts/webfrontend` beskriver hur du distribuerar containern till Kubernetes.

För tillfället är det inte nödvändigt att förstå det fullständiga innehållet i dessa filer. Det är dock värt att påpeka att **samma Kubernetes- och Docker-konfiguration som kod-tillgångar kan användas från utveckling till produktion, vilket ger bättre konsekvens mellan olika miljöer.**
 
En fil med namnet `./azds.yaml` genereras även av kommandot `prep`, och det är konfigurationsfilen för Azure Dev Spaces. Den kompletterar Docker- och Kubernetes-artefakterna med ytterligare konfiguration som möjliggör en iterativ utvecklingsprocess i Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes
Nu kör vi vår kod! Kör det här kommandot från webbklientdelen **rotmappen för kod** i terminalfönstret:

```cmd
azds up
```

Håll ett öga på kommandots utdata – du kommer att se flera saker under förloppet:
- Källkoden synkroniseras till utvecklarmiljön i Azure.
- En containeravbildning skapas i Azure, enligt Docker-tillgångarna i kodmappen.
- Kubernetes-objekt som använder containeravbildningen enligt Helm-diagrammet i kodmappen skapas.
- Information om containerns slutpunkt(er) visas. I det här fallet förväntar vi oss en offentlig HTTP-URL.
- Förutsatt att ovanstående steg slutförs ordentligt kan du börja se `stdout`- (och `stderr`-) utdata när containern startas.

> [!Note]
> De här stegen tar längre tid första gången kommandot `up` körs, men efterföljande körningar bör gå snabbare.

### <a name="test-the-web-app"></a>Testa webbappen
Skanna konsolens utdata för information om den offentliga webbadressen som skapades av kommandot `up`. Den kommer att vara i formen: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Identify the public URL for the service in the output from the `up` command. It ends in `.azds.io`. In the above example, the public URL is `http://webfrontend.1234567890abcdef1234.eus.azds.io/`.

To see your web app, open the public URL in a browser. Also, notice `stdout` and `stderr` output is streamed to the *azds trace* terminal window as you interact with your web app. You'll also see tracking information for HTTP requests as they go through the system. This makes it easier for you to track complex multi-service calls during development. The instrumentation added by Dev Spaces provides this request tracking.

> [!Note]
> In addition to the public URL, you can use the alternative `http://localhost:<portnumber>` URL that is displayed in the console output. Om du använder localhost-URL:en kan det verka som om containern körs lokalt, men i själva verket körs den i AKS. Azure Dev Spaces uses Kubernetes *port-forward* functionality to map the localhost port to the container running in AKS. This facilitates interacting with the service from your local machine.

### <a name="update-a-content-file"></a>Uppdatera en innehållsfil
Azure Dev Spaces handlar om mer än att bara få kod att köra i Kubernetes – det handlar om att du snabbt och löpande kan se effekten av dina kodändringar i en Kubernetes-miljö i molnet.

1. Tryck på `Ctrl+C` (för att stoppa `azds up`) i terminalfönstret.
1. Open `src/main/java/com/ms/sample/webfrontend/Application.java`, and edit the greeting message on [line 19](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19):

    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. Spara filen.
1. Kör `azds up` i terminalfönstret.

Det här kommandot återskapar containeravbildningen och distribuerar Helm-diagrammet på nytt. Du kan enkelt se kodändringarna i det aktiva programmet genom att uppdatera webbläsaren.

Det finns dock en ännu *snabbare kodutvecklingsmetod*, som vi ska titta närmare på i nästa avsnitt. 

## <a name="debug-a-container-in-kubernetes"></a>Felsöka en container i Kubernetes

I det här avsnittet ska du använda VS Code för att direkt felsöka våra containrar som körs i Azure. Du får också lära dig hur du kan få en snabbare redigera-kör-test-loop.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Om du fastnar** du kan när som helst referera till avsnittet [Felsökning](troubleshooting.md) eller lägga upp en kommentar på den här sidan.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initiera felsökningstillgångar med VS Code-tillägget
Du måste först konfigurera kodprojektet så att VS Code kommunicerar med vår utvecklarmiljö i Azure. VS Code-tillägget för Azure Dev Spaces har ett hjälpkommando för att konfigurera felsökningskonfigurationen. 

Öppna **Kommandopaletten** (med hjälp av menyn **Visa | Kommandopalett**) och använd automatisk komplettering för att ange och välja det här kommandot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Då läggs felsökningskonfigurationen för Azure Dev Spaces till under mappen `.vscode`. Det här kommandot ska inte förväxlas med kommandot `azds prep` som konfigurerar projektet för distribution.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Välj AZDS-felsökningskonfigurationen
1. Du öppnar felsökningsvyn genom att klicka på felsökningsikonen i **aktivitetsfältet** längs kanten i VS Code.
1. Välj **Launch Java program (AZDS)** (Starta Java- program (AZDS)) som aktiv felsökningskonfiguration.

![](media/get-started-java/debug-configuration.png)

> [!Note]
> Om du inte ser några Azure Dev Spaces-kommandon på kommandopaletten kontrollerar du att du har installerat VS Code-tillägget för Azure Dev Spaces. Be sure the workspace you opened in VS Code is the folder that contains `azds.yaml`.

### <a name="debug-the-container-in-kubernetes"></a>Felsöka containern i Kubernetes
Tryck på **F5** för att felsöka koden i Kubernetes.

Precis som med `up`-kommandot, synkroniseras koden med utvecklarmiljön och en container skapas och distribueras till Kubernetes. Men den här gången är felsökaren kopplad till fjärrcontainern.

> [!Tip]
> The VS Code status bar will turn orange, indicating that the debugger is attached. It will also display a clickable URL, which you can use to open your application.

![](media/common/vscode-status-bar-url.png)

Lägg till en brytpunkt i en kodfil på serversidan, t.ex. i funktionen `greeting()` i källfilen `src/main/java/com/ms/sample/webfrontend/Application.java`. Brytpunkten aktiveras när du uppdaterar sidan i webbläsaren.

Du har fullständig åtkomst till felsökningsinformation precis som när koden körs lokalt, t.ex. anropsstack, lokala variabler, undantagsinformation och så vidare.

### <a name="edit-code-and-refresh"></a>Redigera kod och uppdatera
Gör en kodändring när felsökaren är aktiv. Du kan till exempel ändra hälsningen i `src/main/java/com/ms/sample/webfrontend/Application.java`. 

```java
public String greeting()
{
    return "I'm debugging Java code in Azure!";
}
```

Save the file, and in the **Debug actions pane**, click the **Restart** button.

![](media/common/debug-action-refresh.png)

I stället för att återskapa och distribuera om en ny containeravbildning varje gång koden ändras, vilket ofta tar lång tid, kompilerar Azure Dev Spaces om koden inkrementellt i befintliga containern för snabbare redigerings- och felsökningsförlopp.

Uppdatera webbappen i webbläsaren. Nu bör ditt anpassade meddelande visas i användargränssnittet.

**Nu vet du hur du snabbt kan arbeta med kod och felsöka direkt i Kubernetes!** Nu ska vi gå vidare och se hur du kan skapa och anropa en andra container.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om utveckling för flera tjänster](multi-service-java.md)


[supported-regions]: about.md#supported-regions-and-configurations
