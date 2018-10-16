---
title: Hur du stoppa övervakning Your Azure Kubernetes Service-kluster | Microsoft Docs
description: Den här artikeln beskrivs hur du kan sluta övervakning av din Azure-AKS-kluster med Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/04/2018
ms.author: magoedte
ms.openlocfilehash: 7cd2aecf21a86bb58452e48fcdf1d79f1d3a2104
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321232"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Hur du stoppar övervakningen Azure Kubernetes Service (AKS) med Azure Monitor för behållare

Om du aktiverar när övervakningen av AKS-klustret kan du beslutar att du inte längre vill du övervaka den, kan du *avanmäla dig*.  Den här artikeln visar hur du gör detta med hjälp av Azure CLI eller med de angivna Azure Resource Manager-mallarna.  


## <a name="azure-cli"></a>Azure CLI
Använd den [az aks disable-tillägg](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) kommando för att inaktivera Azure Monitor för behållare. Kommandot tar bort agenten från noder i klustret, den tar inte bort lösningen eller data som samlas in och lagras i Log Analytics-resursen redan.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Om du vill återaktivera övervakning för ditt kluster, se [aktivera övervakning med hjälp av Azure CLI](monitoring-container-insights-onboard.md#enable-monitoring-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Tillhandahållna är två Azure Resource Manager-mall för ta bort lösningsresurser konsekvent och upprepade gånger i resursgruppen. En är en JSON-mall som anger att konfigurationen ska *avanmäla dig* och den andra innehåller parametervärden som du konfigurerar för att ange AKS-kluster-ID och resurs resursgruppen som klustret distribueras i. 

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Mallen måste distribueras i samma resursgrupp som klustret.
>

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
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

2. Spara filen som **OptOutTemplate.json** till en lokal mapp.
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
        }
      }
    }
    ```

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena för AKS-kluster som finns på den **egenskaper** sidan för det markerade klustret .

    ![Egenskapssidan för behållare](./media/monitoring-container-health/container-properties-page.png)

    När du är på den **egenskaper** kan också kopiera den **Arbetssyteresurs-ID**. Det här värdet krävs om du vill att ta bort Log Analytics-arbetsytan senare. Ta bort Log Analytics-arbetsytan utförs inte som en del av den här processen. 

5. Spara filen som **OptOutParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

### <a name="remove-the-solution-using-azure-cli"></a>Ta bort lösningen med hjälp av Azure CLI
Kör du följande kommando med Azure CLI på Linux för att ta bort lösningen och rensa konfigurationen på AKS-klustret.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart, returneras ett meddelande som liknar följande som innehåller resultatet:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Ta bort lösningen med hjälp av PowerShell

Kör följande PowerShell-kommandon i den mapp som innehåller mallen för att ta bort lösningen och rensa konfigurationen från ditt AKS-kluster.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart, returneras ett meddelande som liknar följande som innehåller resultatet:

```powershell
ProvisioningState       : Succeeded
```

Om arbetsytan har skapats enbart för att stöd för övervakning av klustret och inte längre behövs kan behöva du manuellt ta bort den. Om du inte är bekant med hur du tar bort en arbetsyta kan se [ta bort en Azure Log Analytics-arbetsyta med Azure portal](../log-analytics/log-analytics-manage-del-workspace.md). Glöm inte att den **Arbetssyteresurs-ID** vi kopierade tidigare i steg 4, du kommer att behöva som. 

