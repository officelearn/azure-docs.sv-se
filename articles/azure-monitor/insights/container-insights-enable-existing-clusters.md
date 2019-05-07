---
title: Övervaka ett Azure Kubernetes Service (AKS)-kluster som distribueras | Microsoft Docs
description: Lär dig hur du aktiverar övervakning av ett kluster i Azure Kubernetes Service (AKS) med Azure Monitor för behållare som redan har distribuerats i din prenumeration.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: ae340fb11d422b7516cc315e78be974d22239503
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074687"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Aktivera övervakning i Azure Kubernetes Service (AKS)-kluster som redan har distribuerats

Den här artikeln beskriver hur du ställer in Azure Monitor för behållare för att övervaka hanterade Kubernetes-kluster som finns på [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) som redan har distribuerats i din prenumeration.

Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med någon av metoderna som stöds:

* Azure CLI
* Terraform
* [Från Azure Monitor](#enable-from-azure-monitor-in-the-portal) eller [direkt från AKS-klustret](#enable-directly-from-aks-cluster-in-the-portal) i Azure portal 
* Med den [tillhandahålls av Azure Resource Manager-mall](#enable-using-an-azure-resource-manager-template) med hjälp av Azure PowerShell-cmdleten `New-AzResourceGroupDeployment` eller med Azure CLI. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com). 

## <a name="enable-using-azure-cli"></a>Aktivera med hjälp av Azure CLI

Följande steg aktiverar övervakning av AKS-klustret med hjälp av Azure CLI. I det här exemplet måste du behöver inte per skapa eller ange en befintlig arbetsyta. Det här kommandot gör enklare för dig genom att skapa en standardarbetsyta i standardresursgruppen för AKS-kluster-prenumeration om det inte redan finns i regionen.  Standardarbetsytan skapade liknar formatet *DefaultWorkspace -\<GUID >-\<Region >*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Utdata ska likna följande:

```azurecli
provisioningState       : Succeeded
```

Om du skulle hellre integrerar med en befintlig arbetsyta, kan du använda följande kommando för att ange den arbetsytan.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

Utdata ska likna följande:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-using-terraform"></a>Aktivera med hjälp av Terraform

1. Lägg till den **oms_agent** tillägg profil i den befintliga [azurerm_kubernetes_cluster resurs](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Lägg till den [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) följa stegen i Terraform-dokumentationen.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Aktivera från Azure Monitor i portalen 

Om du vill aktivera övervakning av AKS-kluster i Azure-portalen från Azure Monitor, gör du följande:

1. I Azure-portalen väljer du **övervakaren**. 
2. Välj **behållare** i listan.
3. På den **skärm – behållare** väljer **icke-övervakas kluster**.
4. Från listan över ej övervakade kluster, hitta behållaren i listan och klicka på **aktivera**.   
5. På den **Kom igång med Azure Monitor för behållare** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret, markerar du den i den nedrullningsbara listan.  
    Listan förväljer standardarbetsytan och plats som AKS-behållare distribueras till i prenumerationen. 

    ![Aktivera AKS behållareinsikter övervakning](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från klustret, följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Var noga med att skapa arbetsytan i samma prenumeration som AKS-behållare distribueras till. 
 
När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret. 

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Aktivera direkt från AKS-kluster i portalen

Om du vill aktivera övervakning direkt från en av dina AKS-kluster i Azure-portalen, gör du följande:

1. Välj **Alla tjänster** i Azure-portalen. 
2. I listan över resurser, börjar du skriva **behållare**.  
    Filtreras listan baserat på dina indata. 
3. Välj **Kubernetes-tjänster**.  

    ![Länken Kubernetes-tjänster](./media/container-insights-onboard/portal-search-containers-01.png)

4. I listan över behållare, väljer du en behållare.
5. På översiktssidan behållaren väljer **övervaka behållare**.  
6. På den **Kom igång med Azure Monitor för behållare** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret, markerar du den i den nedrullningsbara listan.  
    Listan förväljer standardarbetsytan och plats som AKS-behållare distribueras till i prenumerationen. 

    ![Aktivera hälsoövervakning för AKS-behållare](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från klustret, följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Var noga med att skapa arbetsytan i samma prenumeration som AKS-behållare distribueras till. 
 
När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa användningsdata för klustret. 

## <a name="enable-using-an-azure-resource-manager-template"></a>Aktivera med hjälp av en Azure Resource Manager-mall

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

* AKS-behållare resurs-ID. 
* Den resursgrupp som klustret har distribuerats i.

>[!NOTE]
>Mallen måste distribueras i samma resursgrupp som klustret.
>

Log Analytics-arbetsytan måste skapas innan du aktiverar övervakning med hjälp av Azure PowerShell eller CLI. För att skapa arbetsytan, du kan konfigurera det via [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.59 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-a-template"></a>Skapa och köra en mall

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
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
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
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     }
     ]
    }
    ```

2. Spara filen som **existingClusterOnboarding.json** till en lokal mapp.
3. Klistra in följande JSON-syntax i filen:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       }  
     }
    }
    ```

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena på den **översikt över AKS** för AKS-klustret. Värdet för **workspaceResourceId** är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan. 
5. Spara filen som **existingClusterParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

   * Om du vill distribuera med Azure PowerShell använder du följande kommandon i den mapp som innehåller mallen:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Om du vill distribuera med Azure CLI kör du följande kommandon:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```azurecli
       provisioningState       : Succeeded
       ```
     När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret. 

## <a name="verify-agent-and-solution-deployment"></a>Kontrollera distributionen av agenten och lösning

Med agentversion *06072018* eller senare, kan du kontrollera att både agenten och lösningen har distribuerats. Du kan kontrollera endast agentdistribution med tidigare versioner av agenten.

### <a name="agent-version-06072018-or-later"></a>Agentversion 06072018 eller senare

Kör följande kommando för att kontrollera att agenten har distribuerats. 

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Kontrollera distributionen av lösningen genom att köra följande kommando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Tidigare än 06072018 agentversion

Verifiera att agentversionen Log Analytics som publicerades före *06072018* har distribuerats korrekt, kör du följande kommando:  

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Visa konfiguration med CLI

Använd den `aks show` för att hämta information om sådana som är den lösning som har aktiverats eller inte, vad är Log Analytics-arbetsyta resourceID och sammanfattningsinformation om klustret.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Efter ett par minuter kommandot har slutförts och returnerar JSON-formaterad information om lösningen.  Resultatet av kommandot ska visa övervakning tillägg profilen och liknar följande Exempelutdata:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Nästa steg

* Om det uppstår problem vid försök att publicera lösningen kan du granska den [felsökningsguide](container-insights-troubleshoot.md)

* Med övervakning aktiverat för att avbilda hälsomått för både noder i AKS och poddar är dessa health-mått tillgängliga i Azure-portalen. Läs hur du använder Azure Monitor för behållare i [visa Azure Kubernetes Service health](container-insights-analyze.md).
