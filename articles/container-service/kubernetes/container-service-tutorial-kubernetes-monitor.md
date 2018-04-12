---
title: Självstudiekurs för Azure Container Service – Övervaka Kubernetes
description: Självstudiekurs för Azure Container Service – Övervaka Kubernetes med Log Analytics
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 5b11c3cdf3eb457ade111d0908a2dac867ac1278
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-a-kubernetes-cluster-with-log-analytics"></a>Övervaka ett Kubernetes-kluster med Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Det är viktigt att du övervakar Kubernetes-klustren och behållarna, särskilt när du hanterar ett produktionskluster i skala med flera program.

Du kan dra nytta av flera Kubernetes-övervakningslösningar från antingen Microsoft eller andra leverantörer. I den här självstudiekursen övervakar du Kubernetes-kluster med Containers-lösningen i [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md), Microsofts molnbaserade IT-hanteringslösning. (Containers-lösningen är i förhandsversion.)

Den här självstudiekursen, som är del sju av sju, tar upp följande uppgifter:

> [!div class="checklist"]
> * Hämta inställningar för Log Analytics-arbetsytan
> * Ställa in OMS-agenterna på Kubernetes-noderna
> * Åtkomst till övervakningsinformation i OMS- eller Azure-portalen

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades.

Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa behållaravbildningar](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Hämta inställningar för arbetsyta

När du har åtkomst till [OMS-portalen](https://mms.microsoft.com) navigerar du till **Inställningar** > **Anslutna källor** > **Linux-servrar**. Där hittar du *arbetsyte-ID* och en primär eller sekundär *arbetsytenyckel*. Anteckna dessa värden. Du behöver dem när du ställer in OMS-agenterna i klustret.

## <a name="create-kubernetes-secret"></a>Skapa Kubernetes-hemlighet

Lagra inställningarna för Log Analytics-arbetsytan i en Kubernetes-hemlighet som du namnger `omsagent-secret` med hjälp av kommandot [kubectl create secret][kubectl-create-secret]. Uppdatera `WORKSPACE_ID` med ditt ID för Log Analytics-arbetsytan och `WORKSPACE_KEY` med arbetsytenyckeln.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-oms-agents"></a>Ställ in OMS-agenter

Du kan använda följande Kubernetes-manifestfil för att konfigurera behållarens övervakningsagenter på ett Kubernetes-kluster. Det skapar ett Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) som kör en enda identisk pod på varje klusternod.

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

Visa och analysera övervakningsdata för behållaren med [behållarlösningen](../../log-analytics/log-analytics-containers.md) i OMS-portalen eller Azure Portal.

När du vill installera behållarlösningen via [OMS-portalen](https://mms.microsoft.com) öppnar du **lösningsgalleriet**. Lägg sedan till **behållarlösning**. Du kan också lägga till behållarlösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

Sök efter en **Behållare**-sammanfattningspanel på instrumentpanelen i OMS-portalen. Klicka på panelen. Nu visas information om behållarhändelser, fel, status, avbildningslager och processor- och minnesanvändning. Om du vill ha mer detaljerad information klickar du på en rad på valfri panel eller utför en [loggsökning](../../log-analytics/log-analytics-log-searches.md).

![Behållarinstrumentpanelen i OMS-portalen](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

I Azure-portalen navigerar du på samma sätt till **Log Analytics** och väljer namnet på arbetsytan. När du vill visa sammanfattningspanelen **Behållare** klickar du på **Lösningar** > **Behållare**. Klicka på panelen om du vill se mer information.

I [Azure Log Analytics-dokumentationen](../../log-analytics/index.yml) finns det detaljerad information om att fråga och analysera övervakningsdata.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du övervakat ditt Kubernetes-kluster med Log Analytics. Här är några av uppgifterna:

> [!div class="checklist"]
> * Hämta inställningar för Log Analytics-arbetsytan
> * Ställa in OMS-agenterna på Kubernetes-noderna
> * Åtkomst till övervakningsinformation i OMS- eller Azure-portalen


Följ den här länken om du vill se inbyggda skriptexempel för Container Service.

> [!div class="nextstepaction"]
> [Skriptexempel för Azure Container Service](cli-samples.md)
