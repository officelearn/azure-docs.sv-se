---
title: Distribuera och uppgradera med Azure Resource Manager
description: Lär dig hur du distribuerar program och tjänster till ett Service Fabric-kluster med hjälp av en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: a2dfe54bf2c6b4fa8814f10c10576a73727a7417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610258"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Hantera program och tjänster som Azure Resource Manager-resurser

Du kan distribuera program och tjänster till ditt Service Fabric-kluster via Azure Resource Manager. Det innebär att du i stället för att distribuera och hantera program via PowerShell eller CLI efter att ha fått vänta på att klustret ska vara klart kan du nu uttrycka program och tjänster i JSON och distribuera dem i samma Resource Manager-mall som klustret. Processen för programregistrering, etablering och distribution sker i ett steg.

Det här är det rekommenderade sättet för dig att distribuera alla program för installation, styrning eller klusterhantering som du behöver i klustret. Detta inkluderar [Patch Orchestration Application](service-fabric-patch-orchestration-application.md), Watchdogs eller alla program som måste köras i klustret innan andra program eller tjänster distribueras. 

Hantera programmen som Resource Manager-resurser i förekommande fall för att förbättra:
* Granskningsspårning: Resource Manager granskar varje åtgärd och håller en detaljerad *aktivitetslogg* som kan hjälpa dig att spåra alla ändringar som gjorts i dessa program och klustret.
* Rollbaserad åtkomstkontroll (RBAC): Hantera åtkomst till kluster samt program som distribueras i klustret kan göras via samma Resource Manager-mall.
* Azure Resource Manager (via Azure Portal) blir en one-stop-shop för hantering av kluster och kritiska programdistributioner.

Följande kodavsnitt visar de olika typer av resurser som kan hanteras via en mall:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Lägga till ett nytt program i Resource Manager-mallen

1. Förbered klustrets Resource Manager-mall för distribution. Mer information om detta [finns i Skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager.](service-fabric-cluster-creation-via-arm.md)
2. Tänk på några av de program som du planerar att distribuera i klustret. Finns det några som alltid kommer att köras som andra program kan ta beroende av? Planerar du att distribuera klusterstyrnings- eller inställningsprogram? Dessa typer av program hanteras bäst via en Resource Manager-mall, som diskuterats ovan. 
3. När du har listat ut vilka program du vill ska distribueras på detta sätt, programmen måste förpackas, zippade, och sätta på en filresurs. Resursen måste vara tillgänglig via en REST-slutpunkt för Azure Resource Manager att använda under distributionen.
4. Beskriv egenskaperna för varje program under klusterdeklarationen i mallen Resource Manager. Dessa egenskaper omfattar replik- eller instansantal och eventuella beroendekedjor mellan resurser (andra program eller tjänster). En lista över omfattande egenskaper finns i [REST API Swagger Spec](https://aka.ms/sfrpswaggerspec). Observera att detta inte ersätter program- eller tjänstmanifesten, utan snarare beskriver en del av vad som finns i dem som en del av klustrets Resource Manager-mall. Här är en exempelmall som innehåller distribution av en tillståndslös tjänst *Service1* och en tillståndskänslig tjänst *Service2* som en del av *Application1:*

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
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
        "apiVersion": "2019-03-01",
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
   > *ApiVersion* måste ställas `"2019-03-01"`in på . Den här mallen kan också distribueras oberoende av klustret så länge klustret redan har distribuerats.

5. Distribuera! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Ta bort programresursprogramresurs för tjänstinfrastruktur
Följande utlöser att appaketet inte etableras från klustret, och detta rensar diskutrymmet som används:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Om du bara tar bort Microsoft.ServiceFabric/clusters/application från ARM-mallen tas programmet inte upp

>[!NOTE]
> När borttagningen är klar bör du inte se paketversionen i SFX eller ARM längre. Du kan inte ta bort programtypsversionsresursen som programmet körs med. ARM/SFRP förhindrar detta. Om du försöker avetablera det löpande paketet förhindrar SF-körningen det.


## <a name="manage-an-existing-application-via-resource-manager"></a>Hantera ett befintligt program via Resource Manager

Om klustret redan är uppe och vissa program som du vill hantera som Resource Manager-resurser redan har distribuerats på det, i stället för att ta bort programmen och distribuera om dem, kan du använda ett PUT-anrop med samma API:er för att få programmen att hämta som Resource Manager-resurser. Mer information finns i [Vad är programresursmodellen För Service Fabric?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model)

> [!NOTE]
> Om du vill att en klusteruppgradering ska ignorera felaktiga appar kan kunden ange "maxPercentUnhealthyApplications: 100" i avsnittet "upgradeDescription/healthPolicy". detaljerade beskrivningar för alla inställningar finns i dokumentationen för [REST API-klusteruppgraderingsprincip för servicevävnader.](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)

## <a name="next-steps"></a>Nästa steg

* Använd [Service Fabric CLI](service-fabric-cli.md) eller [PowerShell](service-fabric-deploy-remove-applications.md) för att distribuera andra program till klustret. 
* [Uppgradera ditt Service Fabric-kluster](service-fabric-cluster-upgrade.md)

