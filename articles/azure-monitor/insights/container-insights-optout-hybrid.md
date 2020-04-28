---
title: Så här stoppar du övervakning av ditt hybrid Kubernetes-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du kan sluta övervaka ditt hybrid Kubernetes-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f2f3a8671c1f2baf60d399cc87f2f843dfee4f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196223"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Så här stoppar du övervakning av ditt hybrid kluster

När du har aktiverat övervakning av ditt Kubernetes-kluster som körs på Azure Stack eller lokalt kan du sluta övervaka klustret med Azure Monitor för behållare om du inte längre vill övervaka det. Den här artikeln visar hur du kan göra detta.  

## <a name="how-to-stop-monitoring-using-helm"></a>Stoppa övervakning med Helm

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

    Detta tar bort versionen från klustret. Du kan kontrol lera genom att `helm list` köra kommandot:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Konfigurations ändringen kan ta några minuter att slutföra. Eftersom Helm spårar dina versioner även efter att du har tagit bort dem, kan du granska ett klusters historik och till och med ta `helm rollback`bort en version med.

## <a name="next-steps"></a>Nästa steg

Om Log Analytics arbets ytan bara har skapats för att stödja övervakning av klustret och den inte längre behövs, måste du ta bort den manuellt. Om du inte är bekant med hur du tar bort en arbets yta, se [ta bort en Azure Log Analytics-arbetsyta](../../log-analytics/log-analytics-manage-del-workspace.md).
