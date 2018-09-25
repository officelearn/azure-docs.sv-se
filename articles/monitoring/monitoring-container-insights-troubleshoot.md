---
title: Hur du felsöker Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs hur du kan felsöka och lösa problem med Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: de7ae5788224b83105e4dc9a24aea35c8b841c88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986735"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Felsökning Azure Monitor för behållare

När du konfigurerar övervakning av ditt Azure Kubernetes Service (AKS)-kluster med Azure Monitor för behållare kan du kan stöta på ett problem som förhindrar insamling av data eller rapporterar status. Den här artikeln beskriver några vanliga problem och felsökning.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor för behållare har aktiverats men inte rapporterar någon information
Om Azure Monitor för behållare har aktiverats och konfigurerats, men du kan inte visa statusinformation eller inga resultat som returneras från en Log Analytics log-fråga, kan du diagnostisera problemet genom att följa dessa steg: 

1. Kontrollera statusen för agenten genom att köra kommandot: 

    `kubectl get ds omsagent --namespace=kube-system`

    Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Kontrollera distributionsstatusen med agentversion *06072018* eller senare med kommandot:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Utdata bör likna följande exempel, som anger att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Kontrollera status för poden att verifiera att den körs med hjälp av kommandot: `kubectl get pods --namespace=kube-system`

    Utdata bör likna följande exempel med statusen *kör* för omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Loggarna för agenten. När behållare agenten distribueras, kör en snabb kontroll genom att köra OMI kommandon och visar versionen av agenten och providern. 

5. Kontrollera att agenten har integrerats har genom att köra kommandot: `kubectl logs omsagent-484hw --namespace=kube-system`

    Statusen bör likna följande exempel:

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Nästa steg
Med övervakning aktiverat för att avbilda hälsomått för både noder i AKS och poddar är dessa health-mått tillgängliga i Azure-portalen. Läs hur du använder Azure Monitor för behållare i [visa Azure Kubernetes Service health](monitoring-container-insights-analyze.md).