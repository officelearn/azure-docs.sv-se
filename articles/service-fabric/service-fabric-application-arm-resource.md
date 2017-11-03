---
title: "Distribuera och uppgradera program och tjänster med Azure Resource Manager | Microsoft Docs"
description: "Lär dig hur du distribuerar program och tjänster till ett Service Fabric-kluster med en Azure Resource Manager-mall."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: dekapur
ms.openlocfilehash: 609183545ffcde20da7001ef4cf35183c750e181
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Hantera program och tjänster som Azure Resource Manager-resurser

Du kan distribuera program och tjänster till Service Fabric-kluster via Azure Resource Manager. Detta innebär att i stället för att distribuera och hantera program via PowerShell eller CLI efter att behöva vänta på att klustret ska bli klar, du kan nu express program och tjänster i JSON och distribuera dem i samma Resource Manager-mallen som klustret. Processen för programregistrering, etablering och distribuera alla sker i ett steg.

Detta är det rekommenderade sättet att distribuera alla installationsprogrammet, styrning eller kluster hanteringsprogram som du behöver i klustret. Detta inkluderar den [korrigering Orchestration programmet](service-fabric-patch-orchestration-application.md), Watchdogs eller program som måste köras i klustret innan andra program och tjänster distribueras. 

I förekommande fall kan du hantera dina program som hanteraren för filserverresurser resurser för att förbättra:
* Granskningslogg: Resource Manager granskningar varje åtgärd och bevarar en detaljerad *aktivitetsloggen* som kan hjälpa dig att spåra ändringar som görs till dessa program och klustret.
* Rollbaserad åtkomstkontroll (RBAC): hantera åtkomst till kluster samt program som distribuerats på klustret kan ske via samma Resource Manager-mallen.
* Azure Resource Manager (via Azure portal) blir en en hos för att hantera dina kluster och distribution av kritiska program.

Följande utdrag visar olika typer av resurser som kan hanteras via en mall:

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Lägg till ett nytt program till Resource Manager-mall

1. Förbered ditt kluster Resource Manager-mall för distribution. Se [skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) för mer information.
2. Tänk på att vissa program som du ska distribuera i klustret. Finns det något som kommer alltid att köra som andra program kan ta beroenden? Planerar du distribuera alla kluster styrning eller installationen program? Dessa typer av program som bäst hanteras via en Resource Manager-mall som beskrivs ovan. 
3. När du har förstått vilka program som du vill att distribuera det här sättet måste programmen paketeras, zippade och placeras på en filresurs. Resursen måste vara tillgängligt via REST-slutpunkt för Azure Resource Manager för att använda under distributionen.
4. Beskriv egenskaper för varje program i mallen Resource Manager under din kluster-deklaration. Dessa egenskaper innehåller antalet målrepliker eller instanser och alla beroende kedjor mellan resurser (andra program eller tjänster). En lista över omfattande egenskaper finns i [REST API Swagger-specifikationen](https://github.com/Azure/azure-rest-api-specs/blob/current/specification/servicefabric/resource-manager/Microsoft.ServiceFabric/2017-07-01-preview/servicefabric.json). Observera att detta ersätter inte programmet eller tjänsten visar, men i stället beskrivs några av vad som finns i dem som en del av klustrets Resource Manager-mall. Här är en exempelmall som innehåller distribuera tillståndslösa tjänsten *Service1* och en tillståndskänslig service *plats2* som en del av *Application1*:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only"
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name"
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version"
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package zip file"
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The application name"
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Service1",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The service type name"
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Service2",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The service type name"
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "26"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
  }
  ```

  > [!NOTE] 
  > Den *apiVersion* måste anges till `"2017-07-01-preview"`. Den här mallen kan också distribueras oberoende av klustret, så länge klustret har redan distribuerats.

5. Distribuera! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Hantera ett befintligt program via Hanteraren för filserverresurser

Om klustret redan är igång och vissa program som du vill hantera som resurshanteraren resurser som redan har distribuerats, i stället för att ta bort program och omdistribuera dem kan du använda ett PUT-anrop med samma API: er för att få program bekräftas som Resource Manager-resurser. 


## <a name="next-steps"></a>Nästa steg

* Använd den [Service Fabric CLI](service-fabric-cli.md) eller [PowerShell](service-fabric-deploy-remove-applications.md) att distribuera andra program i klustret. 
* [Uppgradera Service Fabric-kluster](service-fabric-cluster-upgrade.md)

