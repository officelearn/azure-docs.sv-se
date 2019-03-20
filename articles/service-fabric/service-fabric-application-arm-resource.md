---
title: Distribuera och uppgradera program och tjänster med Azure Resource Manager | Microsoft Docs
description: Lär dig hur du distribuerar program och tjänster till Service Fabric-kluster med en Azure Resource Manager-mall.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e41647140373fcf637cad55af62764bd87826a62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849354"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Hantera program och tjänster som Azure Resource Manager-resurser

Du kan distribuera program och tjänster till ditt Service Fabric-kluster via Azure Resource Manager. Det innebär att i stället för att distribuera och hantera program via PowerShell eller CLI efter att behöva vänta på att klustret är klar finns nu express program och tjänster i JSON och distribuera dem i samma Resource Manager-mallen som klustret. Processen för programregistrering, etablering och distribuera alla sker i ett steg.

Det här är det rekommenderade sättet att distribuera valfri installation, styrning eller kluster hanteringsprogram som du behöver i ditt kluster. Detta inkluderar den [Patch Orchestration Application](service-fabric-patch-orchestration-application.md), Watchdogs eller program som måste köras i klustret innan andra program eller tjänster distribueras. 

När så är tillämpligt, kan du hantera dina program som Resource Manager-resurser för att förbättra:
* Granskningslogg: Resource Manager granskar varje åtgärd och håller en detaljerad *aktivitetsloggen* som hjälper dig att spåra ändringar som görs till dessa program och klustret.
* Rollbaserad åtkomstkontroll (RBAC): Hantera åtkomst till kluster, samt program som distribueras i klustret kan göras via samma Resource Manager-mallen.
* Azure Resource Manager (via Azure portal) blir en en-när det gäller för att hantera ditt kluster och distribution av kritiska program.

Följande utdrag visar de olika typerna av resurser som kan hanteras via en mall:

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

1. Förbered ditt kluster Resource Manager-mall för distribution. Se [skapa Service Fabric-kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) mer information om detta.
2. Tänk på att vissa program som du ska distribuera i klustret. Finns det någon som alltid körs som andra program kan ta beroenden? Planerar du distribuera alla kluster styrning och installationsprogrammet program? Dessa typer av program som bäst hanteras via Resource Manager-mall som beskrivs ovan. 
3. När du har kommit fram till vilka program som du vill ska distribueras på så sätt har programmen paketeras, zippade och placera på en filresurs. Resursen måste vara tillgänglig via ett REST-slutpunkt för Azure Resource Manager att använda under distributionen.
4. I Resource Manager-mallen, under din kluster-deklarationen Beskriv egenskaper för varje program. Dessa egenskaper innehåller antalet målrepliker eller instanser och eventuella beroendekedjor mellan resurser (andra program eller tjänster). En lista över omfattande egenskaper finns i den [REST API Swagger-specifikation](https://aka.ms/sfrpswaggerspec). Observera att detta ersätter inte programmet eller tjänsten manifest, men i stället beskriver några av vad som finns i dem som en del av klustrets Resource Manager-mall. Här är en exempel-mall som innehåller distribuerar en tillståndslös tjänst *Service1* och en tillståndskänslig tjänst *plats2* som en del av *Application1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
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
            "highKey": "5"
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
   > Den *apiVersion* måste anges till `"2017-07-01-preview"`. Den här mallen kan också distribueras oberoende av klustret, så länge klustret redan har distribuerats.

5. Distribuera! 

## <a name="manage-an-existing-application-via-resource-manager"></a>Hantera ett befintligt program via Resource Manager

Om klustret redan är igång och vissa program som du vill hantera som Resurshanterarens resurser redan har distribuerats på den, i stället för att avlägsna program och omdistribuera dem. Du kan använda ett PUT-anrop med samma API: er för att få programmen får bekräftas som Resource Manager-resurser. 

> [!NOTE]
> Så att en uppgradering av klustret att ignorera feltillstånd appar kunden kan ange ”maxPercentUnhealthyApplications: 100 ”i avsnittet” upgradeDescription/healthPolicy ”; detaljerade beskrivningar för alla inställningar finns i [dokumentation om Service fabric REST API kluster uppgradera Policy](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Nästa steg

* Använd den [Service Fabric CLI](service-fabric-cli.md) eller [PowerShell](service-fabric-deploy-remove-applications.md) att distribuera andra program i klustret. 
* [Uppgradera ditt Service Fabric-kluster](service-fabric-cluster-upgrade.md)

