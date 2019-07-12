---
title: Hur Visual Studio Code fungerar med Azure Dev blanksteg
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Hur Visual Studio Code fungerar med Azure Dev blanksteg
keywords: Azure Dev blanksteg, Dev blanksteg, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712155"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Hur Visual Studio Code fungerar med Azure Dev blanksteg

Du kan använda Visual Studio Code och [Azure Dev blanksteg tillägget][azds-extension] för att förbereda, kör och Felsök dina tjänster med Azure Dev blanksteg. Med Visual Studio Code och tillägget Azure Dev blanksteg kan du:

* Generera tillgångar för att köra och felsökning av tjänster i AKS
* Kör dina Java, Node.js och .NET Core-tjänster i en dev-utrymme
* Felsöka Java, Node.js och .NET Core-tjänster som körs i en dev-utrymme direkt

## <a name="generate-assets"></a>Generera tillgångar

Visual Studio Code och tillägget Azure Dev blanksteg genererar följande resurser för ditt projekt:

* Dockerfiles för Java-program med hjälp av Maven, Node.js-program och .NET Core-program
* Helm-diagram för nästan alla språk med en docker-fil
* En `azds.yaml` filen, vilket är den [Azure Dev blanksteg konfigurationsfilen][azds-yaml] för ditt projekt
* En `.vscode` mapp med Visual Studio Code starta konfigurationen av ditt projekt för Java-program med hjälp av Maven, Node.js-program och .NET Core-program

Dockerfile Helm-diagrammet och `azds.yaml` filerna är samma tillgångar genereras när du kör `azds prep`. Dessa filer kan också användas utanför Visual Studio code för att köra projektet i AKS, till exempel körs `azds up`. Den `.vscode` mappen används endast av Visual Studio code för att köra projektet i AKS från Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Kör din tjänst i AKS

När du har genererat tillgångarna för ditt projekt kan köra du dina Java, Node.js och .NET Core-tjänster i ett befintligt adressutrymme för utveckling från Visual Studio Code. I den *felsöka* sidan av Visual Studio Code kan du anropa starta konfigurationen från den `.vscode` directory för att köra projektet.

Du måste skapa ett AKS-kluster och aktivera Azure Dev blanksteg i klustret utanför Visual Studio Code. Du kan exempelvis använda Azure CLI eller Azure-portalen för att göra den här installationen. Du kan återanvända befintliga Dockerfiles, Helm-diagram och `azds.yaml` filer som skapas utanför Visual Studio Code, till exempel de resurser som skapas genom att köra `azds prep`. Om du Återanvänd tillgångar som skapats utanför Visual Studio Code du fortfarande behöver ha en `.vscode` directory. Detta `.vscode` directory kan återskapas genom Visual Studio code och tillägget Azure Dev blanksteg och kommer inte att skriva över dina befintliga tillgångar.

För .NET Core-projekt, måste du ha den [ C# tillägget][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] installerade samt [Maven installerat och konfigurerat][maven] att köra din Java tjänsten från Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Felsöka din tjänst i AKS

När du startar ditt projekt kan du felsöka dina Java, Node.js och .NET Core-tjänster som körs i en dev-utrymme direkt från Visual Studio Code. Starta konfigurationen i den `.vscode` directory innehåller ytterligare felsökningsinformation för att köra en tjänst med felsökning aktiverat i en dev-utrymme. Visual Studio Code bifogar också till debug-processen i behållaren som körs i ditt dev blanksteg, så att du kan ange break punkter, granska variabler och utföra andra åtgärder för felsökning.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Använda Visual Studio Code med Azure Dev blanksteg

Du kan se Visual Studio Code och tillägget Azure Dev blanksteg för att arbeta med Azure Dev blanksteg i följande snabbstarter:

* [Utveckla med Java][quickstart-java]
* [Utveckla med .NET][quickstart-netcore]
* [Utveckla med Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md