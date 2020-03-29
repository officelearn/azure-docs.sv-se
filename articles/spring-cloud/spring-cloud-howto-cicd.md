---
title: CI/CD för Azure Spring Cloud
description: CI/CD för Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278518"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD för Azure Spring Cloud

Kontinuerlig integrering och kontinuerliga leveransverktyg gör det möjligt för utvecklare att snabbt distribuera uppdateringar till befintliga program med minimal ansträngning och risk. Azure DevOps hjälper dig att organisera och kontrollera dessa nyckeljobb. Azure Spring Cloud erbjuder för närvarande inte en specifik Azure DevOps-plugin.  Du kan dock integrera dina Spring Cloud-program med DevOps med hjälp av en [Azure CLI-uppgift](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Den här artikeln visar hur du använder en Azure CLI-uppgift med Azure Spring Cloud för att integrera med Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Skapa en Azure Resource Manager-tjänstanslutning

Läs [den här artikeln](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) om du vill lära dig hur du skapar en Azure Resource Manager-tjänstanslutning till ditt Azure DevOps-projekt. Var noga med att välja samma prenumeration som du använder för din Azure Spring Cloud-tjänstinstans.

## <a name="azure-cli-task-templates"></a>Azure CLI-uppgiftsmallar

### <a name="deploy-artifacts"></a>Distribuera artefakter

Du kan skapa och distribuera dina `tasks`projekt med hjälp av en serie . Det här kodavsnittet definierar först en Maven-uppgift för att skapa programmet, följt av en andra uppgift som distribuerar JAR-filen med Azure Spring Cloud Azure CLI-tillägget.

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

Det är möjligt att distribuera direkt till Azure utan ett separat byggsteg.

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
