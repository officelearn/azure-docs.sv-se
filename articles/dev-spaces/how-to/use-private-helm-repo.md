---
title: Så här använder du en privat Helm-databas i Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Använd en privat Helm-databas i ett Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240474"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Använda en privat Helm-databas i Azure Dev Spaces

[Helm][helm] är paketansvarig för Kubernetes. Helm använder ett [diagramformat][helm-chart] för att paketera beroenden. Helm-diagram lagras i en databas som kan vara offentliga eller privata. Azure Dev Spaces hämtar bara Helm-diagram från offentliga databaser när du kör ditt program. I de fall där Helm-databasen är privat eller om Azure Dev Spaces inte kan komma åt den kan du lägga till ett diagram från databasen direkt i ditt program. Genom att lägga till diagrammet direkt kan Azure Dev Spaces köra ditt program utan att behöva komma åt den privata Helm-databasen.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Lägga till den privata Helm-databasen på din lokala dator

Använd [rodret repo add][helm-repo-add] och [helm repo uppdatering][helm-repo-update] för att komma åt den privata Helm-databasen från din lokala dator.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Lägga till diagrammet i programmet

Navigera till projektets katalog `azds prep`och kör .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> Kommandot `prep` försöker generera [ett Dockerfile- och Helm-diagram](../how-dev-spaces-works-prep.md#prepare-your-code) för projektet. Azure Dev Spaces använder dessa filer för att skapa och köra din kod, men du kan ändra dessa filer om du vill ändra hur projektet byggs och kördes.

Skapa en [requirements.yaml-fil][helm-requirements] med diagrammet i programmets diagramkatalog. Om ditt program till exempel heter *app1*skapar du *diagram/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navigera till programmets diagramkatalog och använd [uppdatering av helmberoende][helm-dependency-update] för att uppdatera Helm-beroendena för ditt program och hämta diagrammet från den privata databasen.

```cmd
helm dependency update
```

Kontrollera att en *underkatalog* med en *tgz-fil* har lagts till i programmets diagramkatalog. *Diagram/app1/charts/mychart-0.1.0.tgz*.

Diagrammet från din privata Helm-databas har hämtats och lagts till i projektet. Ta bort *filen requirements.yaml* så att Dev Spaces inte försöker uppdatera det här beroendet.

## <a name="run-your-application"></a>Köra ditt program

Navigera till projektets rotkatalog `azds up` och kör för att verifiera att programmet körs i ditt utvecklingsutrymme.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Helm och hur det fungerar][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
