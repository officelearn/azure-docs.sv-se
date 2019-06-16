---
title: Kubernetes-versioner som stöds i Azure Kubernetes Service
description: Förstå Supportpolicy för Kubernetes-version och livscykeln för kluster i Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 2d555908007f4e43a38b6d0eff909ef5050878ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069680"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes Service (AKS)

Kubernetes-communityn släpper nya lägre versioner ungefär var tredje månad. Dessa versioner inkluderar nya funktioner och förbättringar. Korrigeringsversioner släpps oftare (ibland varje vecka) och avser endast korrigeringar av allvarliga buggar i en lägre version. Dessa patch-versioner innehåller korrigeringar av säkerhetsrisker eller större buggar som påverkar ett stort antal kunder och produkter som körs i produktionsmiljön baserat på Kubernetes.

AKS syftar till att certifiera och publicera nya Kubernetes-versioner inom 30 dagar från en publiceringsgren omfattas av stabiliteten för versionen.

## <a name="kubernetes-versions"></a>Kubernetes-versioner

Kubernetes använder standard [semantiska versionshantering](https://semver.org/) versionsschema. Det innebär att varje version av Kubernetes följer den här numrering:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

Varje tal i version anger allmänna kompatibiliteten med den tidigare versionen:

* Huvudversioner ändras när inkompatibla API ändras eller bakåt kompatibilitet brytas.
* Delversioner ändras när funktionen ändringar som är bakåtkompatibel till andra mindre versioner.
* Korrigera versioner ändras när bakåtkompatibla felkorrigeringar görs.

I allmänhet användare bör vinnlägga din att köra den senaste patch-versionen av den lägre versionen av de körs, till exempel om ditt produktionskluster är på *1.13.6* och *1.13.7* är den senaste korrigeringen version som är tillgängliga för den *1.13* serien, bör du uppgradera till *1.13.7* så snart kan du se till att klustret är fullständigt korrigeringar och stöd.

## <a name="kubernetes-version-support-policy"></a>Stödprincip för Kubernetes-version

AKS har stöd för fyra lägre Kubernetes-versioner:

* Den aktuella delversionen som släpps i AKS (N)
* Tre tidigare lägre versioner. Varje lägre version som stöds har även stöd för två stabila korrigeringsversioner.

Detta kallas för ”N-3”-(N (senaste versionen) – 3 (delversioner)).

Exempel: om AKS introducerar *1.13.x* idag finns stöd för följande versioner:

Ny delversion stöds versionslista
-----------------        ----------------------
1.13.x                   1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Där ”x” och ”en” och ”.b” är representativt versioner.

Se ”meddelanden” nedan för information om kommunikation om ändringar av version och förväntningar.

När en ny delversion introduceras är de äldsta mindre version och patch versioner som stöds inaktuell och tas bort. Till exempel om aktuellt listan över versioner som stöds är:

<a name="supported-version-list"></a>Listan över versioner som stöds
----------------------
1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b, 1.9.a, 1.9.b

Och AKS släpper 1.13.x, det innebär att 1.9.x-versioner (alla 1,9 versioner) kommer att tas bort och support upphör.

> [!NOTE]
> Observera att om kunderna använder ett Kubernetes-version som inte stöds kan de bli ombedd att uppgradera när du kontaktar supporten för klustret. Kluster som kör stöds inte Kubernetes-versioner omfattas inte av den [AKS stödprinciper](https://docs.microsoft.com/azure/aks/support-policies).


Förutom ovanstående på delversioner AKS har stöd för två senaste *patch** versioner av en viss minor-version. Till exempel få följande versioner som stöds:

<a name="supported-version-list"></a>Listan över versioner som stöds
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

Om överordnade Kubernetes är 1.12.3 och 1.11.6 och AKS frigör de korrigera versioner, de äldsta versionerna är inaktuell och ta bort och blir listan version som stöds:

<a name="supported-version-list"></a>Listan över versioner som stöds
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

### <a name="communications"></a>Kommunikationer

* För nya **mindre** versioner av Kubernetes
  * Alla användare får ett meddelande av den nya versionen och vilken version kommer att tas bort.
  * Kunder som kör version **som ska tas bort** meddelas att de har **60 dagar** att uppgradera till en version som stöds (t.ex. mindre version).
* För nya **patch** versioner av Kubernetes
  * Alla användare får ett meddelande om den nya patch-versionen släppts, och för att uppgradera till den senaste versionen för patch.
  * Användarna har **30 dagar** uppgraderas till en nyare, stöds patch-versionen. Användarna har **30 dagar** uppgraderas till en stöds patch-versionen innan den äldsta tas bort.

AKS definierar ”publicerad” som allmänt tillgängliga, aktiverat i alla SLO / kvalitet i tjänsten mätning av faktisk användning och finns i alla regioner.

> [!NOTE]
> Kunderna informeras om Kubernetes versioner och utfasningar när en mindre version är inaktuell eller tas bort användare ges 60 dagar att uppgradera till en version som stöds. När det gäller patch versioner får kunder 30 dagar att uppgradera till en version som stöds.

Meddelanden skickas:

* [Viktig information om AKS](https://aka.ms/aks/releasenotes)
* Azure-portalen meddelanden
* [Uppdatering av Azure-kanal][azure-update-channel]

### <a name="policy-exceptions"></a>Undantag för principen

AKS förbehåller sig rätten att lägga till eller ta bort nya/befintliga versioner som har identifierats så att den har en eller flera kritiska produktion som påverkar buggar eller säkerhetsproblem utan att i förväg.

Specifika patch versioner hoppas eller distributionen accelerated beroende på hur allvarligt problemet bugg eller säkerhet.

### <a name="azure-portal-and-cli-default-versions"></a>Azure portal och CLI standard-versioner

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI kan har klustret alltid värdet N-1 minor-version och senaste korrigeringen. Exempel: om AKS stöder *1.13.x*, *1.12.a* + *1.12.b*, *1.11.a*  +   *1.11.b*, *1.10.a* + *1.10b*, standardversionen för nya kluster är *1.12.b*.

AKS N-1 (minor.latestPatch, t.ex 1.12.b) att ge kunderna en känd, stabil som standard och korrigerade versionen som standard.

## <a name="list-currently-supported-versions"></a>Lista över versioner som stöds för närvarande

Om du vill ta reda på vilka versioner som är tillgängliga för din prenumeration och region kan du använda den [az aks get-versioner] [ az-aks-get-versions] kommando. I följande exempel visar en lista över tillgängliga Kubernetes-versioner för den *EastUS* region:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Utdata liknar följande exempel som visar att Kubernetes-version *1.13.5* finns den senaste versionen:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad händer när en kund uppgraderas ett Kubernetes-kluster med en mindre version som inte stöds?**

Om du använder den *n-4* version, du är utanför support och uppmanas att uppgradera. Om din uppgradering från version n-4 till n-3 lyckas, är nu vår support-principer. Exempel:

- Om AKS-versioner som stöds är *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *1.11d*, och *1.10.e* + *1.10F* och du har en *1.9.g* eller *1.9.h*, du är utanför support.
- Om uppgraderingen från *1.9.g* eller *1.9.h* till *1.10.e* eller *1.10.f* lyckas, är du tillbaka i den i vår supportprinciper.

Uppgraderingar till versioner som är äldre än *n-4* stöds inte. I sådana fall rekommenderar vi kunder skapar nya AKS-kluster och distribuera om sina arbetsbelastningar.

**Vad betyder ”av Support”**

'Utanför Support ”innebär att den version du kör ligger utanför listan över versioner som stöds och du blir ombedd att uppgradera klustret till en version som stöds när du kontaktar supporten. AKS gör dessutom inte alla runtime eller andra garantier för kluster utanför listan över versioner som stöds.

**Vad händer när en kund kan skalas upp ett Kubernetes-kluster med en mindre version som inte stöds?**

För mindre versioner som inte stöds av AKS kan fortsätter skala in eller ut att fungera utan problem.

**Kan en kund stanna kvar på ett Kubernetes-version permanent?**

Ja. Om klustret inte finns på en av de versioner som stöds av AKS är dock klustret utanför supportprinciper AKS. Azure inte automatiskt uppgradera klustret eller ta bort den.

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
