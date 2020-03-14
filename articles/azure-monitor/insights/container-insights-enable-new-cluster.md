---
title: Övervaka ett nytt Azure Kubernetes service-kluster (AKS) | Microsoft Docs
description: Lär dig hur du aktiverar övervakning för ett nytt Azure Kubernetes service-kluster (AKS) med Azure Monitor for containers-prenumeration.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275443"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Aktivera övervakning av ett nytt Azure Kubernetes service-kluster (AKS)

Den här artikeln beskriver hur du konfigurerar Azure Monitor för behållare för att övervaka hanterade Kubernetes-kluster som finns i [Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/) som du förbereder att distribuera i din prenumeration.

Du kan aktivera övervakning av ett AKS-kluster med hjälp av en av de metoder som stöds:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Aktivera med hjälp av Azure CLI

Om du vill aktivera övervakning av ett nytt AKS-kluster som skapats med Azure CLI följer du stegen i artikeln snabb start under avsnittet [skapa AKS-kluster](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.74 eller senare. Du kan identifiera din version genom att köra `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Om du har installerat AKS-Preview CLI-tillägget 0.4.12 eller högre, tar du bort alla ändringar som du har gjort för att aktivera ett förhands gransknings tillägg eftersom det kan åsidosätta standard beteendet för Azure CLI eftersom AKS Preview-funktioner inte är tillgängliga i Azure US Governmnet-molnet.

## <a name="enable-using-terraform"></a>Aktivera med hjälp av Terraform

Om du [distribuerar ett nytt AKS-kluster med terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)anger du de argument som krävs i profilen [för att skapa en Log Analytics arbets yta](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) om du inte väljer att ange en befintlig. 

>[!NOTE]
>Om du väljer att använda Terraform, måste du köra Terraform Azure RM-providern version 1.17.0 eller senare.

Om du vill lägga till Azure Monitor för behållare i arbets ytan går du till [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) och slutför profilen genom att inkludera [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) och ange **oms_agent**. 

När du har aktiverat övervakning och alla åtgärder för konfiguration har slutförts kan övervaka du prestanda för ditt kluster på något av två sätt:

* Direkt i AKS-klustret genom att välja **hälsa** i det vänstra fönstret.
* Genom att välja panelen **övervaka behållar insikter** på kluster sidan AKS för det valda klustret. I Azure Monitor i det vänstra fönstret väljer du **hälsa**. 

  ![Alternativ för att välja Azure Monitor för behållare i AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa hälsomått för klustret. 

## <a name="verify-agent-and-solution-deployment"></a>Kontrollera distributionen av agenten och lösning
Med agent version *06072018* eller senare kan du kontrol lera att både agenten och lösningen har distribuerats. Du kan kontrollera endast agentdistribution med tidigare versioner av agenten.

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

Kör följande kommando för att kontrol lera att den Log Analytics agent version som släpptes innan *06072018* distribueras korrekt:  

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
Använd kommandot `aks show` för att få information om till exempel att lösningen är aktive rad eller inte, vad är inställningen för Log Analytics-arbetsytan och sammanfattningen av klustret.  

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

* Läs [fel söknings guiden](container-insights-troubleshoot.md) om du får problem när du försöker publicera lösningen

* När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt AKS-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.
