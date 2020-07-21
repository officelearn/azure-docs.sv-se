---
title: Kubernetes-versioner som stöds i Azure Kubernetes Service
description: Förstå Kubernetes-versionens support policy och livs cykel för kluster i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 019ae80020dafb54f2c06dd504797f21069914ae
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86507071"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes Service (AKS)

Kubernetes community släpper lägre versioner ungefär var tredje månad. Dessa versioner innehåller nya funktioner och förbättringar. Uppdaterings versioner är oftare (ibland veckovis) och är endast avsedda för viktiga fel korrigeringar i en lägre version. Dessa korrigerings versioner innehåller korrigeringar för säkerhets sårbarheter eller större buggar.

## <a name="kubernetes-versions"></a>Kubernetes-versioner

Kubernetes använder standard versions schemat för [semantisk versions](https://semver.org/) hantering, vilket innebär att varje version av Kubernetes följer det här numreringsschema:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Varje tal i versionen indikerar allmän kompatibilitet med den tidigare versionen:

* Huvud versioner ändras när inkompatibla API-ändringar eller bakåtkompatibilitet kan vara brutna.
* Lägre versioner ändras när funktions förändringar görs som är bakåtkompatibla med andra mindre versioner.
* Korrigerings versioner ändras när bakåtkompatibla fel korrigeringar görs.

Användarna bör sträva efter att köra den senaste korrigerings versionen av den lägre version som de kör, till exempel om ditt produktions kluster är på **`1.17.7`** och **`1.17.8`** är den senaste tillgängliga korrigerings versionen som är tillgänglig för *1,17* -serien, bör du uppgradera till så snart som möjligt för att **`1.17.8`** säkerställa att klustret är fullständigt korrigerat och stöds.

## <a name="kubernetes-version-support-policy"></a>Support princip för Kubernetes-version

AKS definierar en allmänt tillgänglig version, som en version som är aktive rad i alla service nivå mål eller SLA-mätningar och som är tillgänglig i alla regioner. AKS stöder tre sekundär-versioner av Kubernetes:

* Den senaste sekundär versions versionen som släpps i AKS (som vi refererar till som N). 
* Två tidigare del versioner. 
* Varje lägre version som stöds har även stöd för högst två (2) stabila korrigeringar.
* AKS kan också ha stöd för för hands versioner, som uttryckligen märks och omfattas av för [hands versions][preview-terms]villkor.

> [!NOTE]
> AKS använder säker distributions praxis som inbegriper stegvis distribution av regioner. Det innebär att det kan ta upp till 10 arbets dagar för en ny version eller en ny version som är tillgänglig i alla regioner.

Det fönster som stöds av Kubernetes-versioner på AKS kallas "N-2": (N (senaste versionen)-2 (del versioner)).

Om AKS till exempel introducerar *1.17. a* idag tillhandahålls support för följande versioner:

Ny del version    |    Versions lista som stöds
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Där ". us" är representativ för korrigerings versioner.

När en ny del version introduceras, är den äldsta lägre versionen och de nya korrigerings versionerna som stöds föråldrade och tas bort. Till exempel, om den aktuella versions listan som stöds är:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

Och AKS släpper 1,18. \* , innebär det att alla 1,15.- \* versioner kommer att tas bort och att de inte kommer att fungera på 30 dagar.

> [!NOTE]
> Observera att om kunderna kör en Kubernetes-version som inte stöds uppmanas de att uppgradera när de begär support för klustret. Kluster som kör Kubernetes-versioner som inte stöds omfattas inte av [support principerna för AKS](./support-policies.md).

Förutom ovanstående, stöder AKS högst två **korrigerings** versioner av en specifik del version. Så få följande versioner som stöds:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Om AKS-versioner `1.17.9` och `1.16.11` , de äldsta korrigerings versionerna är föråldrade och tas bort, och den versions lista som stöds blir:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

## <a name="release-and-deprecation-process"></a>Version och utfasnings process

Du kan referera till kommande versions utgåvor och utfasningar i [AKS Kubernetes release-kalendern](#aks-kubernetes-release-calendar).

För nya **mindre** versioner av Kubernetes
1. AKS publicerar ett förmeddelande med planerat datum för en ny versions version och respektive gamla versions utfasning på AKS- [versionen](https://aka.ms/aks/releasenotes) , minst 30 dagar innan borttagning.
2. AKS publicerar ett [meddelande om tjänst hälsa](../service-health/service-health-overview.md) som är tillgänglig för alla användare med AKS och Portal åtkomst och skickar ett e-postmeddelande till prenumerations administratörerna med den planerade versionen av borttagnings datum.
3. Användare har **30 dagar** från borttagnings versionen för att uppgradera till en lägre versions version som stöds för att fortsätta få support.

För nya **korrigerings** versioner av Kubernetes
  * På grund av den brådskande typen av korrigerings versioner kan dessa införas i tjänsten när de blir tillgängliga.
  * I allmänhet gör AKS inte en bred kommunikation för att lansera nya korrigerings versioner. Men AKS övervakar och validerar alltid tillgängliga CVE-korrigeringsfiler för att stödja dem i AKS i rimlig tid. Om en kritisk korrigering påträffas eller om en användar åtgärd krävs kommer AKS att meddela användarna om att de kan uppgradera till den nyligen tillgängliga korrigeringen.
  * Användare har **30 dagar** från den tidpunkt då en korrigerings version tas bort från AKS för att uppgraderas till en korrigerings fil som stöds och fortsätta få support.

### <a name="supported-versions-policy-exceptions"></a>Princip undantag för versioner som stöds

AKS förbehåller sig rätten att lägga till eller ta bort nya/befintliga versioner som har identifierats ha en eller flera kritiska produktioner som påverkar buggar eller säkerhets problem utan föregående meddelande.

Vissa korrigerings versioner kan hoppas över eller att distributionen accelereras beroende på fel-eller säkerhets problemets allvarlighets grad.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal-och CLI-versioner

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI, är klustret standardvärdet för den lägre versionen av N-1 och den senaste korrigeringen. Om AKS till exempel stöder *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e*, och *1.15. f*, är den valda standard versionen *1.16. c*.

Om du vill ta reda på vilka versioner som för närvarande är tillgängliga för din prenumeration och region använder du kommandot [AZ AKS get-versions][az-aks-get-versions] . I följande exempel visas tillgängliga Kubernetes-versioner för regionen *östra* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```


## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes, versions kalender

Den senaste versions historiken finns [här](https://en.wikipedia.org/wiki/Kubernetes#History).

|  K8s-version | Överordnad version  | AKS för hands version  | AKS GA  | Uttjänta |
|--------------|-------------------|--------------|---------|-------------|
| 1,17  | Dec-09-19  | 19 Jan   | Jul 20  | 1,20 GA | 
| 1,18  | Mar – 23-20  | 20 maj   | Aug 20  | 1,21 GA | 
| 1,19  | Aug – 04-20  | Aug 20   | 20 nov  | 1,22 GA | 
| 1,20  | * Nov 20    | * Dec 20   | * Jan 21  | 1,23 GA | 

\*Datum bekräftelse för väntande lansering av uppladdning.

## <a name="faq"></a>Vanliga frågor

**Vad händer när en användare uppgraderar ett Kubernetes-kluster med en lägre version som inte stöds?**

Om du använder *n-3-* versionen eller äldre, innebär det att du inte har stöd för och att du kommer att bli ombedd att uppgradera. När uppgraderingen från version n-3 till n-2 lyckas, är du tillbaka i våra support principer. Till exempel:

- Om den äldsta AKS-versionen som stöds är *1.15. a* och du är på *1.14. b* eller äldre är du utanför supporten.
- När du har uppgraderat från *1.14. b* till *1.15. en* eller fler lyckas är du tillbaka i våra support principer.

Nedgradering stöds inte.

**Vad innebär "utanför supporten"**

Utanför supporten innebär att den version som du kör ligger utanför listan över versioner som stöds och du uppmanas att uppgradera klustret till en version som stöds när du begär support, om du inte är inom 30 dagar efter versionens utfasnings period. Dessutom gör AKS inte någon körning eller andra garantier för kluster utanför listan över versioner som stöds.

**Vad händer när en användare skalar ett Kubernetes-kluster med en lägre version som inte stöds?**

För mindre versioner som inte stöds av AKS, måste skala in eller ut fortsätta att fungera, men det finns inga garantier för tjänst kvalitet, så vi rekommenderar att du uppgraderar för att återställa klustret till support.

**Kan en användare stanna kvar på en Kubernetes-version för alltid?**

Om ett kluster har stöd för fler än tre (3) lägre versioner och har befunnits medföra säkerhets risker, kontaktar Azure dig för att proaktivt uppgradera klustret. Om du inte vidtar ytterligare åtgärder förbehåller Azure rätten att automatiskt uppgradera ditt kluster för din räkning.

**Vilken version kontrollerar kontroll planet om Node-poolen inte finns i någon av de AKS-versioner som stöds?**

Kontroll planet måste vara inom ett fönster med versioner från alla noder i pooler. Mer information om hur du uppgraderar kontroll planet eller Node-pooler finns i dokumentationen om hur du [uppgraderar Node-pooler](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Kan jag hoppa över en version vid uppgradering?**

Nej, efter Kubernetes bästa praxis, tillåter AKS endast uppgraderingar till omedelbar nästa korrigering eller lägre version. Azure Portal visar bara de versioner du kan uppgradera till, och i CLI kan du `az aks get-upgrades -n MyAKSCluster -g MyResourceGroup` Se tillgängliga uppgraderingar från den aktuella versionen.

**Hur kan jag uppgradera till en version som stöds om jag har flera versioner bakom den senaste versionen som stöds?**

Om du är i det här fallet måste du undvika att falla över flera versioner från listan över stödda, men om du är i det här fallet kommer AKS alltid att tillåta uppgraderingen till den lägsta version som stöds.

## <a name="next-steps"></a>Nästa steg

Information om hur du uppgraderar klustret finns i [uppgradera ett Azure Kubernetes service-kluster (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
