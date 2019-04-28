---
title: Azure Kubernetes Service (AKS) stöder principer
description: Läs mer om Azure Kubernetes Service (AKS) supportprinciper, delat ansvar, förhandsversion/Alpha/Beta-funktioner.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032482"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Azure Kubernetes Service (AKS) stöder principer

Den här artikeln innehåller information kring AKS-principer för teknisk support, begränsningar och information, inklusive hantering av worker, hanteras styra plan komponenter från tredje part komponenter med öppen källkod och säkerhet / uppdateringshantering.

## <a name="service-updates--releases"></a>Tjänstuppdateringar och versioner

* Viktig information finns i den [AKS viktig][1]
* Funktioner i offentlig förhandsversion, se [AKS Förhandsversionsfunktioner och relaterade projekt][2]

## <a name="what-is-managed"></a>Vad är ”hanterad”

Till skillnad från IaaS molnet baskomponenterna, till exempel beräkning eller nätverk, som exponera på låg nivå kontroller och anpassningsalternativ för användare att använda AKS-tjänsten ger en nyckelfärdig Kubernetes-distribution som representerar den gemensamma uppsättningen konfigurationer och funktioner som krävs för Kubernetes. Kunder som använder den här tjänsten har ett begränsat antal andra alternativ, distribution och anpassningar. Det innebär också att kunder inte behöver oroa dig eller hantera Kubernetes-kluster direkt.

Med AKS, kommer kunden en fullständigt hanterad **kontrollplanet** – där kontrollplanet innehåller alla komponenter och tjänster som krävs för att driva och förse slutanvändarna Kubernetes-kluster. Alla komponenter i Kubernetes bibehålls och hanteras av Microsoft.

Med den hanterade **kontrollplanet** hanteras och övervakas av Microsoft har följande komponenter:

* Kubelet / Kubernetes API-servrar
* Etcd eller en kompatibel nyckel/värde-lager – inklusive kvaliteten på tjänsten, skalbarhet och runtime
* DNS-tjänster (till exempel kube-dns / CoreDNS)
* Kubernetes-Proxy/nätverk

AKS är inte en 100% hanteras **kluster** lösning. Vissa komponenter (till exempel arbetsnoder) har vissa **delade ansvarsområden** fall där åtgärder för att underhålla AKS-kluster kräver interaktion från användaren. Till exempel arbetsnod OS security patch program.

 **Hanterade**, innebär det att Microsoft och AKS-teamet distribuerar, fungerar och är ansvarig för tillgänglighet och funktioner i dessa tjänster. **Kunder kan inte ändra de här komponenterna**. Anpassning är begränsad för att säkerställa en konsekvent och skalbar användarupplevelse. En helt anpassningsbar lösning Se [AKS-Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> Det är viktigt att förstå att Azure Kubernetes Service arbetsnoder visas i Azure Portal som vanliga Azure IaaS-resurser, även om de virtuella datorerna distribueras i en anpassad Azure-resursgrupp (föregås av prefixet MC\\*). En användare kan ändra dem, SSH till dem precis som vanliga virtuella datorer (du kan dock ändra den grundläggande OS-avbildningen och ändringar kan inte sparas via en uppdatering eller starta om) och du kan koppla andra Azure-resurs till dem eller annat sätt modifiera dem. **Detta av utanför band-hantering och anpassning innebär dock att själva AKS-klustret kan bli unsupportable. Undvik att worker noden ändring om inget annat anges av Microsoft Support.**

## <a name="what-is-shared-responsibility"></a>Vad är delat ansvar

När kluster skapas skapas ett antal Kubernetes arbetsnoder som definierats av kunden i AKS. Dessa noder, enligt vad som anges är där kundens arbetsbelastningar körs. Kunder som kan äger och visa eller ändra de här arbetsnoder.

Eftersom dessa noder kör privata kod och lagra känsliga data, Microsoft-supporten har **begränsad åtkomst** kunden i alla klusternoder. Microsoft-supporten kan inte logga in på, kör kommandon eller visa loggar för dessa noder utan uttryckliga kunden behörighet och/eller behöver hjälp att köra felsökning kommandon enligt anvisningarna från supporten.

Microsoft tar mycket försiktig så att begränsa ”i bakgrunden” hanteringen av dessa noder på grund av dessa arbetsnoder känsliga natur. Även om Kubernetes master-noder, etcd och andra komponenter misslyckas (hanteras av Microsoft) fortsätter din arbetsbelastning att köras i många fall. Om arbetsnoderna ändras utan att vård, det leda till förlust av data och/eller arbetsbelastning och återge klustret unsupportable.

## <a name="azure-kubernetes-service-support-coverage"></a>Azure Kubernetes Service-supporttäckning

**Microsoft tillhandahåller teknisk support för följande:**

* Anslutning till alla Kubernetes-komponenter som tillhandahålls och stöds av Kubernetes-tjänst (till exempel API-server)
* Hantering, drifttid, QoS och driften av Kubernetes styra plan tjänster (Kubernetes Master noder, API-servern, etcd, kube-dns till exempel.
* Etcd support omfattar automatiserad, transparent säkerhetskopieringar av alla etcd data varje halvtimme för katastrofåterställning planerings- och klustret återställningen av användartillstånd. Dessa säkerhetskopior är inte tillgängliga direkt till kunder/användare och används för att garantera tillförlitligheten för data och konsekvens
* Alla integreringspunkter i Azure Cloud Provider-drivrutinen för Kubernetes, till exempel integreringar till andra Azure-tjänster, till exempel belastningsutjämnare, beständiga volymer, Networking (Kubernetes och Azure CNI)
* Dataplaner komponenter som Kubernetes API-servern, etcd och kube-DNS-frågor eller problem runt anpassning av kontrollen.
* Utfärdar om nätverksteknik, till exempel Azure CNI, Kubenet eller andra problem med åtkomst och funktioner (DNS-matchning, paketförlust, Routning och så vidare).
  * Scenarier för nätverk som stöds omfattar Kubenet (grundläggande) och avancerade nätverk (Azure CNI) i klustret och associerade komponenter, anslutning till andra Azure-tjänster och program. Dessutom stöds ingress-styrenheter och ingress/konfigurationen för belastningsutjämnaren, nätverkets prestanda och svarstid av Microsoft.

**Microsoft tillhandahåller inte teknisk support för följande:**

* Rådgivande / ”instruktioner” Använd Kubernetes frågor, till exempel hur du skapar anpassade ingress-styrenheter och programmet arbetsbelastning frågor och tredje-part/OSS-paket eller verktyg som ligger utanför omfånget.
  * Rådgivande biljetter för AKS-kluster funktioner, anpassning, justeringsåtgärder – t.ex Kubernetes problem/How-to-tos är inom omfånget.
* Projekt för öppen källkod från tredje part inte har angetts som en del av Kubernetes styra plan eller distribueras med AKS-kluster, till exempel Istio, Helm, Envoy och andra.
  * För tredje parts öppen källkod-projekt, till exempel Helm och Kured, erbjuds support efter bästa förmåga exempel och program som angetts i Microsoft-dokumentationen och där det verktyget för öppen källkod från tredje part kan integreras med Kubernetes Azure molnleverantör eller andra AKS-specifika buggar.
* Stängd källkod programvara från tredje part – detta kan inkludera säkerhetsgenomsökning verktyg för nätverk enheter eller program.
* Problem om ”flera moln” eller flera leverantörer build-detaljer stöds inte, till exempel köra en leverantörslösning för federerade med flera offentliga moln stöds inte.
* Specifika anpassningar, förutom dem som beskrivs i officiellt [dokumentationen om AKS][3].
  > [!NOTE]
  > Problem och buggar runt Nätverkssäkerhetsgrupper stöds. Till exempel kan stöd besvara frågor kring NSG: er som inte kan uppdatera eller oväntade resultat för NSG eller belastningsutjämnare.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Azure Kubernetes Service supporttäckning (Arbetsnoder)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Microsofts ansvar för Azure Kubernetes Service-arbetsnoder

Enligt vad som anges i den `shared responsibility` avsnittet Kubernetes arbetsnoder faller inom en modell för gemensamma ansvar, där:

* Innehåller den grundläggande operativsystemsavbildningen krävs tillägg (till exempel övervakning och nätverk agenter)
* Automatisk leverans av korrigeringsfiler för operativsystem till arbetsnoder
* Automatisk reparation av problem med Kubernetes styra plan komponenter som körs på arbetsnoderna, till exempel:
  * kube-proxy
  * nätverk tunnlar som tillhandahåller kommunikationsvägar till Kubernetes master-komponenter
  * kubelet
  * daemon för docker/moby

> [!NOTE]
> Om en kontroll plan komponent är inte kan användas på en underordnad nod, kan AKS-teamet behöva starta om hela arbetsnoden för att lösa problemet. Detta är klar för en användares räkning och inte utförs om inte en kund eskalering görs (på grund av att komma åt kunder active arbetsbelastning och data). När det är möjligt AKS personal ska starta att göra alla obligatoriska om icke-programmet påverkas.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Kundens ansvarsområden för Azure Kubernetes Service-arbetsnoder

**Microsoft gör inte:**

- Automatiskt omstart arbetsnoder för OS nivå korrigeringar ska börja gälla **(för närvarande kan se nedan)**

Korrigeringsfiler för operativsystem levereras till arbetsnoder, men det är den **kundens ansvar** att starta om arbetsnoder för att ändringarna ska börja gälla. Den här automatiska korrigeringar innehåller delade bibliotek, daemons, till exempel SSHD och andra nivån system/OS-komponenter.

För Kubernetes-uppgraderingar, **kunderna är ansvariga för körning av uppgraderingen** via Kontrollpanelen i Azure eller Azure CLI. Detta gäller för uppdateringar som innehåller säkerhets- eller funktioner förbättringar av Kubernetes.

> [!NOTE]
> Eftersom AKS är en `managed service` målen slutet av tjänsten omfattar borttagning av ansvar för korrigeringar, uppdateringar, loggar in med flera så att de blir en mer helt hanterade och helautomatisk tjänst. De här objekten (omstart av noden, automatiska korrigeringar) kan tas bort i kommande versioner när våra funktioner för att slutpunkt till slutpunkt management ökar.

### <a name="security-issues-and-patching"></a>Uppdatering och säkerhetsproblem

I vissa fall (till exempel CVEs) finns en säker i en eller flera av komponenterna i AKS-tjänsten. I sådana scenarier kommer AKS korrigera alla berörda kluster för att åtgärda problemet om möjligt eller ge användarna uppgradera vägledningen.

För de arbetsnoder som påverkas av sådana ett säkerhetshål om det finns en stilleståndstid korrigering av problemet AKS-teamet tillämpa den korrigeringen och meddela användare om ändringen.

Om en säkerhetskorrigering kräver arbetsnod startar om, Microsoft meddelar kunden om det här kravet och kunden ansvarar för att köra omstarten eller uppdatera för att hämta uppdateringen för sina kluster. Om användarna inte är tillämpliga korrigeringar enligt AKS vägledning fortsätter deras kluster sårbara för med information om problemet.

### <a name="node-maintenance-and-access"></a>Noden underhåll och åtkomst

Eftersom arbetsnoderna är ett delat ansvar och under ägarskap för kunder, kan kunder logga in på de här arbetarna och utföra potentiellt skadliga ändringar, till exempel kernel-uppdateringar, ta bort paket och installera nya paket.

Om kunder utföra skadliga åtgärder eller åtgärder som utlöser kontroll plan komponenter för att arbeta offline eller blivit annars fungerar inte, AKS-tjänsten identifierar det här felet och utföra autoreparation om du vill återställa arbetsnoden till föregående aktivt tillstånd.

Kunder kan logga in och ändra arbetsnoder, men det är *rekommenderas inte* eftersom detta kan göra ditt kluster unsupportable.

## <a name="network-ports-access-and-network-security-groups"></a>Nätverksportar, åtkomst och Nätverkssäkerhetsgrupper

Som en hanterad tjänst har AKS särskilda krav för nätverk och anslutning. Dessa krav är mindre flexibelt än normala IaaS-komponenter. Till skillnad från andra IaaS-komponenter kan vissa åtgärder (till exempel anpassning av nätverkssäkerhetsgruppsregler, blockerar viss port, URL: en lista över tillåtna och så vidare) återge klustret unsupportable (till exempel brandväggsregler som blockerar utgående port 443).

> [!NOTE]
> Helt låsa utgående (till exempel explicit domän/port vitlistning) från ditt kluster är inte ett AKS-scenario som stöds just nu. Lista över webbadresser och portar kan ändras utan varning och kan lämnas med Azure-supporten via en biljett. Den angivna listan är endast avsedd för kunder som är beredd att acceptera som *tillgängligheten för ditt kluster kan påverkas när som helst.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Alpha/Beta Kubernetes-funktioner (stöds inte)

AKS har endast stöd för stabila funktioner i det överordnade Kubernetes-projektet. Alpha/Beta-funktioner som är tillgängliga i överordnade Kubernetes stöds inte om inte och kommer annars dokumenterade.

Det finns två fall där Alpha eller Beta-funktioner kan lanseras innan allmän tillgänglighet:

* Kunderna har uppfyllt med AKS produkten, support eller teknikteam och uttryckligen har blivit ombedd att testa de här nya funktionerna.
* De här funktionerna har [aktiverat via en funktionsflagga] [ 2] (explicit opt-in)

## <a name="preview-features--feature-flags"></a>Förhandsversionsfunktioner / funktionen flaggor

För egenskaper och funktioner som kräver utökade testning, community och Användarfeedback Microsoft kommer att släppa nya förhandsvisningsfunktioner eller funktioner bakom en funktionsflagga. Dessa funktioner ska betraktas som förhandsversioner / Beta och är tillgängliga för att ge användarna en chans att testa de här nya funktionerna.

Men dessa förhandsversion / funktionsflagga funktioner inte är avsedda för användning i produktion – API: er, Funktionsändring, felkorrigeringar och andra ändringar kan göras som kan leda till instabil kluster och driftstopp. Stöd för dessa funktioner är begränsad till bugg/problem reporting. Gör inte dessa funktioner på produktionssystem eller prenumerationer.

> [!NOTE]
> Aktivera förhandsversionsfunktioner gälla i Azure **prenumeration** nivå. Installera inte förhandsversionsfunktioner för produktion-prenumeration som den kan ändra standardbeteendet API som påverkar normal drift.

## <a name="upstream-bugs-and-issues"></a>Överordnad buggar och problem

Beroende på utveckling hastighet i överordnade Kubernetes-projektet finns utan undantag buggar som inte går att korrigera eller arbetat runt i AKS-systemet och i stället kräver större korrigeringar till överordnade projekt (som Kubernetes, noden/Worker operativsystem och kärnor). För komponenter som vi äger (till exempel Azure Cloud-Provider), genomförs AKS/Azure-personal att åtgärda problemet uppströms i communityn.

I de fall där ett ärende om teknisk support är roten orsakade att en eller flera överordnade buggar, stöd för AKS och teknik kommer att göra följande:

* Identifiera och länka de överordnade buggarna med någon stödjande information om varför detta påverkar klustret och/eller arbetsbelastning. Kunder ges med länkar till nödvändiga lagringsplatser/problem att titta på problem och se när en ny Kubernetes/andra versionen kommer att innehålla fix(es)
* Potentiella genvägar eller åtgärder: I vissa fall kan det vara möjligt att undvika problemet – i det här fallet en ”[kända problem](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)” kommer att fyllas i AKS-databasen som förklarar:
  * Problem och länka till överordnad buggar
  * Lösning och information kring uppgradera/övrigt persistence för lösningen
  * Ungefärlig tidslinjer ska ingå baserat på den överordnade frisläppningstakt

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
