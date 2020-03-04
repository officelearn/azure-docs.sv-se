---
title: Så här stoppar du övervakningen av ditt Azure Red Hat OpenShift-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du kan stoppa övervakningen av ditt Azure Red Hat OpenShift-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: eff5203aeedd3c7ad283b55ba12f0e45a556460d
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250721"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Så här stoppar du övervakningen av ditt Azure Red Hat OpenShift-kluster med Azure Monitor för behållare

När du har aktiverat övervakning av ditt Azure Red Hat OpenShift-kluster kan du stoppa övervakningen av klustret om du vill att du inte längre vill övervaka det. Den här artikeln visar hur du gör detta med hjälp av de tillhandahållna Azure Resource Manager mallarna.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Tillhandahållna är två Azure Resource Manager-mall för ta bort lösningsresurser konsekvent och upprepade gånger i resursgruppen. Det ena är en JSON-mall som anger konfigurationen för att stoppa övervakningen och den andra innehåller parameter värden som du konfigurerar för att ange det OpenShift-kluster resurs-ID och Azure-region som klustret har distribuerats i.

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.65 eller senare. Du kan identifiera din version genom att köra `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-template"></a>Skapa mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Spara filen som **OptOutTemplate. JSON** i en lokal mapp.

3. Klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Redigera värdena för **aroResourceId** och **aroResourceLocation** med hjälp av värdena i OpenShift-klustret, som du kan hitta på **egenskaps** sidan för det valda klustret.

    ![Egenskapssidan för behållare](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Spara filen som **OptOutParam. JSON** i en lokal mapp.

6. Nu är det dags att distribuera den här mallen.

### <a name="remove-the-solution-using-azure-cli"></a>Ta bort lösningen med hjälp av Azure CLI

Kör följande kommando med Azure CLI i Linux för att ta bort lösningen och rensa konfigurationen på klustret.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart, returneras ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Ta bort lösningen med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kör följande PowerShell-kommandon i mappen som innehåller mallen för att ta bort lösningen och rensa konfigurationen från klustret.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart, returneras ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Nästa steg

Om arbetsytan har skapats enbart för att stöd för övervakning av klustret och inte längre behövs kan behöva du manuellt ta bort den. Om du inte är bekant med hur du tar bort en arbets yta, se [ta bort en Azure Log Analytics-arbetsyta](../../log-analytics/log-analytics-manage-del-workspace.md).
