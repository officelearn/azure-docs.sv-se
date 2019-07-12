---
title: Utveckla med Node.js på Kubernetes med Azure Dev blanksteg
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: Snabb Kubernetes-utveckling med behållare, mikrotjänster och Node.js i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 3da6c015d46d2c83dd74c625e1e8eeaee81da2ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707131"
---
# <a name="quickstart-develop-with-nodejs-on-kubernetes-using-azure-dev-spaces"></a>Snabbstart: Utveckla med Node.js på Kubernetes med Azure Dev blanksteg

I den här guiden får du lära dig hur du:

- Ställa in Azure Dev Spaces med ett hanterat Kubernetes-kluster i Azure.
- Utveckla iterativt koden i behållare med hjälp av Visual Studio Code.
- Felsöka kod i ditt dev-adressutrymmet från Visual Studio Code.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har en Azure-prenumeration, kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free).
- [Visual Studio Code installerat](https://code.visualstudio.com/download).
- Den [Azure Dev blanksteg](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) tillägget för Visual Studio Code installerat.
- [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Skapa ett Azure Kubernetes Service-kluster

Du måste skapa ett AKS-kluster i en [region som stöds][supported-regions]. Den kommandona nedan skapar du en resursgrupp med namnet *MyResourceGroup* och ett AKS-kluster som heter *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Aktivera Azure Dev blanksteg på AKS-kluster

Använd den `use-dev-spaces` kommando för att aktivera Dev blanksteg på AKS-klustret och följa anvisningarna. I nedanstående kommando aktiverar Dev blanksteg på den *MyAKS* -kluster i den *MyResourceGroup* gruppen och skapar en *standard* dev utrymme.

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

## <a name="get-sample-application-code"></a>Exempelkod för program

I den här artikeln använder du den [Azure Dev blanksteg exempelprogrammet](https://github.com/Azure/dev-spaces) att visa hur man använder Azure Dev blanksteg.

Klona programmet från GitHub.

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Förbereda exempelprogrammet i Visual Studio Code

Öppna Visual Studio Code, klicka på *filen* sedan *öppen...* , navigera till den *dev-blanksteg/samples/nodejs/komma-igång/webfrontend* directory klicka sedan på *öppna*.

Nu har du den *webfrontend* projekt som är öppna i Visual Studio Code. Generera Docker och Helm-diagram tillgångarna med tillägget Azure Dev blanksteg i kommandot Pallette för att köra programmet i dev-utrymmet.

Klicka för att öppna Kommandopaletten i Visual Studio Code *visa* sedan *Kommandopaletten*. Börja skriva `Azure Dev Spaces` och klicka på `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

![Förbereda konfigurationsfiler för Azure Dev blanksteg](./media/common/command-palette.png)

När Visual Studio Code uppmanar dig att konfigurera din offentliga slutpunkt, välja `Yes` att aktivera en offentlig slutpunkt.

![Välj offentlig slutpunkt](media/common/select-public-endpoint.png)

Det här kommandot förbereder ditt projekt för att köra i Azure Dev blanksteg genom att generera ett Dockerfile och Helm-diagram. Det genererar även en *.vscode* katalogen med felsökning av konfigurationen i roten av projektet.

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Skapa och köra kod i Kubernetes från Visual Studio Code

Klicka på den *felsöka* ikonen till vänster och klicka på *starta Server (AZDS)* högst upp.

![Starta Server](media/get-started-node/debug-configuration-nodejs.png)

Det här kommandot bygger och kör din tjänst i Azure Dev blanksteg. Den *Terminal* fönstret längst ned på sidan visar resultatet och URL: er för tjänsten som kör Azure Dev blanksteg. Den *Felsökningskonsolen* visar loggutdata.

> [!Note]
> Om du inte ser några blanksteg för utveckling av Azure-kommandon i den *Kommandopaletten*, kontrollera att du har installerat den [Visual Studio Code-tillägg för Azure Dev blanksteg](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Kontrollera också att du har öppnat den *dev-blanksteg/samples/nodejs/komma-igång/webfrontend* katalogen i Visual Studio Code.

Du kan se att tjänsten körs genom att öppna en offentlig URL.

Klicka på *felsöka* sedan *stoppa felsökning* att stoppa felsökningen.

## <a name="update-code"></a>Uppdatera kod

För att distribuera en uppdaterad version av din tjänst måste du uppdatera alla filer i projektet och kör *starta Server*. Exempel:

1. Om ditt program körs klickar du på *felsöka* sedan *stoppa felsökning* att stoppa den.
1. Uppdatera [rad 13 i `server.js` ](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) till:
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. Spara ändringarna.
1. Kör *starta Server*.
1. Navigera till tjänsten som körs och se dina ändringar.
1. Klicka på *felsöka* sedan *stoppa felsökning* att stoppa programmet.

## <a name="setting-and-using-breakpoints-for-debugging"></a>Ställa in och använda brytpunkter för felsökning

Starta din tjänst med hjälp av *starta Server (AZDS)* .

Gå tillbaka till den *Explorer* vyn genom att klicka på *visa* sedan *Explorer*. Öppna `server.js` och klicka någonstans på rad 13 att placera markören där. Ange en brytpunkt når *F9* eller klicka på *felsöka* sedan */Radera brytpunkt*.

Öppna din tjänst i en webbläsare och Observera visas inget meddelande. Gå tillbaka till Visual Studio Code och notera rad 13 markeras. Brytpunkt som du angett har pausats tjänsten på rad 13. Om du vill återuppta tjänsten når *F5* eller klicka på *felsöka* sedan *Fortsätt*. Gå tillbaka till webbläsaren och Observera meddelandet visas nu.

När du kör din tjänst i Kubernetes ett internt, har du fullständig åtkomst till felsökningsinformation som anropsstacken, lokala variabler och undantagsinformation.

Ta bort brytpunkten genom att placera markören på rad 13 i `server.js` och träffa *F9*.

Klicka på *felsöka* sedan *stoppa felsökning* att stoppa felsökningen.

## <a name="update-code-from-visual-studio-code"></a>Uppdatera kod från Visual Studio Code

Ändra felsökningsläge till *ansluta till en Server (AZDS)* och starta tjänsten:

![](media/get-started-node/attach-nodejs.png)

Det här kommandot bygger och kör din tjänst i Azure Dev blanksteg. Den startas också en [nodemon](https://nodemon.io) bearbeta i din tjänst behållare och bifogar VS Code till den. Den *nodemon* processen ger automatiska omstarter när källan kodändringar görs, så att snabbare inre slingan utveckling liknar utveckling i din lokala dator.

När tjänsten har startat, navigera till den med hjälp av din webbläsare och interagera med den.

När tjänsten körs, gå tillbaka till VS Code och uppdatera rad 13 i `server.js`. Exempel:
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

Spara filen och återgå till din tjänst i en webbläsare. Interagera med tjänsten och Observera uppdaterade meddelandet visas.

När du kör *nodemon*, nod-processen startas om automatiskt så fort kodändringar identifieras. Den här processen för automatisk omstart liknar upplevelsen av att redigera och starta om tjänsten på den lokala datorn, vilket ger en inre slingan utvecklingsupplevelse.

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur Azure Dev blanksteg hjälper dig att utveckla mer komplexa program över flera behållare och du kan förenkla samarbetsfunktioner utveckling genom att arbeta med olika versioner eller grenar av din kod i olika blanksteg.

> [!div class="nextstepaction"]
> [Arbeta med flera containrar och utveckling i team](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations