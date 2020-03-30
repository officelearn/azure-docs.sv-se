---
title: Övervaka ett AKS-kluster (Azure Kubernetes Service) | Microsoft-dokument
description: Lär dig hur du aktiverar övervakning av ett AKS-kluster (Azure Kubernetes Service) med Azure Monitor för behållare som redan har distribuerats i din prenumeration.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275456"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Aktivera övervakning av AKS-kluster (Azure Kubernetes Service) som redan har distribuerats

I den här artikeln beskrivs hur du konfigurerar Azure Monitor för behållare för att övervaka hanterade Kubernetes-kluster som finns på [Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/) som redan har distribuerats i din prenumeration.

Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats med någon av de metoder som stöds:

* Azure CLI
* Terraform
* [Från Azure Monitor](#enable-from-azure-monitor-in-the-portal) eller [direkt från AKS-klustret](#enable-directly-from-aks-cluster-in-the-portal) i Azure-portalen
* Med den [medföljande Azure Resource Manager-mallen](#enable-using-an-azure-resource-manager-template) `New-AzResourceGroupDeployment` med hjälp av Azure PowerShell-cmdlet eller med Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Aktivera med Azure CLI

Följande steg gör det möjligt att övervaka ditt AKS-kluster med Azure CLI. I det här exemplet behöver du inte skapa eller ange en befintlig arbetsyta per år. Det här kommandot förenklar processen för dig genom att skapa en standardarbetsyta i standardresursgruppen för AKS-klusterprenumerationen om det inte redan finns någon i regionen.  Den skapade standardarbetsytan liknar formatet *standardarbetsyta-\<GUID\<>- Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Utdata kommer att likna följande:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrera med en befintlig arbetsyta

Om du hellre vill integrera med en befintlig arbetsyta utför du följande steg för att först `--workspace-resource-id` identifiera det fullständiga resurs-ID för log analytics-arbetsytan som krävs för parametern och kör sedan kommandot för att aktivera övervakningstillägget mot den angivna arbetsytan.  

1. Lista alla prenumerationer som du har åtkomst till med följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata kommer att likna följande:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Kopiera värdet för **SubscriptionId**.

2. Växla till prenumerationen som är värd för Log Analytics-arbetsytan med följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. I följande exempel visas listan över arbetsytor i dina prenumerationer i standardformatet JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Leta reda på arbetsytans namn i utdata och kopiera sedan det fullständiga resurs-ID:et för den logganalysarbetsytan under **fält-ID.**

4. Kör följande kommando för att aktivera övervakningstillägget `--workspace-resource-id` och ersätter värdet för parametern. Strängvärdet måste ligga inom de dubbla citaten:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Utdata kommer att likna följande:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Aktivera med Terraform

1. Lägga **oms_agent** till oms_agent-tilläggsprofilen i den befintliga [azurerm_kubernetes_cluster resursen](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Lägg till [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) som följer stegen i Terraform-dokumentationen.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Aktivera från Azure Monitor i portalen

Så här aktiverar du övervakning av AKS-klustret i Azure-portalen från Azure Monitor:

1. Välj **Övervaka**i Azure-portalen .

2. Välj **Behållare** i listan.

3. På sidan **Bildskärm - behållare** väljer du Kluster som inte **övervakas**.

4. Leta reda på behållaren i listan i listan över kluster som inte övervakas och klicka på **Aktivera**.   

5. Om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret på sidan **Onboarding to Azure Monitor för behållare** väljer du den i listrutan.  
    Listan förväljs till standardarbetsytan och platsen som AKS-behållaren distribueras till i prenumerationen.

    ![Aktivera övervakning av AKS-behållarinsikter](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för lagring av övervakningsdata från klustret följer du instruktionerna i [Skapa en log analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Var noga med att skapa arbetsytan i samma prenumeration som AKS-behållaren distribueras till.

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Aktivera direkt från AKS-kluster i portalen

Så här aktiverar du övervakning direkt från ett av AKS-kluster i Azure-portalen:

1. Välj **Alla tjänster** i Azure-portalen.

2. Börja skriva **behållare**i listan över resurser .  Listan filtrerar baserat på dina indata.

3. Välj **Kubernetes-tjänster**.  

    ![Länken Kubernetes-tjänster](./media/container-insights-onboard/portal-search-containers-01.png)

4. Välj en behållare i listan över behållare.

5. På sidan för behållaröversikt väljer du **Övervaka behållare**.  

6. Om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret på sidan **Onboarding to Azure Monitor för behållare** väljer du den i listrutan.  
    Listan förväljs till standardarbetsytan och platsen som AKS-behållaren distribueras till i prenumerationen.

    ![Aktivera hälsoövervakning av AKS-behållare](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Om du vill skapa en ny Log Analytics-arbetsyta för lagring av övervakningsdata från klustret följer du instruktionerna i [Skapa en log analytics-arbetsyta](../../azure-monitor/learn/quick-create-workspace.md). Var noga med att skapa arbetsytan i samma prenumeration som AKS-behållaren distribueras till.

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa driftdata för klustret.

## <a name="enable-using-an-azure-resource-manager-template"></a>Aktivera med hjälp av en Azure Resource Manager-mall

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

* AKS-behållarresurs-ID.
* Resursgruppen som klustret distribueras i.

>[!NOTE]
>Mallen måste distribueras i samma resursgrupp som klustret.
>

Log Analytics-arbetsytan måste skapas innan du aktiverar övervakning med Azure PowerShell eller CLI. Om du vill skapa arbetsytan kan du konfigurera den via [Azure Resource Manager,](../../azure-monitor/platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall läser du:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.59 eller senare. Om du vill `az --version`identifiera din version kör du . Om du behöver installera eller uppgradera Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
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
          "tags": "[parameters('aksResourceTagValues')]",
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

2. Spara den här filen som **existerandeClusterOnboarding.json** i en lokal mapp.

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

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena på **aks-översiktssidan** för AKS-klustret. Värdet för **workspaceResourceId** är det fullständiga resurs-ID:n för din Log Analytics-arbetsyta, som innehåller arbetsytans namn.

    Redigera värdena för **aksResourceTagValues** så att de matchar de befintliga taggvärden som angetts för AKS-klustret.

5. Spara den här filen som **befintligaClusterParam.json** i en lokal mapp.

6. Nu är det dags att distribuera den här mallen.

   * Om du vill distribuera med Azure PowerShell använder du följande kommandon i mappen som innehåller mallen:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```output
       provisioningState       : Succeeded
       ```

   * Om du vill distribuera med Azure CLI kör du följande kommandon:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```output
       provisioningState       : Succeeded
       ```

       När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

## <a name="verify-agent-and-solution-deployment"></a>Verifiera agent- och lösningsdistribution

Med agentversion *06072018* eller senare kan du kontrollera att både agenten och lösningen har distribuerats. Med tidigare versioner av agenten kan du bara verifiera agentdistributionen.

### <a name="agent-version-06072018-or-later"></a>Agentversion 06072018 eller senare

Kör följande kommando för att kontrollera att agenten har distribuerats.

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata ska likna följande, vilket indikerar att den har distribuerats korrekt:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Kontrollera distributionen av lösningen genom att köra följande kommando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Utdata ska likna följande, vilket indikerar att den har distribuerats korrekt:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agentversion tidigare än 06072018

Om du vill kontrollera att log Analytics-agentversionen som släpptes innan *06072018* har distribuerats korrekt kör du följande kommando:  

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata ska likna följande, vilket indikerar att den har distribuerats korrekt:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Visa konfiguration med CLI

Använd `aks show` kommandot för att få information som är lösningen aktiverad eller inte, vad är Log Analytics workspace resourceID och sammanfattande information om klustret.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Efter några minuter slutför och returnerar kommandot JSON-formaterad information om lösningen.  Resultatet av kommandot ska visa övervakningstilläggsprofilen och liknar följande exempelutdata:

```output
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

* Om du får problem när du försöker gå in på lösningen läser du [felsökningsguiden](container-insights-troubleshoot.md)

* När övervakning är aktiverad för att samla in hälso- och resursanvändning för AKS-kluster och arbetsbelastningar som körs på dem kan du lära dig [hur du använder](container-insights-analyze.md) Azure Monitor för behållare.
