---
title: Nätverkssäkerhet för Azure Event Grid-resurser
description: I den här artikeln beskrivs hur du konfigurerar åtkomst från privata slutpunkter
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300159"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Nätverkssäkerhet för Azure Event Grid-resurser
I den här artikeln beskrivs hur du använder följande säkerhetsfunktioner med Azure Event Grid: 

- Tjänsttaggar för utgående (förhandsgranskning)
- IP-brandväggsregler för inträngning (förhandsgranskning)
- Privata slutpunkter för inträngning (förhandsgranskning)


## <a name="service-tags"></a>Tjänsttaggar
En tjänsttagg representerar en grupp IP-adressprefix från en viss Azure-tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras, vilket minimerar komplexiteten i frekventa uppdateringar av nätverkssäkerhetsregler. Mer information om tjänsttaggar finns i [Översikt över tjänsttaggar](../virtual-network/service-tags-overview.md).

Du kan använda tjänsttaggar för att definiera nätverksåtkomstkontroller i [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Använd tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (till exempel **AzureEventGrid**) i lämpligt *käll-* eller *målfält* för en regel kan du tillåta eller neka trafik för motsvarande tjänst.

| Tjänsttagg | Syfte | Kan du använda inkommande eller utgående? | Kan vara regional? | Kan du använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure-händelserutnät. <br/><br/>*Anm.:* Den här taggen täcker azure Event Grid-slutpunkter endast i US South Central, US East, US East 2, US West 2 och US Central. | Båda | Inga | Inga |


## <a name="ip-firewall"></a>IP-brandvägg 
Azure Event Grid stöder IP-baserade åtkomstkontroller för publicering till ämnen och domäner. Med IP-baserade kontroller kan du begränsa utgivare till ett ämne eller en domän till endast en uppsättning godkända datorer och molntjänster. Den här funktionen kompletterar de [autentiseringsmekanismer](security-authentication.md) som stöds av Event Grid.

Som standard är ämne och domän tillgängliga från internet så länge begäran levereras med giltig autentisering och auktorisering. Med IP-brandväggen kan du begränsa den ytterligare till endast en uppsättning IP-adresser eller IP-adressintervall i [CIDR-notation (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Utgivare som kommer från någon annan IP-adress kommer att avvisas och kommer att få ett 403 (förbjudet) svar.


## <a name="private-endpoints"></a>Privata slutpunkter
Du kan använda [privata slutpunkter](../private-link/private-endpoint-overview.md) för att tillåta inträngning av händelser direkt från ditt virtuella nätverk till dina ämnen och domäner på ett säkert sätt via en [privat länk](../private-link/private-link-overview.md) utan att gå igenom det offentliga internet. En privat slutpunkt är ett speciellt nätverksgränssnitt för en Azure-tjänst i ditt virtuella nätverk. När du skapar en privat slutpunkt för ditt ämne eller din domän ger den säker anslutning mellan klienter på ditt virtuella nätverk och din Event Grid-resurs. Den privata slutpunkten tilldelas en IP-adress från IP-adressintervallet för ditt virtuella nätverk. Anslutningen mellan den privata slutpunkten och tjänsten Event Grid använder en säker privat länk.

![Arkitekturdiagram](./media/network-security/architecture-diagram.png)

Med hjälp av privata slutpunkter för resursen Event Grid kan du:

- Säker åtkomst till ditt ämne eller domän från ett virtuella nätverk via Microsofts stamnätsnätverk i motsats till det offentliga internet.
- Anslut säkert från lokala nätverk som ansluter till det virtuella nätverket med VPN eller ExpressRoutes med privat peering.

När du skapar en privat slutpunkt för ett ämne eller en domän i ditt virtuella nätverk skickas en begäran om medgivande för godkännande till resursägaren. Om användaren som begär att den privata slutpunkten ska skapas också är ägare till resursen godkänns den här begäran om medgivande automatiskt. Annars är anslutningen i **väntande** tillstånd tills den har godkänts. Program i det virtuella nätverket kan ansluta till tjänsten Event Grid via den privata slutpunkten sömlöst, med samma anslutningssträngar och auktoriseringsmekanismer som de annars skulle använda. Resursägare kan hantera begäranden om medgivande och de privata slutpunkterna via fliken **Privata slutpunkter** för resursen i Azure-portalen.

### <a name="connect-to-private-endpoints"></a>Ansluta till privata slutpunkter
Utgivare på ett virtuella nätverk med den privata slutpunkten bör använda samma anslutningssträng för ämnet eller domänen som klienter som ansluter till den offentliga slutpunkten. DNS-upplösning dirigerar automatiskt anslutningar från det virtuella nätverket till ämnet eller domänen via en privat länk. Event Grid skapar en [privat DNS-zon](../dns/private-dns-overview.md) som är kopplad till det virtuella nätverket med den nödvändiga uppdateringen för de privata slutpunkterna som standard. Om du använder din egen DNS-server kan du dock behöva göra ytterligare ändringar i DNS-konfigurationen.

### <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slutpunkter
När du skapar en privat slutpunkt uppdateras DNS CNAME-posten för resursen till ett `privatelink`alias i en underdomän med prefixet . Som standard skapas en privat DNS-zon som motsvarar den privata länkens underdomän. 

När du matchar artikeln eller domänslutpunkts-URL:en utanför det virtuella nätverket med den privata slutpunkten, löss den till tjänstens offentliga slutpunkt. DNS-resursposterna för "topicA", när de matchas **utanför det virtuella nätverket** som är värd för den privata slutpunkten, kommer att vara:

| Namn                                          | Typ      | Värde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<azure traffic manager-profil\>

Du kan neka eller kontrollera åtkomst för en klient utanför det virtuella nätverket via den offentliga slutpunkten med hjälp av [IP-brandväggen](#ip-firewall). 

När problemet är att det privata slutpunkten matchas från det virtuella nätverket som är värd för den privata slutpunkten, matchas artikelns URL-url för ämnet eller domänen till den privata slutpunktens IP-adress. DNS-resursposterna för ämnet "topicA", när de matchas **inifrån det virtuella nätverket** som är värd för den privata slutpunkten, kommer att vara:

| Namn                                          | Typ      | Värde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Den här metoden ger åtkomst till ämnet eller domänen med samma anslutningssträng för klienter på det virtuella nätverket som är värd för de privata slutpunkterna och klienter utanför det virtuella nätverket.

Om du använder en anpassad DNS-server i nätverket kan klienterna matcha FQDN för ämnet eller domänslutpunkten till den privata slutpunkts-IP-adressen. Konfigurera DNS-servern så att den delegerar den privata länkunderdomänen till den `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` privata DNS-zonen för virtuella nätverk, eller konfigurera A-posterna för med den privata slutpunkts-IP-adressen.

Det rekommenderade DNS-zonnamnet är `privatelink.eventgrid.azure.net`.

### <a name="private-endpoints-and-publishing"></a>Privata slutpunkter och publicering

I följande tabell beskrivs de olika tillstånden för den privata slutpunktsanslutningen och effekterna på publicering:

| Anslutningstillstånd   |  Publicera (Ja/Nej) |
| ------------------ | -------------------------------|
| Godkända           | Ja                            |
| Avvisad           | Inga                             |
| Väntande åtgärder            | Inga                             |
| Frånkopplad       | Inga                             |

För att publiceringen ska lyckas bör det privata slutpunktsanslutningstillståndet **godkännas**. Om en anslutning avvisas kan den inte godkännas med Hjälp av Azure-portalen. Den enda möjligheten är att ta bort anslutningen och skapa en ny istället.

## <a name="pricing-and-quotas"></a>Priser och kvoter
**Privata slutpunkter** är endast tillgängliga med ämnen och domäner på premiumnivå. Med Event Grid kan upp till 64 privata slutpunktsanslutningar skapas per ämne eller domän. Information om hur du uppgraderar från grundläggande nivå till premiumnivå finns i artikeln [Uppdatera prisnivå.](update-tier.md)

**FUNKTIONEN IP-brandväggen** är tillgänglig på både grundläggande nivåer och premiumnivåer i Event Grid. Vi tillåter att upp till 16 IP-brandväggsregler skapas per ämne eller domän.


## <a name="next-steps"></a>Nästa steg
Du kan konfigurera IP-brandväggen för din Event Grid-resurs för att begränsa åtkomsten via det offentliga internet från endast en utvald uppsättning IP-adresser eller IP-adressintervall. Stegvisa instruktioner finns i [Konfigurera IP-brandväggen](configure-firewall.md).

Du kan konfigurera privata slutpunkter för att begränsa åtkomsten från endast valda virtuella nätverk. Steg-för-steg-instruktioner finns i [Konfigurera privata slutpunkter](configure-private-endpoints.md).
