---
title: Publicera Azure-båge med Azure Monitor for containers (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Publicera Azure-båge med Azure Monitor för behållare
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680743"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Onboard Azure Monitor for containers with ARC (för hands version)

Inbyggda [Azure Monitor-behållare](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) till Azure Arc-aktiverade Kubernetes-kluster.

## <a name="before-you-begin"></a>Innan du börjar

* [Kubernetes-versioner](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Linux-distributioner för klustrets (Master & Worker) noder – Ubuntu (18,04 LTS och 16,04 LTS)
* Minsta deltagar behörighet för RBAC-rollen i Azure-prenumerationen för Azure Arc-Kubernetes-klustret
* Fullständigt kvalificerat Azure-resurs-ID för det aktiverade Kubernetes-klustret i Azure-bågen
* Kubeconfig-kontexten för Kubernetes-klustret
* Övervaknings agenten kräver att cAdvisor på Kubelet körs på antingen en säker port: 10250 eller en osäker port: 10255 på alla noder för att hämta prestanda måtten   
* Vi rekommenderar att du konfigurerar Kubelet cAdvisor-porten för att skydda porten: 10250.
* Övervaknings agenten kräver följande utgående portar och domäner för att skicka övervaknings data till Azure Monitor Server del (om de blockeras av proxy/brand vägg)
    -  *. ods.opinsights.azure.com 443
    -  *. oms.opinsights.azure.com 443
    -  *. blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Publicering

### <a name="using-helm-chart"></a>Använda HELM-diagram

### <a name="option-1-using-powershell--script"></a>Alternativ 1: använda PowerShell-skript

1. Hämta onboarding-skriptet

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Installera [PowerShell Core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) på din dev-dator för att köra skriptet för PowerShell-onboarding.

3. Logga in till Azure

    ```console
    az login --use-device-code
    ```

4. Kör skriptet nedan med ditt kluster för Azure båg K8s-kluster ResourceId och kontexten för Kubernetes-klustret

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Alternativ 2: använda bash-skript

> **Tips:** Skriptet använder bash 4-funktioner, så se till att din bash är uppdaterad. Du kan kontrol lera den aktuella versionen med `bash --version` .

1. Hämta onboarding-skriptet

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Kör skriptet nedan med ditt kluster för Azure båg K8s-kluster ResourceId och kontexten för Kubernetes-klustret

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Konfigurera agent data insamling

Som standard samlar agenten inte in STDOUT-och stderr-loggar för behållare i Kube-systemets namnrymd.
Läs om hur https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config du konfigurerar agent med önskade data insamlings inställningar.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurera kasse ring av Prometheus-mått

Azure Monitor för behållare tar över Prometheus-mått och inmatningar till Azure Monitor-backend-servern.
Mer information https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration om hur du konfigurerar Prometheus-kassationer hittar du i.

## <a name="user-interface"></a>Användargränssnitt

Navigera till https://aka.ms/azmon-containers-azurearc för att visa det inbyggda klustret.

## <a name="disable-monitoring"></a>Inaktivera övervakning

Om du vill inaktivera övervakning av någon anledning kan du helt enkelt ta bort Azure Monitor for containers HELM-diagrammet för att sluta samla in och mata in övervaknings data till Azure Monitor för behållarens Server del.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)

