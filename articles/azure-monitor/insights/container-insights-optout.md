---
title: Hur du stoppa övervakning Your Azure Kubernetes Service-kluster | Microsoft Docs
description: Den här artikeln beskrivs hur du kan sluta övervakning av din Azure-AKS-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385452"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Hur du stoppar övervakningen Azure Kubernetes Service (AKS) med Azure Monitor för behållare

När du aktiverar övervakning av AKS-klustret måste stoppa du övervakningen klustret om du inte längre vill att övervaka den. Den här artikeln visar hur du gör detta med hjälp av Azure CLI eller med de angivna Azure Resource Manager-mallarna.  


## <a name="azure-cli"></a>Azure CLI

Använd kommandot [AZ AKS Disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) för att inaktivera Azure Monitor för behållare. Kommandot tar bort agenten från klusternoderna, den tar inte bort lösningen eller de data som redan har samlats in och lagrats i Azure Monitor resursen.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Om du vill aktivera övervakning för klustret igen går du till [Aktivera övervakning med Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Tillhandahållna är två Azure Resource Manager-mall för ta bort lösningsresurser konsekvent och upprepade gånger i resursgruppen. Det ena är en JSON-mall som anger konfigurationen för att stoppa övervakningen och den andra innehåller parameter värden som du konfigurerar för att ange AKS-kluster resurs-ID och resurs grupp som klustret har distribuerats i.

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Mallen måste distribueras i samma resurs grupp i klustret. Om du utelämnar andra egenskaper eller tillägg när du använder den här mallen kan det leda till att de tas bort från klustret. Till exempel *enableRBAC* för RBAC-principer som implementeras i klustret eller *aksResourceTagValues* IF-Taggar har angetts för AKS-klustret.  
>

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Du kan identifiera din version genom att köra `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-template"></a>Skapa mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
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
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena för AKS-klustret, som du hittar på **egenskaps** sidan för det valda klustret.

    ![Egenskapssidan för behållare](media/container-insights-optout/container-properties-page.png)

    När du är på sidan **Egenskaper** kopierar du även **arbets ytans resurs-ID**. Det här värdet krävs om du vill att ta bort Log Analytics-arbetsytan senare. Ta bort Log Analytics-arbetsytan utförs inte som en del av den här processen.

    Redigera värdena för **aksResourceTagValues** så att de matchar de befintliga taggvärde som angetts för AKS-klustret.

5. Spara filen som **OptOutParam. JSON** i en lokal mapp.

6. Nu är det dags att distribuera den här mallen.

### <a name="remove-the-solution-using-azure-cli"></a>Ta bort lösningen med hjälp av Azure CLI

Kör du följande kommando med Azure CLI på Linux för att ta bort lösningen och rensa konfigurationen på AKS-klustret.

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

Kör följande PowerShell-kommandon i den mapp som innehåller mallen för att ta bort lösningen och rensa konfigurationen från ditt AKS-kluster.    

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

Om arbetsytan har skapats enbart för att stöd för övervakning av klustret och inte längre behövs kan behöva du manuellt ta bort den. Om du inte är bekant med hur du tar bort en arbets yta, se [ta bort en Azure Log Analytics-arbetsyta med Azure Portal](../../log-analytics/log-analytics-manage-del-workspace.md). Glöm inte att du har kopierat **resurs-ID för arbets ytan** tidigare i steg 4. du kommer att behöva det.
