---
title: Översikt över Standardstandard för Azure DDoS-skydd
description: Läs mer om Azure DDoS Protection-tjänsten.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: f1dd33425a57689974fc98a28724adf7b130ab40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536351"
---
# <a name="azure-ddos-protection-standard-overview"></a>Översikt över Azure DDoS Protection Standard

Distribuerade överbelastningsattacker (DDoS) är några av de största tillgänglighets- och säkerhetsproblemen för kunder som flyttar sina program till molnet. En DDoS-attack försöker uttömma ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet.

Azure DDoS-skydd, kombinerat med metodtips för programdesign, ger skydd mot DDoS-attacker. Azure DDoS-skydd ger följande tjänstnivåer:

- **Grundläggande**: Aktiveras automatiskt som en del av Azure-plattformen. Trafikövervakning som alltid är på trafik och begränsning i realtid av vanliga attacker på nätverksnivå ger samma försvar som används av Microsofts onlinetjänster.Hela skalan av Azures globala nätverk kan användas för att distribuera och minska attacktrafik mellan regioner.Skydd ges för offentliga [IP-adresser](virtual-network-public-ip-address.md)för IPv4 och IPv6 Azure .
- **Standard**: Ger ytterligare begränsningsfunktioner över den grundläggande tjänstnivån som är specifikt inställda på Azure Virtual Network-resurser. DDoS Protection Standard är enkel att aktivera och kräver inga programändringar. Skyddsprinciperna justeras via särskilda övervaknings- och maskininlärningsalgoritmer. Principer tillämpas på offentliga IP-adresser som är associerade till resurser som distribueras i virtuella nätverk, till exempel Azure Load Balancer, Azure Application Gateway och Azure Service Fabric-instanser, men det här skyddet gäller inte för App Service-miljöer.Telemetri i realtid är tillgängligt via Azure Monitor-vyer under en attack och för historik. Omfattande analys av attackreducering är tillgängliga via diagnostikinställningar. Programlagerskydd kan läggas till via [Azure Application Gateway Web Application Firewall](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller genom att installera en brandvägg från tredje part från Azure Marketplace. Skydd ges för offentliga [IP-adresser](virtual-network-public-ip-address.md)för IPv4 och IPv6 Azure .

|Funktion                                         |Grundläggande DDoS-skydd                 |DDoS-skyddsstandard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Aktiv trafikövervakning & alltid vid identifiering |Ja                                   |Ja                                           |
|Automatiska attackbegränsningar                    |Ja                                   |Ja                                           |
|Tillgänglighetsgaranti                          |Azure-region                          |Program                                   |
|Begränsningsprinciper                             |Justerad för Azure-trafikregionvolym |Inställd för programtrafikvolym          |
|Mått & aviseringar                                |Inga                                    |Angreppsmått i realtid & diagnostikloggar via Azure-övervakare                                 |
|Begränsningsrapporter                              |Inga                                    |Rapporter om begränsning av angrepp                |
|Begränsning av flödesloggar                            |Inga                                    |NRT-loggström för SIEM-integrering           |
|Anpassning av begränsningsprincip                 |Inga                                    |Engagera DDoS-experter                           |
|Support                                         |Bästa insats                           |Tillgång till DDoS-experter under en aktiv attack|
|SLA                                             |Azure-region                          |Ansökan garanti & kostnadsskydd       |
|Prissättning                                         |Kostnadsfri                                  |Månatlig användning & användning baserat                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typer av DDoS-attacker som DDoS Protection Standard mildrar

DDoS Protection Standard kan minska följande typer av attacker:

- **Volymetriska attacker**: Attackens mål är att översvämma nätverksskiktet med en betydande mängd till synes legitim trafik. Det omfattar UDP översvämningar, förstärkning översvämningar, och andra falska paket översvämningar. DDoS Protection Standard minskar dessa potentiella attacker med flera gigabyte genom att absorbera och skrubba dem, med Azures globala nätverksskala, automatiskt.
- **Protokollattacker**: Dessa attacker gör ett mål otillgängligt genom att utnyttja en svaghet i protokollstacken för lager 3 och lager 4. Den innehåller, SYN översvämning attacker, reflektion attacker och andra protokoll attacker. DDoS Protection Standard minskar dessa attacker, skiljer mellan skadlig och legitim trafik, genom att interagera med klienten och blockera skadlig trafik. 
- **Resurs (program) lager attacker:** Dessa attacker mål webbprogram paket, för att störa överföringen av data mellan värdar. Attackerna omfattar HTTP-protokollöverträdelser, SQL-injektion, skript över flera webbplatser och andra layer 7-attacker. Använd en brandvägg för webbprogram, till exempel brandväggen för Azure [Application Gateway-webbprogram,](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)samt DDoS Protection Standard för att skydda mot dessa attacker. Det finns också brandväggar för webbprogram från tredje part som är tillgängliga på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS Protection Standard skyddar resurser i ett virtuellt nätverk, inklusive offentliga IP-adresser som är associerade med virtuella datorer, belastningsutjämnare och programgateways. I kombination med webbprogrambrandväggen för Programgateway eller en brandvägg för webbprogram från tredje part som distribueras i ett virtuellt nätverk med en offentlig IP, kan DDoS Protection Standard tillhandahålla full layer 3 till layer 7 mitigation capability.

## <a name="ddos-protection-standard-features"></a>Funktioner i DDoS Protection Standard

![DDoS-funktioner](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection Standard funktioner inkluderar:

- **Integrering av inbyggd plattform:** Inbyggt integrerat i Azure. Inkluderar konfiguration via Azure-portalen. DDoS Protection Standard förstår dina resurser och resurskonfiguration.
- **Nyckelskydd:** Förenklad konfiguration skyddar omedelbart alla resurser i ett virtuellt nätverk så snart DDoS Protection Standard är aktiverad. Ingen åtgärd eller användardefinition krävs. DDoS Protection Standard omedelbart och automatiskt mildrar attacken, när den upptäcks.
- **Trafikövervakning alltid:** Ditt program trafikmönster övervakas 24 timmar om dygnet, 7 dagar i veckan, letar efter indikatorer på DDoS-attacker. Begränsning utförs när skyddsprinciper överskrids.
- **Adaptiv justering:** Intelligent trafikprofilering lär sig programmets trafik över tid och väljer och uppdaterar den profil som passar bäst för din tjänst. Profilen justeras när trafiken ändras över tid.
- **Skydd i flera lager:** Ger fullständig stack DDoS-skydd när det används med en brandvägg för webbprogram.
- **Omfattande begränsningsskala:** Över 60 olika attacktyper kan mildras, med global kapacitet, för att skydda mot de största kända DDoS-attackerna.
- **Attack analys:** Få detaljerade rapporter i fem minuters steg under en attack och en fullständig sammanfattning efter att attacken har avslutats. Stream mitigation flow logs till en offline säkerhetsinformation och händelsehantering (SIEM) system för nära realtid övervakning under en attack.
- **Attack mått:** Sammanfattade mått från varje attack är tillgängliga via Azure Monitor.
- **Varning för angrepp:** Aviseringar kan konfigureras i början och stoppet av en attack och under attackens varaktighet med hjälp av inbyggda attackmått. Aviseringar integreras i din operativa programvara som Microsoft Azure Monitor-loggar, Splunk, Azure Storage, E-post och Azure-portalen.
- **Kostnadsgaranti:** Dataöverföring och utskalningstjänstkrediter för dokumenterade DDoS-attacker.

## <a name="ddos-protection-standard-mitigation"></a>DDoS-skyddsstandard begränsning

DDoS Protection Standard övervakar det faktiska trafikutnyttjandet och jämför det ständigt med de tröskelvärden som definieras i DDoS-principen. När trafiktröskeln överskrids initieras DDoS-begränsning automatiskt. När trafiken återgår under tröskelvärdet tas begränsningen bort.

![Åtgärd](./media/ddos-protection-overview/mitigation.png)

Under begränsningen omdirigeras trafik som skickas till den skyddade resursen av DDoS-skyddstjänsten och flera kontroller utförs, till exempel följande kontroller:

- Se till att paketen överensstämmer med internetspecifikationerna och inte är felformade.
- Interagera med klienten för att avgöra om trafiken är ett förfalskat paket (t.ex.
- Hastighetsbegränsningspaket, om ingen annan tvingande metod kan utföras.

DDoS-skyddet blockerar attackerande trafik och vidarebefordrar återstående trafik till dess avsedda destination. Inom några minuter efter att ett angrepp har identifierats meddelas du via Azure Monitor. Genom att konfigurera loggning på DDoS Protection Standard telemetri, kan du skriva loggarna till tillgängliga alternativ för framtida analys. Måttdata i Azure Monitor för DDoS Protection Standard sparas i 30 dagar.

Microsoft samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) för att skapa ett gränssnitt där du kan generera trafik mot DDoS Protection-aktiverade offentliga IP-adresser för simuleringar. Med BreakPoint Cloud-simuleringen kan du:

- Verifiera hur Microsoft Azure DDoS Protection Standard skyddar dina Azure-resurser från DDoS-attacker
- Optimera din incidentsvarsprocess under DDoS-attack
- Dokument DDoS-efterlevnad
- Träna dina nätverkssäkerhetsteam

## <a name="next-steps"></a>Nästa steg

- [Konfigurera DDoS Protection Standard](manage-ddos-protection.md)
