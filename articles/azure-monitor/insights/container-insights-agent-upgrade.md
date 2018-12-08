---
title: Uppgradera Azure Monitor för behållare (förhandsgranskning)-agenten | Microsoft Docs
description: Den här artikeln beskrivs hur du uppgraderar Log Analytics-agenten som används av Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 026bdd6a59dc84220e7e52707cee3c1971fba838
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104330"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Så här uppgraderar du i Azure Monitor för behållare (förhandsgranskning)-agenten
Azure Monitor för behållare använder en behållare version av Log Analytics-agenten för Linux. När en ny version av agenten släpps, uppgraderas agenten automatiskt på dina hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS).  

Om uppgraderingen av agenten misslyckas kan beskriver den här artikeln processen för att uppgradera agenten manuellt. Om du vill följa de versioner släpps, se [agenten lanseringsmeddelanden](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Uppgraderar agent på övervakade Kubernetes-kluster
Processen för att uppgradera agenten består av två okomplicerat steg. Det första steget är att inaktivera övervakning med Azure Monitor för behållare med hjälp av Azure CLI.  Följ stegen som beskrivs i den [inaktivera övervakning](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) artikeln. Med Azure CLI kan vi ta bort agenten från noder i klustret utan att påverka lösningen och motsvarande data som lagras på arbetsytan. 

>[!NOTE]
>När du utför den här aktiviteten för underhåll, noderna i klustret inte vidarebefordrar insamlade data och prestandavyer kommer inte att visa data mellan tid du tar bort agenten och installera den nya versionen. 
>

Om du vill installera den nya versionen av agenten, följer du stegen som beskrivs i den [integrera övervakning](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) artikel med Azure CLI, för att slutföra den här processen.  

När du har återaktiveras övervakning, kan det ta ungefär 15 minuter innan du kan visa uppdaterade hälsomått för klustret. För att verifiera agenten uppgraderats, kör du kommandot: `kubectl logs omsagent-484hw --namespace=kube-system`

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

## <a name="next-steps"></a>Nästa steg
Om det uppstår problem när du uppgraderar agenten kan du granska den [felsökningsguide](container-insights-troubleshoot.md) för support.