---
title: Kubernetes-versioner som stöds i Azure Kubernetes-tjänsten
description: Förstå Kubernetes-versionens support policy och livs cykel för kluster i Azure Kubernetes service (AKS)
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593452"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes service (AKS)

Kubernetes community släpper lägre versioner ungefär var tredje månad. Dessa versioner innehåller nya funktioner och förbättringar. Uppdaterings versioner är oftare (ibland veckovis) och är endast avsedda för viktiga fel korrigeringar i en lägre version. Dessa korrigerings versioner innehåller korrigeringar för säkerhets sårbarheter eller större buggar som påverkar ett stort antal kunder och produkter som körs i produktion baserat på Kubernetes.

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

Användarna bör sträva efter att köra den senaste korrigerings versionen av den lägre versionen som de kör, till exempel om ditt produktions kluster finns på *1.12.14* och *1.12.15* är den senaste tillgängliga korrigerings versionen som är tillgänglig för *1,12* -serien, bör du uppgradera till *1.12.15* så snart du kan se till att klustret är fullständigt korrigerat och stöds.

## <a name="kubernetes-version-support-policy"></a>Support princip för Kubernetes-version

AKS stöder tre lägre versioner av Kubernetes:

* Den aktuella del versionen som släpps i AKS (N)
* Två tidigare del versioner. Alla lägre versioner som stöds har också stöd för två stabila korrigeringar.

Detta kallas "N-2": (N (senaste versionen)-2 (lägre versioner)).

Om AKS till exempel introducerar *1.15. a* idag tillhandahålls support för följande versioner:

Ny del version    |    Versions lista som stöds
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14. c, 1.14. d, 1.13. e, 1.13. f

Där ". us" är representativ för korrigerings versioner.

Information om kommunikation om versions ändringar och förväntningar finns i "kommunikation" nedan.

När en ny del version introduceras, är den äldsta lägre versionen och de nya korrigerings versionerna som stöds föråldrade och tas bort. Till exempel, om den aktuella versions listan som stöds är:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

Och AKS släpper 1,16. *innebär detta att 1,13.* versioner (alla 1,13-versioner) tas bort och stöds inte.

> [!NOTE]
> Observera att om kunderna kör en Kubernetes-version som inte stöds uppmanas de att uppgradera när de begär support för klustret. Kluster som kör Kubernetes-versioner som inte stöds omfattas inte av [support principerna för AKS](https://docs.microsoft.com/azure/aks/support-policies).

Förutom ovanstående i del versioner stöder AKS de två senaste **korrigerings** versionerna av en specifik del version. Till exempel visas följande versioner som stöds:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Om överordnade Kubernetes publicerade 1.15.3 och 1.14.6 och AKS släpper dessa korrigerings versioner, är de äldsta korrigerings versionerna föråldrade och tas bort och den versions lista som stöds blir:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Kommunikationstjänsten

* För nya **mindre** versioner av Kubernetes
  * Alla användare meddelas offentligt om den nya versionen och vilken version som ska tas bort.
  * När en ny uppdaterings version släpps tas den äldsta korrigerings versionen bort samtidigt.
  * Kunder har **30 dagar** från det offentliga meddelande datumet för att uppgradera till en lägre versions version som stöds.
* För nya **korrigerings** versioner av Kubernetes
  * Alla användare meddelas om den nya korrigerings versionen som släpps och uppgraderas till den senaste korrigerings versionen.
  * Användare har **30 dagar** på sig att uppgradera till en nyare, korrigerings version som stöds innan den äldsta tas bort.

AKS definierar en "utgiven version" som de allmänt tillgängliga versionerna, aktiverade i alla service nivå mål/kvalitet för tjänste mått och är tillgängliga i alla regioner. AKS kan också ha stöd för för hands versioner som uttryckligen märks och omfattas av för hands versions villkor.

#### <a name="notification-channels-for-aks-changes"></a>Aviserings kanaler för AKS-ändringar

AKS publicerar regelbundna tjänst uppdateringar som sammanfattar nya Kubernetes-versioner, tjänst ändringar och komponent uppdateringar som har släppts i tjänsten på [GitHub](https://github.com/Azure/AKS/releases).

Dessa ändringar överförs till alla kunder som en del av det vanliga underhåll som erbjuds som en del av den hanterade tjänsten, men vissa kräver explicita uppgraderingar medan andra inte kräver någon åtgärd.

Meddelanden skickas också via:

* [Viktig information om AKS](https://aka.ms/aks/releasenotes)
* Meddelanden i Azure Portal
* [Azure-uppdaterings kanal][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Princip undantag för versioner som stöds

AKS förbehåller sig rätten att lägga till eller ta bort nya/befintliga versioner som har identifierats ha en eller flera kritiska produktioner som påverkar buggar eller säkerhets problem utan föregående meddelande.

Vissa korrigerings versioner kan hoppas över eller att distributionen accelereras beroende på fel-eller säkerhets problemets allvarlighets grad.

### <a name="azure-portal-and-cli-default-versions"></a>Standard versioner av Azure Portal och CLI

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI, är klustret standardvärdet för den lägre versionen av N-1 och den senaste korrigeringen. Om AKS till exempel stöder *1.15. a*, *1.15. b*, *1.14. c*, *1.14. d*, *1.13. e*, och *1.13. f*, är den valda standard versionen *1.14. c*.

AKS väljer standardvärdet N-1 för att ge kunderna en känd, stabil och korrigerad version som standard.

## <a name="list-currently-supported-versions"></a>Lista över versioner som stöds för närvarande

Om du vill ta reda på vilka versioner som för närvarande är tillgängliga för din prenumeration och region använder du kommandot [AZ AKS get-versions][az-aks-get-versions] . I följande exempel visas tillgängliga Kubernetes-versioner för regionen *östra* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad händer när en kund uppgraderar ett Kubernetes-kluster med en lägre version som inte stöds?**

Om du använder *n-3-* versionen är du utanför supporten och kommer att bli ombedd att uppgradera. Om uppgraderingen från version n-3 till n-2 lyckas är du nu i våra support principer. Några exempel:

- Om den äldsta AKS-versionen som stöds är *1.13. a* och du är på *1.12. b* eller äldre är du utanför supporten.
- Om uppgraderingen från *1.12. b* till *1.13. a* eller senare lyckas är du tillbaka i våra support principer.

Uppgraderingar till versioner som är äldre än det stödda fönstret i *N-2* stöds inte. I sådana fall rekommenderar vi att kunderna skapar nya AKS-kluster och distribuerar om sina arbets belastningar med versioner i fönstret som stöds.

**Vad innebär "utanför supporten"**

Utanför support innebär att den version som du kör ligger utanför listan över versioner som stöds och du uppmanas att uppgradera klustret till en version som stöds när du begär support. Dessutom gör AKS inte någon körning eller andra garantier för kluster utanför listan över versioner som stöds.

**Vad händer när en kund skalar ett Kubernetes-kluster med en lägre version som inte stöds?**

För mindre versioner som inte stöds av AKS, måste skala in eller ut fortsätta att fungera, men det rekommenderas starkt att du uppgraderar för att återställa klustret till support.

**Kan en kund stanna kvar på en Kubernetes-version för alltid?**

Ja. Men om klustret inte finns i en av de versioner som stöds av AKS, ligger klustret utanför AKS-support-principerna. Azure uppgraderar inte automatiskt klustret eller så tas det bort.

**Vilken version kontrollerar kontroll planet om Node-poolen inte finns i någon av de AKS-versioner som stöds?**

Kontroll planet måste vara inom ett fönster med versioner från alla noder i pooler. Mer information om hur du uppgraderar kontroll planet eller Node-pooler finns i dokumentationen om hur du [uppgraderar Node-pooler](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Nästa steg

Information om hur du uppgraderar klustret finns i [uppgradera ett Azure Kubernetes service-kluster (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
