---
title: "Självstudiekurs för Azure Container Service – Övervaka Kubernetes"
description: "Självstudiekurs om Azure Container Service – Övervaka Kubernetes med Microsoft Operations Management Suite (OMS)"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 965ce4b7e154684fc1d171c90f17498afc828a66
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Övervaka ett Kubernetes-kluster med Operations Management Suite

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Det är viktigt att du övervakar Kubernetes-klustren och behållarna, särskilt när du hanterar ett produktionskluster i skala med flera program. 

Du kan dra nytta av flera Kubernetes-övervakningslösningar från antingen Microsoft eller andra leverantörer. I den här självstudiekursen övervakar du Kubernetes-kluster med behållarlösningen i [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md), Microsofts molnbaserade IT-hanteringslösning. (OMS-behållarlösningen är i förhandsvisning.)

Den här självstudiekursen, som är del sju av sju, tar upp följande uppgifter:

> [!div class="checklist"]
> * Hämta inställningar för OMS-arbetsyta
> * Ställa in OMS-agenterna på Kubernetes-noderna
> * Åtkomst till övervakningsinformation i OMS- eller Azure-portalen

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades. 

Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa behållaravbildningar](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Hämta inställningar för arbetsyta

När du har åtkomst till [OMS-portalen](https://mms.microsoft.com) navigerar du till **Inställningar** > **Anslutna källor** > **Linux-servrar**. Där hittar du *arbetsyte-ID* och en primär eller sekundär *arbetsytenyckel*. Anteckna dessa värden. Du behöver dem när du ställer in OMS-agenterna i klustret.

## <a name="set-up-oms-agents"></a>Ställ in OMS-agenter

Här är en YAML-fil för att ställa in OMS-agenter för Linux-klusternoderna. Det skapar ett Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) som kör en enda identisk pod på varje klusternod. Resursen DaemonSet är idealiskt för distribution av en övervakningsagent. 

Spara följande text till en fil med namnet `oms-daemonset.yaml` och ersätt platshållarvärdena för *myWorkspaceID* och *myWorkspaceKey* med arbetsyte-ID och nyckel för OMS. (I produktion kan du koda värdena som hemligheter.)

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

Skapa ett DaemonSet med följande kommando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Kör följande om du vil se det som DaemonSet har skapat:

```azurecli-interactive
kubectl get daemonset
```

De utdata som genereras liknar följande:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

När agenterna har körts tar det flera minuter för OMS att mata in och bearbeta data.

## <a name="access-monitoring-data"></a>Åtkomst till övervakningsdata

Visa och analysera övervakningsdata för OMS-behållaren med [behållarlösningen](../../log-analytics/log-analytics-containers.md) i antingen OMS-portalen eller Azure-portalen. 

När du vill installera behållarlösningen via [OMS-portalen](https://mms.microsoft.com) öppnar du **lösningsgalleriet**. Lägg sedan till **behållarlösning**. Du kan också lägga till behållarlösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

I OMS-portalen tittar du efter en **Containers**-sammafattningspanel på OMS-instrumentpanelen. Klicka på panelen. Nu visas information om behållarhändelser, fel, status, avbildningslager och processor- och minnesanvändning. Om du vill ha mer detaljerad information klickar du på en rad på valfri panel eller utför en [loggsökning](../../log-analytics/log-analytics-log-searches.md).

![Behållarinstrumentpanelen i OMS-portalen](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

I Azure-portalen navigerar du på samma sätt till **Log Analytics** och väljer namnet på arbetsytan. När du vill visa sammanfattningspanelen **Behållare** klickar du på **Lösningar** > **Behållare**. Klicka på panelen om du vill se mer information.

I [Azure Log Analytics-dokumentationen](../../log-analytics/index.yml) finns det detaljerad information om att fråga och analysera övervakningsdata.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du övervakat ditt Kubernetes-kluster med OMS. Här är några av uppgifterna:

> [!div class="checklist"]
> * Hämta inställningar för OMS-arbetsyta
> * Ställa in OMS-agenterna på Kubernetes-noderna
> * Åtkomst till övervakningsinformation i OMS- eller Azure-portalen


Följ den här länken om du vill se inbyggda skriptexempel för Container Service.

> [!div class="nextstepaction"]
> [Skriptexempel för Azure Container Service](cli-samples.md)
