---
title: "Självstudie om Kubernetes i Azure – Övervaka Kubernetes"
description: "AKS-självstudie – Övervaka Kubernetes med Microsoft Operations Management Suite (OMS)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2fedd615733e3bf51469d3b69d5fe51e3e99087e
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="monitor-azure-container-service-aks"></a>Övervaka Azure Container Service (AKS)

Det är viktigt att du övervakar Kubernetes-behållarna och behållarna, särskilt när du kör ett produktionskluster i skala med flera program.

I den här självstudien konfigurerar du övervakningen av AKS-kluster med [behållarlösningen för Log Analytics][log-analytics-containers].

Den här självstudien som är del sju av åtta tar upp följande uppgifter:

> [!div class="checklist"]
> * Konfigurera behållarens övervakningslösning
> * Konfigurera övervakningsagenterna
> * Åtkomst till övervakningsinformation i Azure-portalen

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades.

Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa behållaravbildningar][aks-tutorial-prepare-app].

## <a name="configure-the-monitoring-solution"></a>Konfigurera övervakningslösningen

I Azure-portalen väljer du **Skapa en resurs** och söker efter `Container Monitoring Solution`. När du hittar den väljer du **Skapa**.

![Lägga till lösning](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Skapa en ny OMS-arbetsyta eller välj en befintlig. Formuläret OMS-arbetsyta vägleder dig genom processen.

När du skapar arbetsytan väljer du **Fäst på instrumentpanelen** för enkel hämtning.

![OMS-arbetsyta](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

När du är klar väljer du **OK**. När verifieringen är klar väljer du **Skapa** för att skapa lösningen för övervakning av behållaren.

När arbetsytan har skapats visas den för dig i Azure-portalen.

## <a name="get-workspace-settings"></a>Hämta inställningar för arbetsyta

Log Analytics-arbetsytans ID och nyckel behövs för att konfigurera lösningsagenten på Kubernetes-noderna.

Om du vill hämta värdena väljer du **OMS-arbetsyta** på behållarlösningens vänstra meny. Välj  **	Avancerade inställningar** och anteckna **ARBETSYTANS ID** och **PRIMÄRNYCKELN**.

## <a name="configure-monitoring-agents"></a>Konfigurera övervakningsagenter

Du kan använda följande Kubernetes-manifestfil för att konfigurera behållarens övervakningsagenter på ett Kubernetes-kluster. Det skapar en Kubernetes [DaemonSet][kubernetes-daemonset], som kör en enda pod på varje klusternod.

Spara följande text till en fil med namnet `oms-daemonset.yaml` och ersätt platshållarvärdena för `WSID` och `KEY` med Log Analytics arbetsyta-ID och nyckel.

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
        - mountPath: /var/log
          name: host-log
        - mountPath: /var/lib/docker/containers/
          name: container-log
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
    - name: container-hostname
      hostPath:
       path: /etc/hostname
    - name: host-log
      hostPath:
       path: /var/log
    - name: container-log
      hostPath:
       path: /var/lib/docker/containers/
```

Skapa ett DaemonSet med följande kommando:

```azurecli
kubectl create -f oms-daemonset.yaml
```

Kör följande om du vil se det som DaemonSet har skapat:

```azurecli
kubectl get daemonset
```

De utdata som genereras liknar följande:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

När agenterna har körts tar det flera minuter för OMS att mata in och bearbeta data.

## <a name="access-monitoring-data"></a>Åtkomst till övervakningsdata

I Azure-portalen väljer du Log Analytics-arbetsytan som har fästs på portalens instrumentpanel. Klicka på panelen **Lösning för övervakning av behållare**. Här hittar du information om AKS-klustret och behållarna från klustret.

![Instrumentpanel](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

I [Azure Log Analytics-dokumentationen][log-analytics-docs] finns det detaljerad information om att fråga och analysera övervakningsdata.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du övervakat ditt Kubernetes-kluster med OMS. Här är några av uppgifterna:

> [!div class="checklist"]
> * Konfigurera behållarens övervakningslösning
> * Konfigurera övervakningsagenterna
> * Åtkomst till övervakningsinformation i Azure-portalen

Gå vidare till nästa självstudie om du vill lära dig om att uppgradera Kubernetes till en ny version.

> [!div class="nextstepaction"]
> [Uppgradera Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[log-analytics-containers]: ../log-analytics/log-analytics-containers.md
[log-analytics-docs]: ../log-analytics/index.yml
