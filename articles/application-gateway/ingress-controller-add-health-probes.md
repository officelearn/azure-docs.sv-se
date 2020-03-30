---
title: Lägg till hälsoavsökningar i dina AKS-poddar
description: Den här artikeln innehåller information om hur du lägger till hälsoavsökningar (beredskap och/eller liveness) i AKS-poddar med en Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795598"
---
# <a name="add-health-probes-to-your-service"></a>Lägg till hälsoavsökningar i din tjänst
Som standard etablerar Ingress controller en HTTP GET-avsökning för de exponerade poddarna.
Avsökningsegenskaperna kan anpassas genom att lägga `deployment` / `pod` till en [beredskaps- eller livenessprobning](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) till din spec.

## <a name="with-readinessprobe-or-livenessprobe"></a>Med `readinessProbe` eller`livenessProbe`
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
* [Behållare Avsökningar](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet åtgärd](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe`och `livenessProbe` stöds när de `httpGet`konfigureras med .
> * Utplaning på en annan port än den som visas på podden stöds för närvarande inte.
> * `HttpHeaders`, `InitialDelaySeconds` `SuccessThreshold` stöds inte.

##  <a name="without-readinessprobe-or-livenessprobe"></a>Utan `readinessProbe` eller`livenessProbe`
Om ovanstående avsökningar inte tillhandahålls, gör Ingress Controller ett antagande `Path` om `backend-path-prefix` att tjänsten kan `path` nås på `ingress` angiven för anteckningar eller den som anges i definitionen för tjänsten.

## <a name="default-values-for-health-probe"></a>Standardvärden för hälsoavsökning
För alla egenskaper som inte kan härledas av beredskaps-/liveness-avsökningen anges standardvärden.

| Egenskap för programgatewayavsökning | Standardvärde |
|-|-|
| `Path` | / |
| `Host` | localhost |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |