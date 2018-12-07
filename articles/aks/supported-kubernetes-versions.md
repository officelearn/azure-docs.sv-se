---
title: Kubernetes-versioner som stöds i Azure Kubernetes Service
description: Förstå Supportpolicy för Kubernetes-version och livscykeln för kluster i Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 09/21/2018
ms.author: saudas
ms.openlocfilehash: e535d1c2c581e20667a1ccf10fc97fcf988bc91a
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001810"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes Service (AKS)

Kubernetes-communityn släpper nya lägre versioner ungefär var tredje månad. Dessa versioner inkluderar nya funktioner och förbättringar. Korrigeringsversioner släpps oftare (ibland varje vecka) och avser endast korrigeringar av allvarliga buggar i en lägre version. Dessa patch-versioner innehåller korrigeringar av säkerhetsrisker eller större buggar som påverkar ett stort antal kunder och produkter som körs i produktionsmiljön baserat på Kubernetes.

En ny Kubernetes minor-version är tillgänglig i [aks-engine] [ aks-engine] från dag ett. AKS-servicenivåmål släpper den lägre versionen för AKS-kluster inom 30 dagar beroende på versionens stabilitet.

## <a name="kubernetes-version-support-policy"></a>Stödprincip för Kubernetes-version

AKS har stöd för fyra lägre Kubernetes-versioner:

- Den aktuella delversionen som är publicerad överordnade (n)
- Tre tidigare lägre versioner. Varje lägre version som stöds har även stöd för två stabila korrigeringsversioner.

Exempel: om AKS introducerar *1.11.x* idag finns också stöd för *1.10.a* + *1.10.b*, *1.9.c*  +  *1,9 d*, *1.8.e* + *1.8F* (där bokstäver patch-versioner är två senaste stabil versioner).

När en ny lägre version släpps tas den äldsta lägre versionen och dess korrigeringsversioner ur bruk. 15 dagar före lanseringen av den nya minor-version och kommande versionen dras tillbaka ett meddelande görs via den [Azure uppdateringskanaler][azure-update-channel]. I exemplet ovan var *1.11.x* har lanserats utgångna versioner är *1.7.g* + *1.7.h*.

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI konfigureras klustret alltid till den lägre versionen n-1 och den senaste korrigeringsversionen. Exempel: om AKS stöder *1.11.x*, *1.10.a* + *1.10.b*, *1.9.c* + *1,9 d* , *1.8.e* + *1.8F*, standardversionen för nya kluster är *1.10.b*.

## <a name="list-currently-supported-versions"></a>Lista över versioner som stöds för närvarande

Om du vill ta reda på vilka versioner som är tillgängliga för din prenumeration och region kan du använda den [az aks get-versioner] [ az-aks-get-versions] kommando. I följande exempel visar en lista över tillgängliga Kubernetes-versioner för den *EastUS* region:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Utdata liknar följande exempel som visar att Kubernetes-version *1.11.5* finns den senaste versionen:

```
KubernetesVersion    Upgrades
-------------------  ----------------------
1.11.5               None available
1.11.4               1.11.5
1.10.9               1.11.4, 1.11.5
1.10.8               1.10.9, 1.11.4, 1.11.5
1.9.11               1.10.8, 1.10.9
1.9.10               1.9.11, 1.10.8, 1.10.9
1.8.15               1.9.10, 1.9.11
1.8.14               1.8.15, 1.9.10, 1.9.11
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad händer när en kund uppgraderas ett Kubernetes-kluster med en mindre version som inte stöds?**

Om du använder den *n-4* version kan du ligger utanför Servicenivåmål. Om din uppgradering från version n-4 till n-3 lyckas, och sedan är du tillbaka i på SLO. Exempel:

- Om AKS-versioner som stöds är *1.10.a* + *1.10.b*, *1.9.c* + *1,9 d*,  *1.8.e* + *1.8F* och du har en *1.7.g* eller *1.7.h*, är slut på SLO.
- Om uppgraderingen från *1.7.g* eller *1.7.h* till *1.8.e* eller *1.8.f* lyckas, är du tillbaka i på SLO.

Uppgraderingar till versioner som är äldre än *n-4* stöds inte. I sådana fall rekommenderar vi kunder skapar nya AKS-kluster och distribuera om sina arbetsbelastningar.

**Vad händer när en kund kan skalas upp ett Kubernetes-kluster med en mindre version som inte stöds?**

För mindre versioner som inte stöds av AKS kan fortsätter skala in eller ut att fungera utan problem.

**Kan en kund stanna kvar på ett Kubernetes-version permanent?**

Ja. Om klustret inte finns på en av de versioner som stöds av AKS är dock klustret utanför på AKS SLO. Azure inte automatiskt uppgradera klustret eller ta bort den.

**Vilken version har stöd för master om agenten klustret inte är i ett AKS-versioner som stöds?**

Huvudservern uppdateras automatiskt till den senaste versionen.

## <a name="next-steps"></a>Nästa steg

Information om hur du uppgraderar klustret finns i [uppgradera ett kluster i Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
