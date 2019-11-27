---
title: Kubernetes-versioner som stöds i Azure Kubernetes-tjänsten
description: Förstå Kubernetes-versionens support policy och livs cykel för kluster i Azure Kubernetes service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: b6dd91dda559f778eaa8f5a17b46a22020dd8373
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484052"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes service (AKS)

Kubernetes-communityn släpper nya lägre versioner ungefär var tredje månad. Dessa versioner inkluderar nya funktioner och förbättringar. Korrigeringsversioner släpps oftare (ibland varje vecka) och avser endast korrigeringar av allvarliga buggar i en lägre version. Dessa korrigerings versioner innehåller korrigeringar för säkerhets sårbarheter eller större buggar som påverkar ett stort antal kunder och produkter som körs i produktion baserat på Kubernetes.

AKS syftar till att certifiera och lansera nya Kubernetes-versioner inom 30 dagar från en överordnad version, beroende på stabiliteten för versionen.

## <a name="kubernetes-versions"></a>Kubernetes-versioner

Kubernetes använder standard versions schema för [semantisk versions hantering](https://semver.org/) . Det innebär att varje version av Kubernetes följer det här numreringsschema:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Varje tal i versionen indikerar allmän kompatibilitet med den tidigare versionen:

* Huvud versioner ändras när inkompatibla API-ändringar eller bakåtkompatibilitet kan vara brutna.
* Lägre versioner ändras när funktions förändringar görs som är bakåtkompatibla med andra mindre versioner.
* Korrigerings versioner ändras när bakåtkompatibla fel korrigeringar görs.

I allmänhet bör användare Endeavor att köra den senaste korrigerings versionen av den lägre versionen som de kör, till exempel om ditt produktions kluster finns på *1.12.14* och *1.12.15* är den senaste tillgängliga korrigerings versionen som är tillgänglig för *1,12* -serien, bör du uppgradera till *1.12.15* så snart du kan se till att klustret är fullständigt korrigerat och stöds.

## <a name="kubernetes-version-support-policy"></a>Support princip för Kubernetes-version

> [!NOTE]
> Från den nionde december 2019 AKS flyttas till stöd för de senaste (N)-2 versionerna av Kubernetes. Den här ändringen är att följa överordnat fönster för stöd för Kubernetes-versioner och se till att de senaste och säkraste versionerna används. Läs mer i [meddelandet här](https://azure.microsoft.com/updates/azure-kubernetes-service-will-be-retiring-support-for-kubernetes-versions-1-11-and-1-12/).

AKS har stöd för fyra lägre Kubernetes-versioner:

* Den aktuella del versionen som släpps i AKS (N)
* Tre tidigare lägre versioner. Varje lägre version som stöds har även stöd för två stabila korrigeringsversioner.

Detta kallas "N-3"-(N (senaste versionen)-3 (lägre versioner)).

Om AKS till exempel introducerar *1.13. a* idag tillhandahålls support för följande versioner:

Ny del version    |    Versions lista som stöds
-----------------    |    ----------------------
1.13. a               |    1.12. a, 1.12. b, 1.11. a, 1.11. b, 1.10. a, 1.10. b

Där ". a" och ". b" är representativa korrigerings versioner. " en "från 1.13. a kan skilja sig från 1.12. a. Till exempel 1.13.9 och 1.12.8.

Information om kommunikation om versions ändringar och förväntningar finns i "kommunikation" nedan.

När en ny del version introduceras, är den äldsta lägre versionen och de nya korrigerings versionerna som stöds föråldrade och tas bort. Till exempel om den aktuella versions listan som stöds är:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

Och AKS släpper 1,13. *innebär detta att 1,9.* versioner (alla 1,9-versioner) tas bort och stöds inte.

> [!NOTE]
> Observera att om kunderna kör en Kubernetes-version som inte stöds uppmanas de att uppgradera när de begär support för klustret. Kluster som kör Kubernetes-versioner som inte stöds omfattas inte av [support principerna för AKS](https://docs.microsoft.com/azure/aks/support-policies).


Förutom ovanstående i del versioner stöder AKS de två senaste *korrigeringarna**-versionerna av en specifik del version. Till exempel visas följande versioner som stöds:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Om överordnade Kubernetes publicerade 1.12.3 och 1.11.6 och AKS släpper dessa korrigerings versioner, är de äldsta korrigerings versionerna föråldrade och tas bort och den versions lista som stöds blir:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Kunder bör inte fästa kluster skapande, CI eller andra automatiserade jobb till vissa korrigerings versioner. 

### <a name="communications"></a>Kommunikationer

* För nya **mindre** versioner av Kubernetes
  * Alla användare meddelas offentligt om den nya versionen och vilken version som ska tas bort.
  * När en ny uppdaterings version släpps tas den äldsta korrigerings versionen bort samtidigt.
  * Kunder har **60 dagar** från det offentliga meddelande datumet för att uppgradera till en del versions version som stöds.
* För nya **korrigerings** versioner av Kubernetes
  * Alla användare meddelas om den nya korrigerings versionen som släpps och uppgraderas till den senaste korrigerings versionen.
  * Användare har **30 dagar** på sig att uppgradera till en nyare uppdaterings version som stöds. Användare har **30 dagar** på sig att uppgradera till en korrigerings version som stöds innan den äldsta tas bort.

AKS definierar "frigjord" som allmän tillgänglighet, aktiverat i alla service nivå mål/kvalitet för tjänste mått och är tillgängliga i alla regioner.

> [!NOTE]
> Kunder meddelas om Kubernetes versions versioner och utgångs datum, när en lägre version är föråldrad/borttagna användare får 60 dagar för att uppgradera till en version som stöds. När det gäller korrigerings versioner får kunderna 30 dagar på sig att uppgradera till en version som stöds.

#### <a name="notification-channels-for-aks-changes"></a>Aviserings kanaler för AKS-ändringar

AKS släpper en veckovis tjänst uppdatering som sammanfattar nya Kubernetes-versioner, tjänst ändringar och komponent uppdateringar som har släppts i tjänsten på [GitHub](https://github.com/Azure/AKS/releases).

Dessa ändringar överförs till alla kunder som en del av det vanliga underhåll som erbjuds som en del av den hanterade tjänsten, men vissa kräver explicita uppgraderingar medan andra inte kräver någon åtgärd.

Meddelanden skickas också via:

* [Viktig information om AKS](https://aka.ms/aks/releasenotes)
* Meddelanden i Azure Portal
* [Azure-uppdaterings kanal][azure-update-channel]

### <a name="policy-exceptions"></a>Princip undantag

AKS förbehåller sig rätten att lägga till eller ta bort nya/befintliga versioner som har identifierats ha en eller flera kritiska produktioner som påverkar buggar eller säkerhets problem utan föregående meddelande.

Vissa korrigerings versioner kan hoppas över eller att distributionen accelereras beroende på fel-eller säkerhets problemets allvarlighets grad.

### <a name="azure-portal-and-cli-default-versions"></a>Standard versioner av Azure Portal och CLI

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI är klustret alltid inställt på den lägre versionen av N-1 och den senaste korrigeringen. Om AKS till exempel stöder *1.13. a*, *1.12. a* + *1.12. b*, *1.11. a* + *1.11. b*, *1.10. a* + *1.10 b*är standard versionen för nya kluster *1.12. b*.

AKS är standardvärdet N-1 (minor. latestPatch, tex 1.12. b) för att ge kunderna en känd, stabil och korrigerad version som standard.

## <a name="list-currently-supported-versions"></a>Lista över versioner som stöds för närvarande

Om du vill ta reda på vilka versioner som för närvarande är tillgängliga för din prenumeration och region använder du kommandot [AZ AKS get-versions][az-aks-get-versions] . I följande exempel visas tillgängliga Kubernetes-versioner för regionen *östra* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Utdata liknar följande exempel, som visar att Kubernetes version *1.14.6* är den senaste tillgängliga versionen:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.6               None available
1.14.5               1.14.6
1.13.10              1.14.5, 1.14.6
1.13.9               1.13.10, 1.14.5, 1.14.6
1.12.8               1.13.9, 1.13.10
1.12.7               1.12.8, 1.13.9, 1.13.10
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad händer när en kund uppgraderar ett Kubernetes-kluster med en lägre version som inte stöds?**

Om du använder *n-4-* versionen är du utanför supporten och kommer att bli ombedd att uppgradera. Om uppgraderingen från version n-4 till n-3 lyckas är du nu i våra support principer. Till exempel:

- Om de AKS-versioner som stöds är *1.13. a*, *1.12. b* + *1.12. c*, *1.11. d* + *1.11. e*, och *1.10. f* + *1.10. g* och du är på *1,9. h* eller *1,9. i*är du utanför supporten.
- Om uppgraderingen från *1,9. h* eller *1,9. i* till *1.10. f* eller *1.10. g* lyckas, är du tillbaka i våra support principer.

Uppgraderingar till versioner som är äldre än *n-4* stöds inte. I sådana fall rekommenderar vi att kunderna skapar nya AKS-kluster och distribuerar om sina arbets belastningar.

**Vad betyder "out of support"**

Utanför support innebär att den version som du kör ligger utanför listan över versioner som stöds och du uppmanas att uppgradera klustret till en version som stöds när du begär support. Dessutom gör AKS inte någon körning eller andra garantier för kluster utanför listan över versioner som stöds.

**Vad händer när en kund skalar ett Kubernetes-kluster med en lägre version som inte stöds?**

För mindre versioner som inte stöds av AKS fortsätter skalningen av eller ut att fungera utan problem.

**Kan en kund stanna kvar på en Kubernetes-version för alltid?**

Ja. Men om klustret inte finns i en av de versioner som stöds av AKS, ligger klustret utanför AKS-support-principerna. Azure uppgraderar inte automatiskt klustret eller så tas det bort.

**Vilken version stöder huvud supporten om agent klustret inte finns i någon av de AKS-versioner som stöds?**

Originalet uppdateras automatiskt till den senaste versionen som stöds.

## <a name="next-steps"></a>Nästa steg

Information om hur du uppgraderar klustret finns i [uppgradera ett Azure Kubernetes service-kluster (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
