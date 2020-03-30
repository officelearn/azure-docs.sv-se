---
title: Kubernetes-versioner som stöds i Azure Kubernetes Service
description: Förstå Kubernetes versionssupportprincip och livscykel för kluster i Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593452"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes Service (AKS)

Kubernetes-communityn släpper mindre versioner ungefär var tredje månad. Dessa versioner innehåller nya funktioner och förbättringar. Patch utgåvor är vanligare (ibland varje vecka) och är endast avsedda för kritiska buggfixar i en mindre version. Dessa korrigeringsversioner innehåller korrigeringar för säkerhetsproblem eller större fel som påverkar ett stort antal kunder och produkter som körs i produktion baserat på Kubernetes.

AKS syftar till att certifiera och släppa nya Kubernetes versioner inom 30 dagar efter en uppströms release, med förbehåll för stabiliteten i utgåvan.

## <a name="kubernetes-versions"></a>Kubernetes versioner

Kubernetes använder [standardversionsschemat för semantisk versionshantering.](https://semver.org/) Detta innebär att varje version av Kubernetes följer detta numreringsschema:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Varje nummer i versionen anger allmän kompatibilitet med den tidigare versionen:

* Huvudversioner ändras när inkompatibla API-ändringar eller bakåtkompatibilitet kan brytas.
* Delversioner ändras när funktionsändringar görs som är bakåtkompatibla med de andra mindre versionerna.
* Patchversioner ändras när bakåtkompatibla buggfixar görs.

Användare bör sträva efter att köra den senaste korrigeringsversionen av den delversion de kör, till exempel om produktionsklustret är på *1.12.14* och *1.12.15* är den senaste tillgängliga patchversionen som är tillgänglig för *1.12-serien,* bör du uppgradera till *1.12.15* så snart du kan se till att klustret är helt korrigerat och stöds.

## <a name="kubernetes-version-support-policy"></a>Stödprincip för kubernetesversion

AKS stöder tre delversioner av Kubernetes:

* Den aktuella delversionen som släpps i AKS (N)
* Två tidigare delversioner. Varje delversion som stöds stöder också två stabila korrigeringar.

Detta kallas "N-2": (N (Senaste utgåvan) - 2 (mindre versioner)).

Om AKS till exempel introducerar *1.15.a* idag finns stöd för följande versioner:

Ny delversion    |    Versionslista som stöds
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

Där ".letter" är representativt för patchversioner.

För mer information om kommunikation om versionsändringar och förväntningar, se "Kommunikation" nedan.

När en ny delversion introduceras är de äldsta delversions- och korrigeringsversionerna som stöds inaktuella och borttagna. Om den aktuella versionslistan stöds är till exempel:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

Och AKS släpper 1,16. *betyder det att 1,13.* versioner (alla 1,13 versioner) kommer att tas bort och har på stöd.

> [!NOTE]
> Observera att om kunder kör en kubernetes-version som inte stöds uppmanas de att uppgradera när de begär stöd för klustret. Kluster som kör Kubernetes-versioner som inte stöds omfattas inte av [AKS-supportprinciperna](https://docs.microsoft.com/azure/aks/support-policies).

Utöver ovanstående på delversioner stöder AKS de två senaste **patchversionerna** av en viss delversion. Med tanke på följande versioner som stöds:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Om uppströms Kubernetes släppt 1.15.3 och 1.14.6 och AKS släpper dessa patchversioner, är de äldsta patchversionerna inaktuella och borttagna och den versionslista som stöds blir:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Kommunikation

* För nya **delversioner** av Kubernetes
  * Alla användare meddelas offentligt om den nya versionen och vilken version som ska tas bort.
  * När en ny patchversion släpps tas den äldsta korrigeringsversionen bort samtidigt.
  * Kunder har **30 dagar** på sig från det offentliga meddelandedatumet att uppgradera till en delversion som stöds.
* För nya **patchversioner** av Kubernetes
  * Alla användare meddelas om den nya patchversionen som släpps och att uppgradera till den senaste patchversionen.
  * Användare har **30 dagar** på sig att uppgradera till en nyare, korrigeringsfrisättning som stöds innan den äldsta tas bort.

AKS definierar en "släppt version" som allmänt tillgängliga versioner, aktiverad i alla SLO / Quality of Service mätningar och finns i alla regioner. AKS kan också stödja förhandsgranskningsversioner som uttryckligen är märkta och omfattas av förhandsvillkor.

#### <a name="notification-channels-for-aks-changes"></a>Meddelandekanaler för AKS-ändringar

AKS publicerar regelbundna serviceuppdateringar som sammanfattar nya Kubernetes-versioner, tjänständringar och komponentuppdateringar som har släppts på tjänsten på [GitHub](https://github.com/Azure/AKS/releases).

Dessa ändringar rullas till alla kunder som en del av regelbundet underhåll som erbjuds som en del av den hanterade tjänsten, vissa kräver explicita uppgraderingar medan andra kräver ingen åtgärd.

Meddelanden skickas också via:

* [AKS-viktig information](https://aka.ms/aks/releasenotes)
* Meddelanden i Azure Portal
* [Azure-uppdateringskanal][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Undantag för versionsprinciper som stöds

AKS förbehåller sig rätten att lägga till eller ta bort nya/befintliga versioner som har identifierats för att ha en eller flera kritiska produktionseffekter buggar eller säkerhetsproblem utan förvarning.

Specifika korrigeringsversioner kan hoppas över eller distribueras som påskyndas beroende på hur allvarlig felet eller säkerhetsproblemet är.

### <a name="azure-portal-and-cli-default-versions"></a>Standardversioner av Azure Portal och CLI

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI, är klustret standard till N-1-delversionen och den senaste korrigeringsfilen. Om AKS till exempel stöder *1.15.a*, *1.15.b*, *1.14.c,* *1.14.d,* *1.13.e*och *1.13.f*är den valda standardversionen *1.14.c*.

AKS väljer standard för N-1 för att ge kunderna en känd, stabil och korrigerad version som standard.

## <a name="list-currently-supported-versions"></a>Lista versioner som stöds för närvarande

Om du vill ta reda på vilka versioner som för närvarande är tillgängliga för din prenumeration och region använder du kommandot [az aks get-versions.][az-aks-get-versions] I följande exempel visas tillgängliga Kubernetes-versioner för *Regionen EastUS:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad händer när en kund uppgraderar ett Kubernetes-kluster med en delversion som inte stöds?**

Om du är på *n-3-versionen,* är du utanför support och kommer att bli ombedd att uppgradera. Om din uppgradering från version n-3 till n-2 lyckas är du nu i våra supportpolicyer. Ett exempel:

- Om den äldsta AKS-versionen som stöds är *1.13.a* och du är på *1.12.b* eller äldre, är du utanför supporten.
- Om uppgraderingen från *1.12.b* till *1.13.a* eller högre lyckas, är du tillbaka inom våra supportpolicyer.

Upgrades to versions older than the supported window of *N-2* are not supported. I sådana fall rekommenderar vi att kunder skapar nya AKS-kluster och distribuerar om sina arbetsbelastningar med versioner i fönstret som stöds.

**Vad betyder "Utanför supporten"**

"Utanför supporten" innebär att den version du kör ligger utanför listan över versioner som stöds, och du kommer att bli ombedd att uppgradera klustret till en version som stöds när du begär support. Dessutom ger AKS inga körnings- eller andra garantier för kluster utanför listan över versioner som stöds.

**Vad händer när en kund skalar ett Kubernetes-kluster med en delversion som inte stöds?**

För mindre versioner som inte stöds av AKS bör skalning in eller ut fortsätta att fungera, men det rekommenderas starkt att uppgradera för att få tillbaka klustret till stöd.

**Kan en kund stanna på en Kubernetes version för evigt?**

Ja. Men om klustret inte finns på någon av de versioner som stöds av AKS, är klustret utanför AKS-stödprinciperna. Azure uppgraderar inte automatiskt klustret eller tar bort det.

**Vilken version stöder kontrollplanet om nodpoolen inte finns i någon av AKS-versionerna som stöds?**

Kontrollplanet måste finnas i ett fönster med versioner från alla nodpooler. Mer information om hur du uppgraderar kontrollplans- eller nodpoolerna finns i dokumentation om [uppgradering av nodpooler](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Nästa steg

Information om hur du uppgraderar klustret finns i [Uppgradera ett AKS-kluster (Azure Kubernetes Service).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
