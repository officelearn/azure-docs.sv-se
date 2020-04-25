---
title: Konfigurera hybrid Kubernetes-kluster med Azure Monitor för behållare | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare för att övervaka Kubernetes-kluster som finns på Azure Stack eller annan miljö.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c0dbbf9f65aa96db1ebcd0b03552bba8d1f91863
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143178"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Konfigurera hybrid Kubernetes-kluster med Azure Monitor för behållare

Azure Monitor for containers innehåller omfattande övervaknings upplevelse för Azure Kubernetes service (AKS) och [AKS-motorn på Azure](https://github.com/Azure/aks-engine), som är ett självhanterat Kubernetes-kluster som finns på Azure. I den här artikeln beskrivs hur du aktiverar övervakning av Kubernetes-kluster som finns utanför Azure och uppnår en liknande övervaknings upplevelse.

## <a name="prerequisites"></a>Krav

Kontrol lera att du har följande innan du börjar:

* En Log Analytics-arbetsyta.

    Azure Monitor for containers stöder en Log Analytics arbets yta i de regioner som anges i Azure- [produkter efter region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Om du vill skapa en egen arbets yta kan den skapas via [Azure Resource Manager](../platform/template-workspace-configuration.md), via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)eller i [Azure Portal](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Det finns inte stöd för att övervaka flera kluster med samma kluster namn till samma Log Analytics-arbetsyta. Kluster namn måste vara unika.
    >

* Du är medlem i **rollen Log Analytics Contributor** för att aktivera övervakning av behållare. Mer information om hur du styr åtkomsten till en Log Analytics arbets yta finns i [Hantera åtkomst till arbets yta och loggdata](../platform/manage-access.md)

* [Helm-klienten](https://helm.sh/docs/using_helm/) för att publicera behållaren Azure Monitor för behållare för det angivna Kubernetes-klustret.

* Följande konfigurations information för proxy och brand väggar krävs för den behållar version av Log Analytics-agenten för Linux för att kunna kommunicera med Azure Monitor:

    |Agentresurs|Portar |
    |------|---------|   
    |*.ods.opinsights.azure.com |Port 443 |  
    |*.oms.opinsights.azure.com |Port 443 |  
    |*.blob.core.windows.net |Port 443 |  
    |*. dc.services.visualstudio.com |Port 443 |

* Den behållare som har `cAdvisor secure port: 10250` behållar agenten kräver Kubelet eller `unsecure port :10255` öppnas på alla noder i klustret för att samla in prestanda mått. Vi rekommenderar att du `secure port: 10250` konfigurerar på Kubelet-cAdvisor om den inte redan har kon figurer ATS.

* Agent agenten kräver att följande miljövariabler anges i behållaren för att kunna kommunicera med Kubernetes-API-tjänsten i klustret för att samla in inventerings data – `KUBERNETES_SERVICE_HOST` och. `KUBERNETES_PORT_443_TCP_PORT`

>[!IMPORTANT]
>Den lägsta agent version som stöds för övervakning av hybrid Kubernetes-kluster är ciprod10182019 eller senare.

## <a name="supported-configurations"></a>Konfigurationer som stöds

Följande stöds officiellt i Azure Monitor for containers.

- Miljöer: Kubernetes on-premises, AKS-motorn på Azure och Azure Stack. Mer information finns i [AKS-motorn på Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Versioner av Kubernetes och support policy är samma som versioner av [AKS som stöds](../../aks/supported-kubernetes-versions.md).
- Container Runtime: Docker och Moby
- Linux OS-version för Master and arbetaded Nodes: Ubuntu (18,04 LTS och 16,04 LTS)
- Åtkomst kontroll som stöds: Kubernetes RBAC och icke-RBAC

## <a name="enable-monitoring"></a>Aktivera övervakning

Att aktivera Azure Monitor för behållare för Hybrid Kubernetes-klustret består av att utföra följande steg i ordning.

1. Konfigurera din Log Analytics arbets yta med container Insights-lösning.

2. Aktivera Azure Monitor for containers HELM-diagrammet med Log Analytics-arbetsytan.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Lägga till Azure Monitor containers-lösningen

Du kan distribuera lösningen med den tillhandahållna Azure Resource Manager-mallen med hjälp av Azure PowerShell `New-AzResourceGroupDeployment` -cmdlet eller med Azure CLI.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall, se:

* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.59 eller senare. För att identifiera din version, `az --version`kör. Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Den här metoden inkluderar två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parameter värden som du konfigurerar för att ange följande:

- **workspaceResourceId** – det fullständiga resurs-ID: t för din Log Analytics-arbetsyta.
- **workspaceRegion** – den region som arbets ytan skapas i, vilket även kallas **plats** i egenskaperna för arbets ytan vid visning från Azure Portal.

För att först identifiera det fullständiga resurs-ID: t för din Log Analytics `workspaceResourceId` -arbetsyta som krävs för parametervärdet i filen **containerSolutionParams. JSON** , utför följande steg och kör sedan PowerShell-cmdleten eller Azure CLI-kommandot för att lägga till lösningen.

1. Lista alla prenumerationer som du har åtkomst till med hjälp av följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Kopiera värdet för **SubscriptionId**.

2. Växla till den prenumeration som är värd för Log Analytics arbets ytan med hjälp av följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. I följande exempel visas listan över arbets ytor i dina prenumerationer i standardformatet JSON.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    I utdata letar du reda på arbets ytans namn och kopierar sedan det fullständiga resurs-ID: t för den Log Analytics arbets ytan under fält **-ID: t**.

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

5. Spara filen som containerSolution. json i en lokal mapp.

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

7. Redigera värdena för **workspaceResourceId** med värdet som du kopierade i steg 3 **och kopiera sedan** värdet **region** efter att du kört kommandot AZ i Azure CLI för att [övervaka logg analys arbets ytan Visa](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Spara filen som containerSolutionParams. json i en lokal mapp.

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

       Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

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
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

       ```azurecli
       provisioningState       : Succeeded
       ```

       När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

## <a name="install-the-chart"></a>Installera diagrammet

>[!NOTE]
>Följande kommandon gäller endast för Helm version 2. Användning av parametern--name kan inte användas med Helm version 3.

Gör så här för att aktivera HELM-diagrammet:

1. Lägg till Azure Charts-lagringsplatsen i din lokala lista genom att köra följande kommando:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Installera diagrammet genom att köra följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Om arbets ytan Log Analytics är i Azure Kina kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Om arbets ytan Log Analytics är i Azure amerikanska myndigheter kör du följande kommando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Konfigurera agent data insamling

Stjärnor med diagram version 1.0.0 styrs inställningarna för agent data insamlingen från ConfigMap. Läs dokumentationen om inställningarna för insamling av agent data [här](container-insights-agent-config.md).

När du har distribuerat diagrammet kan du granska data för ditt hybrid Kubernetes-kluster i Azure Monitor för behållare från Azure Portal.  

>[!NOTE]
>Inmatnings fördröjningen är cirka fem till tio minuter från agenten som ska genomföras i Azure Log Analytics-arbetsytan. Status för klustret visar värdet **inga data** eller **okända** förrän alla nödvändiga övervaknings data är tillgängliga i Azure Monitor.

## <a name="troubleshooting"></a>Felsökning

Om det uppstår ett fel vid försök att aktivera övervakning för ditt hybrid Kubernetes-kluster, kopierar du PowerShell-skriptet [TroubleshootError_nonAzureK8s. ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) och sparar det i en mapp på datorn. Det här skriptet används för att identifiera och åtgärda de problem som uppstått. Problemen som är utformade för att identifiera och försöka korrigera är följande:

* Den angivna Log Analyticss arbets ytan är giltig
* Log Analytics arbets ytan konfigureras med Azure Monitor för container lösning. Annars konfigurerar du arbets ytan.
* OmsAgent REPLICASET-poddar körs
* OmsAgent daemonset-poddar körs
* OmsAgent-tjänsten för hälso tillstånd körs
* Log Analytics arbetsyte-ID och nyckel som kon figurer ATS på behållarens agent matchar i arbets ytan som insikten är konfigurerad med.
* Verifiera att alla Linux Worker- `kubernetes.io/role=agent` noder har etikett att schemalägga RS-pod. Lägg till den om den inte finns.
* Verifiera `cAdvisor secure port:10250` eller `unsecure port: 10255` har öppnats på alla noder i klustret.

Om du vill köra med Azure PowerShell använder du följande kommandon i mappen som innehåller skriptet:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Nästa steg

När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt hybrid Kubernetes-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.
