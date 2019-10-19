---
title: Så här hanterar du Azure Monitor för behållare agent | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar de vanligaste underhålls aktiviteterna med den container Log Analytics-agent som används av Azure Monitor för behållare.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/06/2018
ms.openlocfilehash: bfedd7989e71bcb8cf58cef7ad7122749350ae26
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554052"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Så här hanterar du Azure Monitor för behållare agent
Azure Monitor för behållare använder en behållar version av Log Analytics agent för Linux. Efter den första distributionen finns det rutiner eller valfria uppgifter som du kan behöva utföra under dess livs cykel. Den här artikeln beskriver hur du uppgraderar agenten manuellt och inaktiverar samlingen av miljövariabler från en viss behållare. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Så här uppgraderar du Azure Monitor för behållare agent
Azure Monitor för behållare använder en behållar version av Log Analytics agent för Linux. När en ny version av agenten släpps, uppgraderas agenten automatiskt på de hanterade Kubernetes-kluster som finns i Azure Kubernetes service (AKS).  

Om uppgraderingen av agenten Miss lyckas beskriver den här artikeln processen för att uppgradera agenten manuellt. Information om hur du följer de versioner som lanseras finns i [agent release-meddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Uppgraderar agent på övervakade Kubernetes-kluster
Processen för att uppgradera agenten består av två enkla steg. Det första steget är att inaktivera övervakning med Azure Monitor för behållare med hjälp av Azure CLI.  Följ stegen som beskrivs i artikeln [inaktivera övervakning](container-insights-optout.md?#azure-cli) . Med Azure CLI kan vi ta bort agenten från noderna i klustret utan att påverka lösningen och motsvarande data som lagras i arbets ytan. 

>[!NOTE]
>När du utför den här underhålls aktiviteten vidarebefordrar inte noderna i klustret insamlade data, och prestanda vyer visar inte data mellan den tidpunkt då du tar bort agenten och installerar den nya versionen. 
>

Om du vill installera den nya versionen av agenten följer du stegen som beskrivs i [Aktivera övervakning med Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)för att slutföra processen.  

När du har aktiverat övervakningen igen kan det ta ungefär 15 minuter innan du kan visa uppdaterade hälso mått för klustret. Verifiera att agenten har uppgraderats genom att köra kommandot: `kubectl logs omsagent-484hw --namespace=kube-system`

Status bör likna följande exempel där värdet för *OMI* och *omsagent* ska matcha den senaste versionen som anges i [agentens versions historik](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Så här inaktiverar du miljö variabel samling på en behållare
Azure Monitor för behållare samlar in miljövariabler från behållare som körs i en POD och visar dem i egenskaps rutan för den valda behållaren i vyn **behållare** . Du kan styra det här beteendet genom att inaktivera samling för en speciell behållare antingen under distributionen av AKS-klustret, eller genom att ange miljövariabeln *AZMON_COLLECT_ENV*. Den här funktionen är tillgänglig från agent versionen – ciprod11292018 och högre.  

Om du vill inaktivera insamlingen av miljövariabler på en ny eller befintlig behållare ställer du in variabeln **AZMON_COLLECT_ENV** med värdet **false** i Kubernetes Deployment yaml konfigurations fil.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Kör följande kommando för att tillämpa ändringen på din AKS-behållare: `kubectl apply -f  <path to yaml file>`.

Kontrol lera att konfigurations ändringen har påverkats genom att välja en behållare i vyn **behållare** i Azure Monitor för behållare och expandera **miljövariabler**i egenskaps panelen.  Avsnittet ska endast visa variabeln som skapades tidigare- **AZMON_COLLECT_ENV = false**. För alla andra behållare ska avsnittet miljövariabler lista alla miljövariabler som identifierats.   

Om du vill återaktivera identifieringen av miljövariablerna använder du samma process tidigare och ändrar värdet från **falskt** till **Sant**och kör sedan kommandot `kubectl` för att uppdatera behållaren.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Nästa steg
Om du får problem när du uppgraderar agenten kan du läsa [fel söknings guiden](container-insights-troubleshoot.md) för support.