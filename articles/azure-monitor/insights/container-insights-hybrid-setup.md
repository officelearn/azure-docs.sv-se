---
title: Konfigurera Hybrid Kubernetes-kluster med Azure Monitor för behållare | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare för att övervaka Kubernetes-kluster som finns på Azure Stack eller annan miljö.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198062"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurera hybridkubernätskluster med Azure Monitor för behållare

Azure Monitor för behållare ger omfattande övervakningsupplevelse för Azure Kubernetes Service (AKS) och [AKS Engine på Azure](https://github.com/Azure/aks-engine), som är ett självhanterat Kubernetes-kluster som finns på Azure. I den här artikeln beskrivs hur du aktiverar övervakning av Kubernetes-kluster som finns utanför Azure och uppnår en liknande övervakningsupplevelse.

## <a name="prerequisites"></a>Krav

Innan du börjar kontrollerar du att du har följande:

* En Log Analytics-arbetsyta.

    Azure Monitor för behållare stöder en Log Analytics-arbetsyta i de regioner som anges i [Azure-produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om du vill skapa en egen arbetsyta kan den skapas via [Azure Resource Manager,](../platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure-portalen](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Det går inte att aktivera övervakning av flera kluster med samma klusternamn till samma Log Analytics-arbetsyta. Klusternamnen måste vara unika.
    >

* Du är medlem i **rollen Log Analytics-deltagare** för att aktivera behållarövervakning. Mer information om hur du kontrollerar åtkomsten till en Log Analytics-arbetsyta finns i [Hantera åtkomst till arbetsyta och loggdata](../platform/manage-access.md)

* [HELM-klienten](https://helm.sh/docs/using_helm/) till azure monitor för behållare för det angivna Kubernetes-klustret.

* Följande proxy- och brandväggskonfigurationsinformation krävs för att den behållarversionen av Log Analytics-agenten för Linux ska kunna kommunicera med Azure Monitor:

    |Agentresurs|Portar |
    |------|---------|   
    |*.ods.opinsights.azure.com |Port 443 |  
    |*.oms.opinsights.azure.com |Port 443 |  
    |*.blob.core.windows.net |Port 443 |  
    |*.dc.services.visualstudio.com |Port 443 |

* Den containeriserade agenten kräver `cAdvisor secure port: 10250` Kubelets eller `unsecure port :10255` öppnas på alla noder i klustret för att samla in prestandamått. Vi rekommenderar `secure port: 10250` att du konfigurerar på Kubelets cAdvisor om den inte redan är konfigurerad.

* Behållaren kräver att följande miljövariabler anges på behållaren för att kommunicera med Kubernetes API-tjänst `KUBERNETES_SERVICE_HOST` `KUBERNETES_PORT_443_TCP_PORT`i klustret för att samla in lagerdata - och .

>[!IMPORTANT]
>Den minsta agentversion som stöds för övervakning av hybridkuberneteskluster är ciprod10182019 eller senare.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande stöds officiellt med Azure Monitor för behållare.

- Miljöer: Kubernetes lokalt, AKS Engine på Azure och Azure Stack. Mer information finns i [AKS Engine på Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Versioner av Kubernetes och supportprincipen är desamma som versioner av [AKS som stöds](../../aks/supported-kubernetes-versions.md).
- Container Runtime: Docker och Moby
- Linux OS release för master och arbetade noder: Ubuntu (18,04 LTS och 16,04 LTS)
- Åtkomstkontroll stöds: Kubernetes RBAC och icke-RBAC

## <a name="enable-monitoring"></a>Aktivera övervakning

Aktivera Azure Monitor för behållare för hybridkubernetes-klustret består av att utföra följande steg i ordning.

1. Konfigurera din Log Analytics-arbetsyta med Container Insights-lösningen.

2. Aktivera Azure Monitor för behållare HELM-diagram med Log Analytics arbetsyta.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Så här lägger du till Azure Monitor Containers-lösningen

Du kan distribuera lösningen med den medföljande Azure Resource Manager-mallen med hjälp av Azure PowerShell-cmdlet `New-AzResourceGroupDeployment` eller med Azure CLI.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall läser du:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.59 eller senare. Om du vill `az --version`identifiera din version kör du . Om du behöver installera eller uppgradera Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

- **workspaceResourceId** - det fullständiga resurs-ID:t för din Log Analytics-arbetsyta.
- **workspaceRegion** - den region som arbetsytan skapas i, som också kallas **Plats** i arbetsytans egenskaper när du visar från Azure-portalen.

Om du först vill identifiera det fullständiga resurs-ID:t för din Log Analytics-arbetsyta som krävs för `workspaceResourceId` parametervärdet i filen **containerSolutionParams.json** utför du följande steg och kör sedan powershell-cmdlet- eller Azure CLI-kommandot för att lägga till lösningen.

1. Lista alla prenumerationer som du har åtkomst till med följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata kommer att likna följande:

    ```azurecli
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

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Leta reda på arbetsytans namn i utdata och kopiera sedan det fullständiga resurs-ID:et för den logganalysarbetsytan under **fält-ID.**

4. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Spara filen som containerSolution.json i en lokal mapp.

6. Klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Redigera värdena för **workspaceResourceId** med det värde som du kopierade i steg 3 och för **workspaceRegion** **kopierar regionvärdet** efter att ha kört Azure [CLI-kommandot az monitor log-analytics workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Spara den här filen som containerSolutionParams.json i en lokal mapp.

9. Nu är det dags att distribuera den här mallen.

   * Om du vill distribuera med Azure PowerShell använder du följande kommandon i mappen som innehåller mallen:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Om du vill distribuera med Azure CLI kör du följande kommandon:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```azurecli
       provisioningState       : Succeeded
       ```

       När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret.

## <a name="install-the-chart"></a>Installera diagrammet

Så här aktiverar du HELM-diagrammet:

1. Lägg till Azure-diagramdatabasen i din lokala lista genom att köra följande kommando:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Installera diagrammet genom att köra följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Om arbetsytan Log Analytics finns i Azure Kina kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Om log analytics-arbetsytan finns i Azure US Government kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Konfigurera insamling av agentdata

Med diagramversion 1.0.0 styrs inställningarna för insamling av agentdata från ConfigMap. Se dokumentation om inställningar för insamling av agentdata [här](container-insights-agent-config.md).

När du har distribuerat diagrammet kan du granska data för hybridkubernetes-klustret i Azure Monitor för behållare från Azure-portalen.  

>[!NOTE]
>Svarstiden för inmatning är cirka fem till tio minuter från agenten att genomföra i Azure Log Analytics-arbetsytan. Status för klustret visar värdet **Inga data** eller **Okänd** tills alla nödvändiga övervakningsdata är tillgängliga i Azure Monitor.

## <a name="troubleshooting"></a>Felsökning

Om du stöter på ett fel när du försöker aktivera övervakning för hybridkubernetes-klustret kopierar du PowerShell-skriptet [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) och sparar det i en mapp på datorn. Det här skriptet tillhandahålls för att identifiera och åtgärda de problem som uppstått. De problem som den är utformad för att upptäcka och försöka korrigera är följande:

* Den angivna log analytics-arbetsytan är giltig
* Log Analytics-arbetsytan är konfigurerad med Azure Monitor for Containers-lösningen. Om inte, konfigurera arbetsytan.
* OmsAgent replikaset pods körs
* OmsAgent daemonset pods körs
* OmsAgent Health service körs
* Logganalys-arbetsyte-ID:et och nyckeln som konfigurerats på den behållare agentmatchning med arbetsytan som Insight har konfigurerats med.
* Validera alla Linux-arbetsnoder har `kubernetes.io/role=agent` etikett för att schemalägga rs pod. Om den inte finns lägger du till den.
* Validera `cAdvisor secure port:10250` `unsecure port: 10255` eller öppnas på alla noder i klustret.

Om du vill köra med Azure PowerShell använder du följande kommandon i mappen som innehåller skriptet:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Nästa steg

När övervakning är aktiverad för att samla in hälso- och resursanvändning för ditt hybridkubernetes-kluster och arbetsbelastningar som körs på dem kan du lära dig [hur du använder](container-insights-analyze.md) Azure Monitor för behållare.
