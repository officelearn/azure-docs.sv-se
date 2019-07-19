---
title: Hur Visual Studio Code fungerar med Azure dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Hur Visual Studio Code fungerar med Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 0d80643b366b6d7313f24e73258056e492eb56fc
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297857"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Hur Visual Studio Code fungerar med Azure dev Spaces

Du kan använda Visual Studio Code och [tillägget Azure dev Spaces][azds-extension] för att förbereda, köra och felsöka dina tjänster med Azure dev Spaces. Med Visual Studio Code och tillägget Azure dev Spaces kan du:

* Generera till gångar för att köra och felsöka tjänster i AKS
* Köra Java-, Node. js-och .NET Core-tjänster i ett dev-utrymme
* Direkt felsöka Java, Node. js och .NET Core-tjänster som körs i ett dev-utrymme

## <a name="generate-assets"></a>Generera till gångar

Visual Studio Code och tillägget Azure dev Spaces genererar följande till gångar för ditt projekt:

* Dockerfiles för Java-program med maven-, Node. js-program och .NET Core-program
* Helm diagram för nästan alla språk med en Dockerfile
* En `azds.yaml` fil, som är [konfigurations filen för Azure dev Spaces][azds-yaml] för ditt projekt
* En `.vscode` mapp med Visual Studio Code start Configuration för ditt projekt för Java-program med hjälp av maven, Node. js-program och .net Core-program

Dockerfile, Helm-diagrammet och `azds.yaml` -filerna är samma till gångar som genereras när de körs. `azds prep` Dessa filer kan också användas utanför Visual Studio Code för att köra projektet i AKS, som att köra `azds up`. `.vscode` Mappen används endast av Visual Studio Code för att köra projektet i AKS från Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Kör din tjänst i AKS

När du har genererat till gångarna för ditt projekt kan du köra Java, Node. js och .NET Core-tjänster i ett befintligt dev-utrymme från Visual Studio Code. På sidan *fel sökning* i Visual Studio Code kan du anropa Starta-konfigurationen från `.vscode` katalogen för att köra projektet.

Du måste skapa ditt AKS-kluster och aktivera Azure dev Spaces i ditt kluster utanför Visual Studio Code. Du kan till exempel använda Azure CLI eller Azure Portal för att göra den här installationen. Du kan återanvända befintliga Dockerfiles, Helm-diagram `azds.yaml` och filer som skapats utanför Visual Studio Code, till exempel de till gångar som `azds prep`genererats genom att köra. Om du återanvänder till gångar som skapats utanför Visual Studio Code måste du ändå ha en `.vscode` katalog. Den `.vscode` här katalogen kan återskapas av Visual Studio Code och tillägget Azure dev Spaces och dina befintliga till gångar skrivs inte över.

För .net Core-projekt måste du ha installerat [ C# tillägget][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] samt [maven installerat och konfigurerat][maven] för att köra Java-tjänsten från Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Felsöka tjänsten i AKS

När du har lanserat ditt projekt kan du felsöka Java, Node. js och .NET Core-tjänster som körs i ett dev-utrymme direkt från Visual Studio Code. Start konfigurationen i `.vscode` katalogen ger ytterligare felsöknings information för att köra en tjänst med fel sökning aktiverat i ett dev-utrymme. Visual Studio Code bifogar också fel söknings processen i den behållare som körs i dina dev Spaces, så att du kan ange Bryt punkter, granska variabler och utföra andra fel söknings åtgärder.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Använda Visual Studio Code med Azure dev Spaces

Du kan se Visual Studio Code och tillägget Azure dev Spaces Working with Azure dev Spaces i följande snabb starter:

* [Utveckla med Java][quickstart-java]
* [Utveckla med .NET][quickstart-netcore]
* [Utveckla med Node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
