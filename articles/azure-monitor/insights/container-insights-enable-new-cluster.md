---
title: Övervaka ett nytt AKS-kluster (Azure Kubernetes Service) | Microsoft-dokument
description: Lär dig hur du aktiverar övervakning för ett nytt AKS-kluster (Azure Kubernetes Service) med Azure Monitor för behållarprenumeration.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275443"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Aktivera övervakning av ett nytt AKS-kluster (Azure Kubernetes Service)

I den här artikeln beskrivs hur du konfigurerar Azure Monitor för behållare för att övervaka hanterade Kubernetes-kluster som finns på [Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/) som du förbereder för att distribuera i din prenumeration.

Du kan aktivera övervakning av ett AKS-kluster med någon av de metoder som stöds:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Aktivera med Azure CLI

Om du vill aktivera övervakning av ett nytt AKS-kluster som skapats med Azure CLI följer du steget i snabbstartsartikeln under avsnittet [Skapa AKS-kluster](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.74 eller senare. Om du vill `az --version`identifiera din version kör du . Om du behöver installera eller uppgradera Azure CLI läser du [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Om du har installerat VERSION 0.4.12 i AKS-förhandsversionen av CLI-tillägget tar du bort alla ändringar som du har gjort för att aktivera ett förhandsgranskningstillägg eftersom det kan åsidosätta standardbeteendet för Azure CLI eftersom AKS Preview-funktioner inte är tillgängliga i Azure US Governmnet-molnet.

## <a name="enable-using-terraform"></a>Aktivera med Terraform

Om du [distribuerar ett nytt AKS-kluster med Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)anger du de argument som krävs i profilen [för att skapa en Log Analytics-arbetsyta](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) om du inte väljer att ange en befintlig. 

>[!NOTE]
>Om du väljer att använda Terraform måste du köra Terraform Azure RM Provider version 1.17.0 eller senare.

Information om hur du lägger till Azure Monitor för behållare på arbetsytan finns [i azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) och fyller i profilen genom att inkludera [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) och ange **oms_agent**. 

När du har aktiverat övervakning och alla konfigurationsuppgifter har slutförts kan du övervaka klustrets prestanda på två sätt:

* Direkt i AKS-klustret genom att välja **Hälsa** i den vänstra rutan.
* Genom att välja panelen **Monitor Container-insikter** på AKS-klustersidan för det valda klustret. Välj **Hälsa**i den vänstra rutan i Azure Monitor . 

  ![Alternativ för att välja Azure Monitor för behållare i AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret. 

## <a name="verify-agent-and-solution-deployment"></a>Verifiera agent- och lösningsdistribution
Med agentversion *06072018* eller senare kan du kontrollera att både agenten och lösningen har distribuerats. Med tidigare versioner av agenten kan du bara verifiera agentdistributionen.

### <a name="agent-version-06072018-or-later"></a>Agentversion 06072018 eller senare
Kör följande kommando för att kontrollera att agenten har distribuerats. 

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata ska likna följande, vilket indikerar att den har distribuerats korrekt:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Kontrollera distributionen av lösningen genom att köra följande kommando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Utdata ska likna följande, vilket indikerar att den har distribuerats korrekt:

```
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

```
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

* Om du får problem när du försöker gå in på lösningen läser du [felsökningsguiden](container-insights-troubleshoot.md)

* När övervakning är aktiverad för att samla in hälso- och resursanvändning för AKS-kluster och arbetsbelastningar som körs på dem kan du lära dig [hur du använder](container-insights-analyze.md) Azure Monitor för behållare.
