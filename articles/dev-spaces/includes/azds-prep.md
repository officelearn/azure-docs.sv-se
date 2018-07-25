---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991040"
---
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
