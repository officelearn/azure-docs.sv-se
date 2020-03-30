---
title: Så här fungerar Visual Studio-kod med Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Lär dig mer om hur Visual Studio Code och Azure Dev Spaces hjälper dig att felsöka och snabbt iterera dina Kubernetes-program
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240451"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Så här fungerar Visual Studio-kod med Azure Dev Spaces

Du kan använda Visual Studio-kod och [Azure Dev Spaces-tillägget][azds-extension] för att förbereda, köra och felsöka dina tjänster med Azure Dev Spaces. Med Visual Studio-kod och Azure Dev Spaces-tillägget kan du:

* Generera tillgångar för att köra och felsöka tjänster i AKS
* Kör dina Java-, Node.js- och .NET Core-tjänster i ett dev-utrymme
* Direkt felsöka java-, node.js- och .NET Core-tjänster som körs i ett utvecklingsutrymme

## <a name="generate-assets"></a>Generera tillgångar

Visual Studio Code och Azure Dev Spaces-tillägget genererar följande resurser för ditt projekt:

* Dockerfiler för Java-program med Maven-, Node.js-program och .NET Core-program
* Helm-diagram för nästan alla språk med en Dockerfile
* En `azds.yaml` fil, som är [konfigurationsfilen][azds-yaml] för Azure Dev Spaces för ditt projekt
* En `.vscode` mapp med lanseringskonfigurationen för Visual Studio-kod för ditt projekt för Java-program med Maven-, Node.js-program och .NET Core-program

Dockerfile-, Helm-diagrammet `azds.yaml` och filerna är samma `azds prep`tillgångar som genereras när de körs . Dessa filer kan också användas utanför Visual Studio-kod för att `azds up`köra projektet i AKS, till exempel köra . Mappen `.vscode` används endast av Visual Studio-kod för att köra projektet i AKS från Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Kör din tjänst i AKS

När du har genererat tillgångarna för projektet kan du köra dina Java-, Node.js- och .NET Core-tjänster i ett befintligt utvecklingsutrymme från Visual Studio Code. På *felsökningssidan* i Visual Studio Code kan du anropa `.vscode` startkonfigurationen från katalogen för att köra projektet.

Du måste skapa AKS-klustret och aktivera Azure Dev Spaces i klustret utanför Visual Studio Code. Du kan till exempel använda Azure CLI eller Azure-portalen för att göra den här konfigurationen. Du kan återanvända befintliga Dockerfiles, `azds.yaml` Helm-diagram och filer som skapats utanför `azds prep`Visual Studio-kod, till exempel de tillgångar som genereras av körningen . Om du återanvänder tillgångar som genereras utanför Visual Studio `.vscode` Code måste du fortfarande ha en katalog. Den `.vscode` här katalogen kan återskapas av Visual Studio-kod och Azure Dev Spaces-tillägget och kommer inte att skriva över dina befintliga tillgångar.

För .NET Core-projekt måste du ha [C#-tillägget][csharp-extension] installerat för att köra .NET-tjänsten från Visual Studio Code. Även för Java-projekt som använder Maven måste du ha [Java Debugger för Azure Dev Spaces-tillägget][java-extension] installerat samt [Maven installerat och konfigurerat][maven] för att köra din Java-tjänst från Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Felsöka din tjänst i AKS

När du har startat projektet kan du felsöka java-, node.js- och .NET Core-tjänsterna som körs i ett utvecklingsutrymme direkt från Visual Studio Code. Startkonfigurationen i `.vscode` katalogen innehåller ytterligare felsökningsinformation för att köra en tjänst med felsökning aktiverad i ett utvecklingsutrymme. Visual Studio-kod ansluter också till felsökningsprocessen i den löpbehållare i dev-utrymmena, så att du kan ställa in brytpunkter, inspektera variabler och utföra andra felsökningsåtgärder.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Använda Visual Studio-kod med Azure Dev Spaces

Du kan se Visual Studio-kod och Azure Dev Spaces-tillägget som arbetar med Azure Dev Spaces i följande snabbstarter:

* [Snabbt iterera och felsöka med Visual Studio Code och Java][quickstart-java]
* [Snabbt iterera och felsöka med Visual Studio-kod och .NET][quickstart-netcore]
* [Snabbt iterera och felsöka med Visual Studio-kod och Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
