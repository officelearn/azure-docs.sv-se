---
title: Lägga till hälso avsökningar i din AKS-poddar
description: Den här artikeln innehåller information om hur du lägger till hälso avsökningar (beredskap och/eller Lives) för att AKS poddar med en Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 073babf407509c9bbf05340edd828c895fa376e9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513632"
---
# <a name="add-health-probes-to-your-service"></a>Lägg till hälso avsökningar i din tjänst
Som standard etablerar ingress Controller en HTTP GET-avsökning för exponerade poddar.
Du kan anpassa egenskaper för avsökning genom att lägga till en [beredskaps-eller Direktmigrering](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) till din `deployment`/`pod` specifikation.

## <a name="with-readinessprobe-or-livenessprobe"></a>Med `readinessProbe` eller `livenessProbe`
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API-referens:
* [Container PROBE](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet-åtgärd](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` och `livenessProbe` stöds när de konfigureras med `httpGet`.
> * Det finns för närvarande inte stöd för avsökning på en annan port än den som exponeras på pod.
> * `HttpHeaders``InitialDelaySeconds``SuccessThreshold` inte stöds.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Utan `readinessProbe` eller `livenessProbe`
Om avsökningarna ovan inte anges gör ingångs styrenheten ett antagande att tjänsten kan uppnås på `Path` som anges för `backend-path-prefix` anteckning eller `path` som anges i `ingress` definitionen för tjänsten.

## <a name="default-values-for-health-probe"></a>Standardvärden för hälso avsökning
Standardvärden anges för en egenskap som inte kan härledas av beredskap/direktmigreringen.

| Application Gateway avsöknings egenskap | Standardvärde |
|-|-|
| `Path` | / |
| `Host` | värd |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |