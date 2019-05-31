---
title: Övervaka ett nytt kluster i Azure Kubernetes Service (AKS) | Microsoft Docs
description: Lär dig hur du aktiverar övervakning av ett nytt kluster i Azure Kubernetes Service (AKS) med Azure Monitor för behållare prenumerationen.
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
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077537"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Aktivera övervakning av ett nytt kluster i Azure Kubernetes Service (AKS)

Den här artikeln beskriver hur du ställer in Azure Monitor för behållare för att övervaka hanterade Kubernetes-kluster som finns på [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) som du förbereder att distribuera i din prenumeration.

Du kan aktivera övervakning av ett AKS-kluster med någon av metoderna som stöds:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Aktivera med hjälp av Azure CLI

Om du vill aktivera övervakning av ett nytt AKS-kluster som skapats med Azure CLI följer du steg i snabbstartsartikeln under avsnittet [skapa AKS-kluster](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.59 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

## <a name="enable-using-terraform"></a>Aktivera med hjälp av Terraform

Om du är [distribuerar en ny AKS-kluster med Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), du anger argument som krävs i profilen [att skapa en Log Analytics-arbetsyta](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) om du inte vill ange en befintlig. 

>[!NOTE]
>Om du väljer att använda Terraform, måste du köra Terraform Azure RM-providern version 1.17.0 eller senare.

Om du vill lägga till Azure Monitor för behållare i arbetsytan, se [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) och slutför profilen genom att inkludera den [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) och ange **oms_agent**. 

När du har aktiverat övervakning och alla åtgärder för konfiguration har slutförts kan övervaka du prestanda för ditt kluster på något av två sätt:

* Direkt i AKS-kluster genom att välja **hälsotillstånd** i den vänstra rutan.
* Genom att välja den **övervakaren behållareinsikter** panel på sidan AKS-kluster för det markerade klustret. I Azure Monitor, i den vänstra rutan väljer **hälsotillstånd**. 

  ![Alternativ för att välja Azure Monitor för behållare i AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

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
