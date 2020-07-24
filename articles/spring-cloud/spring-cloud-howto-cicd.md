---
title: CI/CD för Azure våren Cloud
description: CI/CD för Azure våren Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 3e392cee1269294e3a01cfc1b24e3fee9ddbc0d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037499"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD för Azure våren Cloud

Med kontinuerlig integrering och kontinuerliga leverans verktyg kan utvecklare snabbt distribuera uppdateringar till befintliga program med minimal ansträngning och risk. Azure DevOps hjälper dig att organisera och kontrol lera dessa viktiga jobb. För närvarande erbjuder Azure våren Cloud inget angivet Azure DevOps-plugin-program.  Du kan dock integrera dina våren Cloud-program med DevOps med hjälp av en [Azure CLI-uppgift](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Den här artikeln visar hur du använder en Azure CLI-aktivitet med Azure våren Cloud för att integrera med Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Skapa en Azure Resource Manager tjänst anslutning

Läs [den här artikeln](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) för att lära dig hur du skapar en Azure Resource Manager tjänst anslutning till ditt Azure DevOps-projekt. Se till att välja samma prenumeration som du använder för din Azure våren Cloud Service-instans.

## <a name="azure-cli-task-templates"></a>Mallar för Azure CLI-aktiviteter

### <a name="deploy-artifacts"></a>Distribuera artefakter

Du kan bygga och distribuera dina projekt med en serie `tasks` . Det här kodfragmentet definierar först en maven-uppgift för att bygga programmet, följt av en andra aktivitet som distribuerar JAR-filen med Azures moln för Azure CLI-tillägget.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Distribuera från källa

Det går att distribuera direkt till Azure utan ett separat build-steg.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
