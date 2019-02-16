---
title: Kubernetes-versioner som stöds i Azure Kubernetes Service
description: Förstå Supportpolicy för Kubernetes-version och livscykeln för kluster i Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 02/15/2018
ms.author: saudas
ms.openlocfilehash: 37a9712749a1575f81086d28ad461a665bef36d9
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313457"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes Service (AKS)

Kubernetes-communityn släpper nya lägre versioner ungefär var tredje månad. Dessa versioner inkluderar nya funktioner och förbättringar. Korrigeringsversioner släpps oftare (ibland varje vecka) och avser endast korrigeringar av allvarliga buggar i en lägre version. Dessa patch-versioner innehåller korrigeringar av säkerhetsrisker eller större buggar som påverkar ett stort antal kunder och produkter som körs i produktionsmiljön baserat på Kubernetes.

En ny Kubernetes minor-version är tillgänglig i [aks-engine] [ aks-engine] från dag ett. AKS-servicenivåmål släpper den lägre versionen för AKS-kluster inom 30 dagar beroende på versionens stabilitet.

## <a name="kubernetes-version-support-policy"></a>Stödprincip för Kubernetes-version

AKS har stöd för fyra lägre Kubernetes-versioner:

- Den aktuella delversionen som är publicerad överordnade (n)
- Tre tidigare lägre versioner. Varje lägre version som stöds har även stöd för två stabila korrigeringsversioner.

Exempel: om AKS introducerar *1.12.x* idag finns också stöd för *1.11.a* + *1.11.b*, *1.10.c*  +  *1.10d*, *1.9.e* + *1.9f* (där bokstäver patch-versioner är två senaste stabil versioner).

När en ny lägre version släpps tas den äldsta lägre versionen och dess korrigeringsversioner ur bruk. 15 dagar före lanseringen av den nya minor-version och kommande versionen dras tillbaka ett meddelande görs via den [Azure uppdateringskanaler][azure-update-channel]. I exemplet ovan var *1.12.x* har lanserats utgångna versioner är *1.8.g* + *1.8.h*.

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI konfigureras klustret alltid till den lägre versionen n-1 och den senaste korrigeringsversionen. Exempel: om AKS stöder *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +   *1.10d*, *1.9.e* + *1.9f*, standardversionen för nya kluster är *1.10.b*.

## <a name="list-currently-supported-versions"></a>Lista över versioner som stöds för närvarande

Om du vill ta reda på vilka versioner som är tillgängliga för din prenumeration och region kan du använda den [az aks get-versioner] [ az-aks-get-versions] kommando. I följande exempel visar en lista över tillgängliga Kubernetes-versioner för den *EastUS* region:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Utdata liknar följande exempel som visar att Kubernetes-version *1.12.5* finns den senaste versionen:

```
KubernetesVersion    Upgrades
-------------------  -----------------------
1.12.5               None available
1.12.4               1.12.5
1.11.7               1.12.4, 1.12.5
1.11.6               1.11.7, 1.12.4, 1.12.5
1.10.12              1.11.6, 1.11.7
1.10.9               1.10.12, 1.11.6, 1.11.7
1.9.11               1.10.9, 1.10.12
1.9.10               1.9.11, 1.10.9, 1.10.12
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad händer när en kund uppgraderas ett Kubernetes-kluster med en mindre version som inte stöds?**

Om du använder den *n-4* version kan du ligger utanför Servicenivåmål. Om din uppgradering från version n-4 till n-3 lyckas, och sedan är du tillbaka i på SLO. Exempel:

- Om AKS-versioner som stöds är *1.12.x*, *1.11.a* + *1.11.b*, *1.10.c*  +  *1.10d*, och *1.9.e* + *1.9f* och du har en *1.8.g* eller *1.8.h*, är slut på SLO.
- Om uppgraderingen från *1.8.g* eller *1.8.h* till *1.9.e* eller *1.9.f* lyckas, är du tillbaka i på SLO.

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
