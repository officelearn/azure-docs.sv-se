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
ms.date: 11/30/2018
ms.author: magoedte
ms.openlocfilehash: 4957665391dab5a88941fa84c03152e33718de3b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095269"
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

## <a name="error-messages"></a>Felmeddelanden

I tabellen nedan sammanfattas kända fel kan uppstå när du använder Azure Monitor för behållare.

| Felmeddelanden  | Åtgärd |  
| ---- | --- |  
| Felmeddelande `No data for selected filters`  | Det kan ta lite tid att upprätta övervakning dataflöde för nyligen skapade kluster. Låt minst 10 – 15 minuter innan data visas för klustret. |   
| Felmeddelande `Error retrieving data` | Medan Azure Kubenetes Service-kluster att konfigurera för hälso- och prestandaövervakning, upprättas en anslutning mellan kluster och Azure Log Analytics-arbetsyta. En Log Analytics-arbetsyta används för att lagra alla övervakningsdata för klustret. Det här felet kan uppstå när Log Analytics-arbetsytan har tagits bort eller tappas bort. Kontrollera om din arbetsyta är tillgänglig genom att granska [hantera åtkomst](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json#workspace-information). Om arbetsytan saknas, behöver du re publicera ditt kluster med Azure Monitor för behållare. Att re publicera behöver du [inaktivera](container-insights-optout.md) övervakning för klustret och [aktivera](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-for-a-new-cluster) Azure Monitor för behållare igen. |  
| `Error retrieving data` När du lägger till Azure Monitor för behållare via az aks cli | När onboarding med `az aks cli`, mycket sällan Azure Monitor för behållare kanske inte korrekt integrerats. Kontrollera om lösningen har publicerats. Gör detta genom att gå till Log Analytics-arbetsytan och se om lösningen är tillgänglig genom att välja **lösningar** från fönstret till vänster. För att lösa problemet måste du distribuera lösningen igen genom att följa anvisningarna [så här distribuerar du Azure Monitor för behållare](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json) |  

För att diagnosticera problemet, har vi lagt till ett felsökning skript som är tillgängliga [här](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script).  

## <a name="next-steps"></a>Nästa steg
Med övervakning aktiverat för att avbilda hälsomått för både noder i AKS och poddar är dessa health-mått tillgängliga i Azure-portalen. Läs hur du använder Azure Monitor för behållare i [visa Azure Kubernetes Service health](container-insights-analyze.md).