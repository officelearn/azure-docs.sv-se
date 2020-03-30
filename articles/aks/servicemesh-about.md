---
title: Om servicenät
description: Få en översikt över servicenät, deras arkitektur och funktioner och vilka kriterier du bör tänka på när du väljer en att distribuera.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594319"
---
# <a name="about-service-meshes"></a>Om servicenät

Ett servicenät ger funktioner som trafikhantering, återhämtning, princip, säkerhet, stark identitet och observerbarhet för dina arbetsbelastningar. Ditt program är frikopplat från dessa driftsfunktioner och tjänstnätet flyttar dem ut ur programlagret och ner till infrastrukturlagret.

## <a name="scenarios"></a>Scenarier

Det här är några av de scenarier som kan aktiveras för dina arbetsbelastningar när du använder ett tjänstnät:

- **Kryptera all trafik i klustret** - Aktivera ömsesidig TLS mellan angivna tjänster i klustret. Detta kan utökas till ingående och utgående vid nätverksperimetern. Ger ett säkert som standardalternativ utan ändringar som behövs för programkod och infrastruktur.

- **Kanariefågel och stegvisa distributioner** – Ange villkoren för en delmängd av trafiken som ska dirigeras till en uppsättning nya tjänster i klustret. Vid lyckad test av kanariefågelfrisättning tar du bort villkorlig routning och fas gradvis ökar % av all trafik till ny tjänst. Så småningom kommer all trafik att dirigeras till ny tjänst.

- **Trafikhantering och manipulering** - Skapa en princip för en tjänst som kommer att begränsa begränsa all trafik till en version av en tjänst från ett visst ursprung. Eller en princip som tillämpar en strategi för återförsök på klasser av fel mellan angivna tjänster. Spegla direkt trafik till nya versioner av tjänster under en migrering eller för att felsöka problem. Injicera fel mellan tjänster i en testmiljö för att testa återhämtningen.

- **Observerbarhet** - Få insikt i hur dina tjänster är anslutna trafik som flödar mellan dem. Hämta mått, loggar och spårningar för all trafik i klustret och ingående/utgående. Lägg till distribuerade spårningsfunktioner i dina program.

## <a name="architecture"></a>Arkitektur

Ett servicenät består vanligtvis av ett kontrollplan och dataplanet.

**Kontrollplanet** har ett antal komponenter som stöder hantering av servicenätet. Detta innehåller vanligtvis ett hanteringsgränssnitt som kan vara ett användargränssnitt eller ett API. Det finns vanligtvis också komponenter som hanterar regel- och principdefinitionerna som definierar hur servicenätet ska implementera specifika funktioner. Det finns också komponenter som hanterar aspekter av säkerhet som stark identitet och certifikat för mTLS. Tjänstnät har vanligtvis också en mått- eller observationskomponent som samlar in och sammanställer mått och telemetri från arbetsbelastningarna.

**Dataplanet** består vanligtvis av en proxy som matas in transparent som en sidovagn till dina arbetsbelastningar. Den här proxyn är konfigurerad för att styra all nätverkstrafik in och ut ur podden som innehåller din arbetsbelastning. Detta gör att proxyn kan konfigureras för att skydda trafik via mTLS, dynamiskt dirigera trafik, tillämpa principer för trafik och att samla in mått och spåra information. 

![Typisk servicenätarkitektur](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Funktioner

Var och en av tjänstnäten har en naturlig passform och fokus på att stödja specifika scenarier, men du kommer vanligtvis att upptäcka att de flesta kommer att implementera ett antal, om inte alla, av följande funktioner.

### <a name="traffic-management"></a>Trafikledning 

- **Protokoll** – skikt 7 (http, grpc)
- **Dynamisk routning** – villkorlig, viktning, spegling
- **Återhämtning –** timeout, återförsök, brytare
- **Policy** – åtkomstkontroll, räntegränser, kvoter
- **Testning** - felinsprutning

### <a name="security"></a>Säkerhet

- **Kryptering** – mTLS, certifikathantering, extern certifikatutfärdarorganisation
- **Stark identitet** – SPIFFE eller liknande
- **Auth** – autentisering, auktorisation

### <a name="observability"></a>Observerbarhet

- **Metrics** - gyllene mått, prometheus, grafana
- **Spårning** - spår över arbetsbelastningar
- **Trafik** – kluster, ingång/utgående

### <a name="mesh"></a>Nät

- **Beräkning som stöds** - Kubernetes, virtuella datorer
- **Multi-kluster** - gateways, federation

## <a name="selection-criteria"></a>Urvalskriterier

Innan du väljer ett servicenät ska du se till att du förstår dina krav och orsakerna till att du installerar ett servicenät. Försök att ställa följande frågor.

- **Räcker en ingresskontrollant för mina behov?** - Ibland har en förmåga som a / b-testning eller trafik uppdelning vid inträngningen är tillräckligt för att stödja det nödvändiga scenariot. Lägg inte till komplexitet i din miljö utan uppsida.

- **Kan mina arbetsbelastningar och miljö tolerera ytterligare omkostnader?** - Alla ytterligare komponenter som krävs för att stödja tjänsten mesh kräver ytterligare resurser som cpu och minne. Dessutom lägger alla proxyservrar och tillhörande principkontroller till svarstid i trafiken. Om du har arbetsbelastningar som är mycket känsliga för svarstid eller inte kan tillhandahålla ytterligare resurser för att täcka tjänstnätkomponenterna bör du tänka ut igen.

- **Är detta att lägga till ytterligare komplexitet i onödan?** - Om anledningen till att installera ett servicenät är att få en kapacitet som inte nödvändigtvis är avgörande för affärs- eller driftteamen, bör du överväga om den ytterligare komplexiteten i installation, underhåll och konfiguration är värt det.

- **Kan detta antas i ett stegvis tillvägagångssätt?** - Några av de servicenät som ger en hel del kapacitet kan antas i en mer inkrementell strategi. Installera bara de komponenter du behöver för att säkerställa din framgång. När du är mer självsäker och ytterligare funktioner krävs, sedan utforska dessa. Motstå frestelsen att installera *allt* från början.

Om du efter noggrant övervägande bestämmer dig för att du behöver ett servicenät för att tillhandahålla de funktioner som krävs, då är ditt nästa beslut *vilket servicenät?*

Tänk på följande områden och vilka av dem som är mest i linje med dina krav. Detta kommer att guida dig mot den bästa passformen för din miljö och arbetsbelastningar. I avsnittet [Nästa steg](#next-steps) får du mer detaljerad information om specifika servicenät och hur de mappas till dessa områden.

- **Teknisk** - trafikledning, policy, säkerhet, observerbarhet

- **Business** - kommersiellt stöd, stiftelse (CNCF), OSS licens, styrning

- **Drift** – installation/uppgraderingar, resurskrav, prestandakrav, integrationer (mått, telemetri, instrumentpaneler, verktyg, SMI), blandade arbetsbelastningar (Linux- och Windows-nodpooler), beräkning (Kubernetes, virtuella datorer), multikluster

- **Säkerhet** - autentisering, identitet, certifikathantering och rotation, inkopplad extern certifikatutfärdare


## <a name="next-steps"></a>Nästa steg

Följande dokumentation innehåller mer information om tjänstnät som du kan prova på Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Läs mer om Istio ...][istio-about]

> [!div class="nextstepaction"]
> [Läs mer om Linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Läs mer om konsul ...][consul-about]

Du kanske också vill utforska Service Mesh Interface (SMI), ett standardgränssnitt för servicenät på Kubernetes:

- [Service Mesh-gränssnitt (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md