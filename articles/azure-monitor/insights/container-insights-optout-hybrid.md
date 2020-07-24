---
title: Så här stoppar du övervakning av ditt hybrid Kubernetes-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du kan sluta övervaka ditt hybrid Kubernetes-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 8369c82b83cfbaa7128383c6203aaf584916cae9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091206"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Så här stoppar du övervakning av ditt hybrid kluster

När du har aktiverat övervakning av Kubernetes-klustret kan du stoppa övervakningen av klustret med Azure Monitor för behållare om du inte längre vill övervaka det. Den här artikeln visar hur du kan göra detta i följande miljöer:

- AKS-motor på Azure och Azure Stack
- OpenShift, version 4 och högre
- Azure Arc-aktiverade Kubernetes (förhandsversion)

## <a name="how-to-stop-monitoring-using-helm"></a>Stoppa övervakning med Helm

Följande steg gäller för följande miljöer:

- AKS-motor på Azure och Azure Stack
- OpenShift, version 4 och högre

1. För att först identifiera Azure Monitor för behållare Helm Chart release installerat i klustret kör du följande Helm-kommando.

    ```
    helm list
    ```

    Utdata ser ut ungefär så här:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-Release-1* representerar Helm-diagrammets version för Azure Monitor för behållare.

2. Om du vill ta bort diagram versionen kör du följande Helm-kommando.

    `helm delete <releaseName>`

    Exempel:

    `helm delete azmon-containers-release-1`

    Detta tar bort versionen från klustret. Du kan kontrol lera genom att köra `helm list` kommandot:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Konfigurations ändringen kan ta några minuter att slutföra. Eftersom Helm spårar dina versioner även efter att du har tagit bort dem, kan du granska ett klusters historik och till och med ta bort en version med `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Så här stoppar du övervakning av Arc-aktiverade Kubernetes

### <a name="using-powershell"></a>Använda PowerShell

1. Hämta och Spara skriptet till en lokal mapp som konfigurerar klustret med övervaknings tillägget med följande kommandon:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Konfigurera `$azureArcClusterResourceId` variabeln genom att ange motsvarande värden för `subscriptionId` `resourceGroupName` och `clusterName` som representerar resurs-ID för din Azure Arc-aktiverade Kubernetes-klusterresurs.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurera `$kubeContext` variabeln med **Kube-kontexten** för ditt kluster genom att köra kommandot `kubectl config get-contexts` . Om du vill använda den aktuella kontexten ställer du in värdet på `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Kör följande kommando för att stoppa övervakningen av klustret.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>Använda bash

1. Hämta och Spara skriptet till en lokal mapp som konfigurerar klustret med övervaknings tillägget med följande kommandon:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Konfigurera `azureArcClusterResourceId` variabeln genom att ange motsvarande värden för `subscriptionId` `resourceGroupName` och `clusterName` som representerar resurs-ID för din Azure Arc-aktiverade Kubernetes-klusterresurs.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurera `kubeContext` variabeln med **Kube-kontexten** för ditt kluster genom att köra kommandot `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Om du vill stoppa övervakningen av klustret finns det olika kommandon som baseras på distributions scenariot.

    Kör följande kommando för att stoppa övervakningen av klustret med hjälp av den aktuella kontexten.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Kör följande kommando för att stoppa övervakningen av klustret genom att ange en kontext

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>Nästa steg

Om Log Analytics arbets ytan bara har skapats för att stödja övervakning av klustret och den inte längre behövs, måste du ta bort den manuellt. Om du inte är bekant med hur du tar bort en arbets yta, se [ta bort en Azure Log Analytics-arbetsyta](../platform/delete-workspace.md).
