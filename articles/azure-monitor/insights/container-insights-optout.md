---
title: Så här slutar du övervaka azure kubernetes-tjänstklustret | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan avbryta övervakningen av ditt Azure AKS-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275261"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Stoppa övervakningen av Azure Kubernetes Service (AKS) med Azure Monitor för containrar

När du har aktiverat övervakning av AKS-klustret kan du sluta övervaka klustret om du bestämmer dig för att du inte längre vill övervaka det. Den här artikeln visar hur du åstadkommer detta med azure CLI eller med de medföljande Azure Resource Manager-mallarna.  


## <a name="azure-cli"></a>Azure CLI

Använd kommandot [az aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) för att inaktivera Azure Monitor för behållare. Kommandot tar bort agenten från klusternoderna, det tar inte bort lösningen eller de data som redan samlas in och lagras i din Azure Monitor-resurs.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Information om hur du aktiverar övervakning för klustret finns i [Aktivera övervakning med Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Tillhandahålls är två Azure Resource Manager-mall för att stödja att ta bort lösningsresurserna konsekvent och upprepade gånger i din resursgrupp. Den ena är en JSON-mall som anger konfigurationen för att stoppa övervakningen och den andra innehåller parametervärden som du konfigurerar för att ange AKS-klusterresurs-ID och resursgrupp som klustret distribueras i.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall läser du:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Mallen måste distribueras i samma resursgrupp i klustret. Om du utelämnar andra egenskaper eller tillägg när du använder den här mallen kan det leda till att de tas bort från klustret. *Aktivera till exempelRBAC* för RBAC-principer som implementerats i *klustret eller aksResourceTagValues* om taggar har angetts för AKS-klustret.  
>

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Om du vill `az --version`identifiera din version kör du . Om du behöver installera eller uppgradera Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

2. Spara den här filen som **OptOutTemplate.json** i en lokal mapp.

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

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena i AKS-klustret, som du hittar på **sidan Egenskaper** för det valda klustret.

    ![Sidan Egenskaper för behållare](media/container-insights-optout/container-properties-page.png)

    När du är på sidan **Egenskaper** kopierar du även **resurs-ID:et för arbetsytan**. Det här värdet krävs om du bestämmer dig för att ta bort log analytics-arbetsytan senare. Det går inte att ta bort log analytics-arbetsytan som en del av den här processen.

    Redigera värdena för **aksResourceTagValues** så att de matchar de befintliga taggvärden som angetts för AKS-klustret.

5. Spara den här filen som **OptOutParam.json** i en lokal mapp.

6. Nu är det dags att distribuera den här mallen.

### <a name="remove-the-solution-using-azure-cli"></a>Ta bort lösningen med Azure CLI

Kör följande kommando med Azure CLI på Linux för att ta bort lösningen och rensa konfigurationen på AKS-klustret.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart returneras ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Ta bort lösningen med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kör följande PowerShell-kommandon i mappen som innehåller mallen för att ta bort lösningen och rensa konfigurationen från AKS-klustret.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart returneras ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Nästa steg

Om arbetsytan skapades bara för att stödja övervakning av klustret och det inte längre behövs, måste du manuellt ta bort den. Om du inte är bekant med hur du tar bort en arbetsyta läser du [Ta bort en Azure Log Analytics-arbetsyta med Azure-portalen](../../log-analytics/log-analytics-manage-del-workspace.md). Glöm inte **arbetsyteresurs-ID** kopieras tidigare i steg 4, du kommer att behöva det.
