---
title: Så här hanterar du Azure Monitor för behållare agent | Microsoft-dokument
description: I den här artikeln beskrivs hantering av de vanligaste underhållsuppgifterna med den behållaren Log Analytics-agent som används av Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275326"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Hantera Azure Monitor för behållare agent

Azure Monitor för behållare använder en behållare version av Log Analytics-agenten för Linux. Efter den första distributionen finns det rutin- eller valfria uppgifter som du kan behöva utföra under livscykeln. I den här artikeln beskrivs hur agenten uppgraderas manuellt och inaktiverar insamling av miljövariabler från en viss behållare. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Uppgradera Azure Monitor för behållare agent

Azure Monitor för behållare använder en behållare version av Log Analytics-agenten för Linux. När en ny version av agenten släpps uppgraderas agenten automatiskt på dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS) och Azure Red Hat OpenShift. För ett [hybridkubernetes-kluster](container-insights-hybrid-setup.md) hanteras inte agenten och du måste uppgradera agenten manuellt.

Om agentuppgraderingen misslyckas för ett kluster som finns på AKS beskrivs även processen för att uppgradera agenten manuellt. Information om hur du följer de versioner som har släppts finns i [meddelanden om agentrelease](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Uppgradera agent på övervakat Kubernetes-kluster

Processen för att uppgradera agenten på kluster, andra än Azure Red Hat OpenShift, består av två steg framåt. Det första steget är att inaktivera övervakning med Azure Monitor för behållare som använder Azure CLI. Följ stegen som beskrivs i artikeln [Inaktivera övervakning.](container-insights-optout.md?#azure-cli) Med hjälp av Azure CLI kan vi ta bort agenten från noderna i klustret utan att påverka lösningen och motsvarande data som lagras på arbetsytan. 

>[!NOTE]
>Medan du utför den här underhållsaktiviteten vidarebefordrar noderna i klustret inte insamlade data och prestandavyer visar inte data mellan den tidpunkt då du tar bort agenten och installerar den nya versionen. 
>

Om du vill installera den nya versionen av agenten följer du stegen som beskrivs i [aktivera övervakning med Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)för att slutföra den här processen.  

När du har aktiverat övervakning igen kan det ta ungefär 15 minuter innan du kan visa uppdaterade hälsomått för klustret. Om du vill verifiera att agenten har uppgraderats kör du kommandot:`kubectl logs omsagent-484hw --namespace=kube-system`

Statusen ska likna följande exempel, där värdet för *omi* och *omsagent* ska matcha den senaste versionen som anges i [agentutgivningshistoriken](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Uppgradera agent på hybrid Kubernetes kluster

Processen för att uppgradera agenten i ett Kubernetes-kluster som finns lokalt, AKS-motorn på Azure och Azure Stack kan slutföras genom att köra följande kommando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Om arbetsytan Log Analytics finns i Azure Kina kör du följande kommando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Om log analytics-arbetsytan finns i Azure US Government kör du följande kommando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Inaktiverar miljövariabelsamling på en behållare

Azure Monitor för behållare samlar in miljövariabler från behållarna som körs i en pod och presenterar dem i egenskapsfönstret för den valda behållaren i **vyn Behållare.** Du kan styra det här beteendet genom att inaktivera samlingen för en viss behållare, antingen under distributionen av Kubernetes-klustret eller efter genom att ställa in miljövariabeln *AZMON_COLLECT_ENV*. Den här funktionen är tillgänglig från agentversionen – ciprod11292018 och högre.  

Om du vill inaktivera insamling av miljövariabler på en ny eller befintlig behållare anger du variabeln **AZMON_COLLECT_ENV** med värdet **False** i kubernetes-distributions-yaml-konfigurationsfilen. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Kör följande kommando för att tillämpa ändringen på Kubernetes-kluster `kubectl apply -f  <path to yaml file>`än Azure Red Hat OpenShift): . Om du vill redigera ConfigMap och tillämpa den här ändringen för Azure Red Hat OpenShift-kluster kör du kommandot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Då öppnas standardtextredigeraren. När du har ställt in variabeln sparar du filen i redigeraren.

Om du vill kontrollera att konfigurationsändringen trädde i kraft väljer du en behållare i vyn **Behållare** i Azure Monitor för behållare och expanderar **miljövariabler**på egenskapspanelen .  Avsnittet ska bara visa den variabel som skapats tidigare - **AZMON_COLLECT_ENV=FALSKT**. För alla andra behållare bör avsnittet Miljövariabler lista alla miljövariabler som identifierats.

Om du vill aktivera identifieringen av miljövariablerna igen använder du samma process tidigare `kubectl` och ändrar värdet från **Falskt** till **Sant**och kör sedan kommandot för att uppdatera behållaren igen.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Nästa steg

Om du får problem när du uppgraderar agenten läser du [felsökningsguiden](container-insights-troubleshoot.md) för support.
