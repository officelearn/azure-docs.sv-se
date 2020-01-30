---
title: Så här hanterar du i Azure Monitor för behållare agent | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar de vanligaste uppgifterna för underhåll med behållare Log Analytics-agenten används av Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843964"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Så här hanterar du i Azure Monitor för behållare agent

Azure Monitor för behållare använder en behållare version av Log Analytics-agenten för Linux. Efter den första distributionen finns rutinen eller valfria aktiviteter som du kan behöva utföra under dess livscykel. Den här artikeln innehåller information om hur du manuellt uppgradera agenten och inaktivera insamling av miljövariabler från en viss behållare. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Så här uppgraderar du i Azure Monitor för behållare agent

Azure Monitor för behållare använder en behållare version av Log Analytics-agenten för Linux. När en ny version av agenten lanseras uppgraderas agenten automatiskt på de hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS) och Azure Red Hat OpenShift. För ett [hybrid Kubernetes-kluster](container-insights-hybrid-setup.md) hanteras inte agenten och du måste uppgradera agenten manuellt.

Om agent uppgraderingen Miss lyckas för ett kluster som finns på AKS, beskriver den här artikeln även processen för att uppgradera agenten manuellt. Om du vill följa de versioner släpps, se [agenten lanseringsmeddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Uppgradera agenten på det övervakade Kubernetes-klustret

Processen för att uppgradera agenten i kluster, förutom Azure Red Hat OpenShift, består av två enkla steg. Det första steget är att inaktivera övervakning med Azure Monitor för behållare med hjälp av Azure CLI. Följ stegen som beskrivs i den [inaktivera övervakning](container-insights-optout.md?#azure-cli) artikeln. Med Azure CLI kan vi ta bort agenten från noder i klustret utan att påverka lösningen och motsvarande data som lagras på arbetsytan. 

>[!NOTE]
>När du utför den här aktiviteten för underhåll, noderna i klustret inte vidarebefordrar insamlade data och prestandavyer kommer inte att visa data mellan tid du tar bort agenten och installera den nya versionen. 
>

Om du vill installera den nya versionen av agenten följer du stegen som beskrivs i [Aktivera övervakning med Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)för att slutföra processen.  

När du har aktiverat övervakningen igen kan det ta ungefär 15 minuter innan du kan visa uppdaterade hälso mått för klustret. För att verifiera agenten uppgraderats, kör du kommandot: `kubectl logs omsagent-484hw --namespace=kube-system`

Statusen bör likna följande exempel där värdet för *omi* och *omsagent* måste matcha den senaste versionen som anges i den [historik för versionen av agenten](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Uppgradera agenten på Hybrid Kubernetes-kluster

Processen för att uppgradera agenten på ett Kubernetes-kluster som finns lokalt, AKS-motorn på Azure och Azure Stack kan utföras genom att köra följande kommando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Om arbets ytan Log Analytics är i Azure Kina kör du följande kommando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Om arbets ytan Log Analytics är i Azure amerikanska myndigheter kör du följande kommando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Så här inaktiverar du miljön variabelsamlingen i en behållare

Azure Monitor för behållare som samlar in miljövariabler från behållare som körs i en pod och visar dem i egenskapsrutan i den valda behållaren i den **behållare** vy. Du kan styra det här beteendet genom att inaktivera samling för en speciell behållare antingen under distributionen av Kubernetes-klustret, eller genom att ange miljövariabeln *AZMON_COLLECT_ENV*. Den här funktionen är tillgänglig från agentversionen – ciprod11292018 och högre.  

Om du vill inaktivera insamling av miljövariabler på en ny eller befintlig behållare ställer du in variabeln **AZMON_COLLECT_ENV** med värdet **false** i yaml-konfigurationsfilen för Kubernetes-distributionen. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Kör följande kommando för att tillämpa ändringen på andra Kubernetes-kluster än Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>`. Om du vill redigera ConfigMap och tillämpa den här ändringen för kluster med ett OpenShift-kluster i Azure Red Hat kör du kommandot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Detta öppnar standard text redigeraren. När du har angett variabeln sparar du filen i redigeraren.

Kontrol lera att konfigurations ändringen har påverkats genom att välja en behållare i vyn **behållare** i Azure Monitor för behållare och expandera **miljövariabler**i egenskaps panelen.  Avsnittet ska endast visa variabeln som skapades tidigare- **AZMON_COLLECT_ENV = false**. För alla andra behållare bör avsnittet miljövariabler lista de miljövariabler som har identifierats.

Om du vill återaktivera identifieringen av miljövariablerna använder du samma process tidigare och ändrar värdet från **falskt** till **Sant**och kör sedan kommandot `kubectl` för att uppdatera behållaren.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Nästa steg

Om det uppstår problem när du uppgraderar agenten kan du granska den [felsökningsguide](container-insights-troubleshoot.md) för support.
