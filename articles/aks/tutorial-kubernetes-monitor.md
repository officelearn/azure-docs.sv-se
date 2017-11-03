---
title: "Kubernetes på kursen i Azure - Monitor Kubernetes"
description: "AKS kursen - övervakaren Kubernetes med Microsoft Operations Management Suite (OMS)"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker behållare Micro-tjänster, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a41f699291a65129906680cbb6719c2478c0d830
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="monitor-azure-container-service-aks"></a>Övervaka Azure Container Service (AKS)

Övervaka dina Kubernetes klustret och behållare är viktigt, särskilt när du kör ett produktionskluster i skala, med flera program.

I kursen får du konfigurera övervakning av du AKS kluster med hjälp av den [behållare lösning för Log Analytics](../log-analytics/log-analytics-containers.md).

Den här självstudien del sju åtta omfattar följande aktiviteter:

> [!div class="checklist"]
> * Konfigurera behållaren övervakningslösning
> * Konfigurera övervakning agenter
> * Åtkomst till övervakningsinformation i Azure-portalen

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudiekurser ett program som har paketerats i behållaren bilder, dessa bilder som har överförts till registret för Azure-behållaren och ett Kubernetes kluster skapas.

Om du inte har gjort dessa steg och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder](./tutorial-kubernetes-prepare-app.md).

## <a name="configure-the-monitoring-solution"></a>Konfigurera övervakning lösningen

Välj i Azure-portalen **ny** och Sök efter `Container Monitoring Solution`. När du väljer **skapa**.

![Lägg till lösning](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Skapa en ny OMS-arbetsyta eller välj en befintlig. Formuläret OMS-arbetsytan vägleder dig genom processen.

När du skapar arbetsytan väljer **fäst på instrumentpanelen** enkelt kan användas.

![OMS-arbetsyta](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

När du är klar väljer **OK**. När verifieringen är klar, Välj **skapa** att skapa behållaren övervakningslösning.

När arbetsytan har skapats kan visas den för dig i Azure-portalen.

## <a name="get-workspace-settings"></a>Hämta arbetsytan inställningar

Logganalys arbetsyte-ID och nyckel krävs för att konfigurera agenten lösning på Kubernetes-noder.

Om du vill hämta dessa värden, Välj **OMS-arbetsytan** i den vänstra menyn lösningar för behållaren. Välj **avancerade inställningar** och anteckna den **ARBETSYTE-ID** och **PRIMÄRNYCKEL**.

## <a name="configure-monitoring-agents"></a>Konfigurera övervakning agenter

Följande Kubernetes manifestfilen kan användas för att konfigurera behållaren övervakningsagenterna på ett Kubernetes kluster. Den skapar en Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), som körs en enda baljor på varje nod i klustret.

Spara följande text i en fil med namnet `oms-daemonset.yaml`, och ersätter platshållarvärdena för `WSID` och `KEY` med Log Analytics arbetsyte-ID och nyckeln.

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
```

Skapa DaemonSet med följande kommando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

För att se att DaemonSet har skapats, kör du:

```azurecli-interactive
kubectl get daemonset
```

De utdata som genereras liknar följande:

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

När agenterna körs tar flera minuter för OMS att mata in och bearbeta data.

## <a name="access-monitoring-data"></a>Åtkomst till övervakningsdata

Välj logganalys-arbetsytan har fästs på portalens instrumentpanel i Azure-portalen. Klicka på den **lösning för övervakning av behållare** panelen. Här hittar du information om AKS klustret och behållare från klustret.

![Instrumentpanel](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Finns det [Azure logganalys dokumentationen](../log-analytics/index.yml) för detaljerad information om frågor och analys av övervakningsdata.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen övervakade Kubernetes klustret med OMS. Uppgifter som omfattas ingår:

> [!div class="checklist"]
> * Konfigurera behållaren övervakningslösning
> * Konfigurera övervakning agenter
> * Åtkomst till övervakningsinformation i Azure-portalen

Gå vidare till nästa kurs att lära dig om att uppgradera Kubernetes till en ny version.

> [!div class="nextstepaction"]
> [Uppgradera Kubernetes](./tutorial-kubernetes-upgrade-cluster.md)