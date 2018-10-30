---
title: Självstudiekurs för Azure Container Service – Övervaka Kubernetes
description: Självstudiekurs för Azure Container Service – Övervaka Kubernetes med Log Analytics
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 249e286b16b78643c33b567f705a4c92991f5553
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404876"
---
# <a name="monitor-a-kubernetes-cluster-with-log-analytics"></a>Övervaka ett Kubernetes-kluster med Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Det är viktigt att du övervakar Kubernetes-klustren och containrarna, särskilt när du hanterar ett produktionskluster i skala med flera program.

Du kan dra nytta av flera Kubernetes-övervakningslösningar från antingen Microsoft eller andra leverantörer. I den här självstudiekursen övervakar du Kubernetes-kluster med Containers-lösningen i [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md), Microsofts molnbaserade IT-hanteringslösning. (Containers-lösningen är i förhandsversion.)

Den här självstudiekursen, som är del sju av sju, tar upp följande uppgifter:

> [!div class="checklist"]
> * Hämta inställningar för Log Analytics-arbetsytan
> * Konfigurera Log Analytics-agenter på Kubernetes-noderna
> * Åtkomst till övervakningsinformation i Log Analytics-portalen eller Azure Portal

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades.

Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa containeravbildningar](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Hämta inställningar för arbetsyta

När du har åtkomst till [Log Analytics-portalen](https://mms.microsoft.com) går du till **Inställningar** > **Anslutna källor** > **Linux-servrar**. Där hittar du *arbetsyte-ID* och en primär eller sekundär *arbetsytenyckel*. Anteckna dessa värden. Du behöver dem när du ställer in Log Analytics-agenterna i klustret.

## <a name="create-kubernetes-secret"></a>Skapa Kubernetes-hemlighet

Lagra inställningarna för Log Analytics-arbetsytan i en Kubernetes-hemlighet som du namnger `omsagent-secret` med hjälp av kommandot [kubectl create secret][kubectl-create-secret]. Uppdatera `WORKSPACE_ID` med ditt ID för Log Analytics-arbetsytan och `WORKSPACE_KEY` med arbetsytenyckeln.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Konfigurera Log Analytics-agenter

Du kan använda följande Kubernetes-manifestfil för att konfigurera containerns övervakningsagenter på ett Kubernetes-kluster. Det skapar ett Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) som kör en enda identisk pod på varje klusternod.

Spara följande text i en fil med namnet `oms-daemonset.yaml`.

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
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
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
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
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

När agenterna har körts tar det flera minuter för Log Analytics att mata in och bearbeta data.

## <a name="access-monitoring-data"></a>Åtkomst till övervakningsdata

Visa och analysera övervakningsdata för containern med [containerlösningen](../../log-analytics/log-analytics-containers.md) i Log Analytics-portalen eller Azure Portal.

Installera containerlösningen med [Log Analytics-portalen](https://mms.microsoft.com) genom att gå till **Lösningsgalleri**. Lägg sedan till **containerlösning**. Du kan också lägga till containerlösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Sök efter en **Containers**-sammanfattningspanel på instrumentpanelen i Log Analytics-portalen. Klicka på panelen. Nu visas information om containerhändelser, fel, status, avbildningslager och processor- och minnesanvändning. Om du vill ha mer detaljerad information klickar du på en rad på valfri panel eller utför en [loggsökning](../../log-analytics/log-analytics-log-searches.md).

![Containers-instrumentpanelen i Azure-portalen](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

I Azure-portalen navigerar du på samma sätt till **Log Analytics** och väljer namnet på arbetsytan. Om du vill visa **Containers**-sammanfattningspanelen klickar du på **Lösningar** > **Containers**. Klicka på panelen om du vill se mer information.

I [Azure Log Analytics-dokumentationen](../../log-analytics/log-analytics-queries.md) finns det detaljerad information om att fråga och analysera övervakningsdata.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du övervakat ditt Kubernetes-kluster med Log Analytics. Här är några av uppgifterna:

> [!div class="checklist"]
> * Hämta inställningar för Log Analytics-arbetsytan
> * Konfigurera Log Analytics-agenter på Kubernetes-noderna
> * Åtkomst till övervakningsinformation i Log Analytics-portalen eller Azure Portal


Följ den här länken om du vill se inbyggda skriptexempel för Container Service.

> [!div class="nextstepaction"]
> [Skriptexempel för Azure Container Service](cli-samples.md)
