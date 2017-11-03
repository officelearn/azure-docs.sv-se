---
title: "Självstudiekurs för Azure Container Service - övervakaren Kubernetes | Microsoft Docs"
description: "Självstudiekurs för Azure Container Service - övervakaren Kubernetes med Microsoft Operations Management Suite (OMS)"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker behållare Micro-tjänster, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f72944fe819a79edbafb73fba635d73642f33e4f
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Övervaka ett Kubernetes kluster med Operations Management Suite

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Övervaka dina Kubernetes klustret och behållare är viktigt, särskilt när du hanterar ett produktionskluster i skala och med flera appar. 

Du kan dra nytta av flera Kubernetes övervakningslösningar, antingen från Microsoft eller andra leverantörer. I kursen får du övervaka Kubernetes klustret med hjälp av behållare lösningen i [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md), Microsofts molnbaserade IT hanteringslösning. (OMS behållare lösningen är i förhandsvisning.)

Den här självstudien tillhör sju sju, omfattar följande aktiviteter:

> [!div class="checklist"]
> * Hämta OMS-arbetsytan inställningar
> * Ställ in OMS-agenterna på noderna Kubernetes
> * Åtkomst till övervakningsinformation i OMS-portalen eller Azure-portalen

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudiekurser ett program som har paketerats i behållaren bilder, dessa bilder som har överförts till registret för Azure-behållaren och ett Kubernetes kluster skapas. 

Om du inte har gjort dessa steg och vill följa med, gå tillbaka till [kursen 1 – skapa behållaren bilder](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Hämta arbetsytan inställningar

När du har åtkomst till den [OMS-portalen](https://mms.microsoft.com), gå till **inställningar** > **anslutna källor** > **Linux-servrar**. Där hittar du den *arbetsyte-ID* och en primär eller sekundär *Arbetsytenyckel*. Anteckna dessa värden som du måste ställa in OMS-agenterna på klustret.

## <a name="set-up-oms-agents"></a>Ställ in OMS-Agent

Här är en YAML-fil för att ställa in OMS-agenterna på noderna i Linux. Den skapar en Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), som körs en enda identiska baljor på varje nod i klustret. Resursen DaemonSet är idealiskt för distribution av en övervakningsagent. 

Spara följande text i en fil med namnet `oms-daemonset.yaml`, och ersätter platshållarvärdena för *myWorkspaceID* och *myWorkspaceKey* med din OMS arbetsyte-ID och nyckel. (För i produktion, kan du koda värdena som hemligheter.)

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

Skapa DaemonSet med följande kommando:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

För att se att DaemonSet har skapats, kör du:

```azurecli-interactive
kubectl get daemonset
```

De utdata som genereras liknar följande:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

När agenterna körs tar flera minuter för OMS att mata in och bearbeta data.

## <a name="access-monitoring-data"></a>Åtkomst till övervakningsdata

Visa och analysera OMS-behållaren övervakningsdata med den [behållare lösning](../../log-analytics/log-analytics-containers.md) i OMS-portalen eller Azure-portalen. 

Att installera lösningen behållaren med den [OMS-portalen](https://mms.microsoft.com), gå till **lösningar galleriet**. Lägg sedan till **behållare lösning**. Du kan också lägga till behållare lösningar från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

I OMS-portalen, söka efter en **behållare** panelen Sammanfattning på OMS-instrumentpanelen. Klicka på panelen för information, inklusive: behållaren händelser, fel, status, bild inventering och processor- och minnesanvändning. Mer detaljerad information finns på en rad på panelen eller utföra en [loggen Sök](../../log-analytics/log-analytics-log-searches.md).

![Instrumentpanel för behållare i OMS-portalen](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

På samma sätt i Azure-portalen går du till **logganalys** och välj namnet på din arbetsyta. Se den **behållare** panelen Sammanfattning, klickar du på **lösningar** > **behållare**. Klicka på panelen om du vill se information.

Finns det [Azure logganalys dokumentationen](../../log-analytics/index.yml) för detaljerad information om frågor och analys av övervakningsdata.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen övervakade Kubernetes klustret med OMS. Uppgifter som omfattas ingår:

> [!div class="checklist"]
> * Hämta OMS-arbetsytan inställningar
> * Ställ in OMS-agenterna på noderna Kubernetes
> * Åtkomst till övervakningsinformation i OMS-portalen eller Azure-portalen


Den här länken för att se förskapad skriptexempel Container Service.

> [!div class="nextstepaction"]
> [Azure Container Service-skriptexempel](cli-samples.md)
