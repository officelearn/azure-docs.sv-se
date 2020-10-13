---
title: Azure DDoS Protection standard översikt
description: Lär dig hur Azure DDoS Protection standard, tillsammans med bästa praxis för program design, ger skydd mot DDoS-attacker.
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
ms.openlocfilehash: 50b0324be8da8024dcbda140eff062f3ff712225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371192"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection standard översikt

Distribuerade överbelastningsattacker (DDoS) är några av de största tillgänglighets- och säkerhetsproblemen för kunder som flyttar sina program till molnet. Ett DDoS-angrepp försöker att belasta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet.

Azure DDoS Protection, kombinerat med bästa praxis för program design, ger försvar mot DDoS-attacker. Varje egenskap i Azure skyddas av Azures DDoS (grundläggande) skydd.Azure DDoS Protection standard ger ytterligare funktioner för minskning av de grundläggande tjänst nivåer som är specifika för Azure Virtual Network-resurser. 

DDoS Protection standard är enkelt att aktivera och kräver inga program ändringar. Skyddsprinciperna justeras via särskilda algoritmer för trafikövervakning och maskininlärning. Principer tillämpas på offentliga IP-adresser som är kopplade till resurser som har distribuerats i virtuella nätverk, till exempel Azure Load Balancer, Azure Application Gateway och Azure Service Fabric-instanser, men det här skyddet gäller inte för App Service miljöer.Telemetri i real tid är tillgängligt via Azure Monitor vyer under ett angrepp och för historik. Omfattande attack minsknings analyser är tillgängliga via diagnostikinställningar. Program skikts skydd kan läggas till via [brand väggen för webbaserade Azure Application Gateway](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller genom att installera en tredjeparts brand vägg från Azure Marketplace. Skydd tillhandahålls för IPv4-och IPv6 [offentliga IP-adresser](virtual-network-public-ip-address.md)i Azure.

![DDoS Protection Basic vs standard](./media/ddos-protection-overview/ddoscomparison.png)

Azure DDoS Protection lagrar inte kund information.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typer av DDoS-attacker som DDoS Protection standard åtgärder

DDoS Protection standard kan minimera följande typer av attacker:

- **Volym attacker**: de här angreppen överbelastar nätverks lagret med stor mängd som verkar vara legitim trafik. De innehåller UDP-översvämmare, förstärknings belastningar och andra falska paket belastningar. DDoS Protection standard minskar risken för angrepp med flera gigabyte genom att absorbera och rensa dem, med Azures globala nätverks skalning automatiskt.
- **Protokoll attacker**: dessa attacker återger ett mål som inte kan nås genom att utnyttja en svaghet i skikt 3-och lager 4-protokollstacken. De omfattar SYN översvämnings attacker, reflektions attacker och andra protokoll attacker. DDoS Protection standard minimerar dessa attacker, skiljer sig från skadlig och legitim trafik, genom att interagera med klienten och blockera skadlig trafik. 
- **Resurs (program) lager attacker**: dessa attacker riktar sig mot webb program paket, för att avbryta överföringen av data mellan värdar. De omfattar HTTP-protokollfel, SQL-inmatning, skript körning över flera webbplatser och andra nivå 7-attacker. Använd en brand vägg för webbaserade program, t. ex. Azure [Application Gateway brand vägg för webbaserade program](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), samt DDoS Protection standard för att ge skydd mot dessa attacker. Det finns även brand Väggs erbjudanden från tredje part som är tillgängliga på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS Protection standard skyddar resurser i ett virtuellt nätverk, inklusive offentliga IP-adresser som är kopplade till virtuella datorer, belastningsutjämnare och programgatewayer. När den är kopplad till Application Gateway brand vägg för webbaserade program eller en tredjeparts brand vägg för webbaserade program som distribueras i ett virtuellt nätverk med en offentlig IP-adress, kan DDoS Protection standard tillhandahålla fullständig nivå 3-till-nivå 7-reducering.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standard funktioner

![DDoS-funktioner](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection standard funktionerna är:

- **Inbyggd plattforms integrering:** Inbyggt i Azure. Inkluderar konfiguration via Azure Portal. DDoS Protection standard förstår dina resurser och resurs konfigurationen.
- **Nyckel färdigt skydd:** Förenklad konfiguration skyddar omedelbart alla resurser i ett virtuellt nätverk så snart DDoS Protection standard har Aktiver ATS. Ingen åtgärd eller användar definition krävs. DDoS Protection standard och minimerar risken automatiskt när den har identifierats.
- **Övervaka alltid trafik:** Dina program trafik mönster övervakas 24 timmar per dag, 7 dagar i veckan, och letar efter indikatorer för DDoS-attacker. Minskning utförs när skydds principerna överskrids.
- **Anpassningsbar justering:** Intelligent trafik profilering lär sig programmets trafik över tid och väljer och uppdaterar den profil som är lämplig för din tjänst. Profilen justeras när trafiken förändras över tid.
- **Skydd med flera skikt:** Ger fullständig stack DDoS-skydd när det används med en brand vägg för webbaserade program.
- **Omfattande minsknings skala:** Över 60 olika typer av attacker kan begränsas, med global kapacitet, för att skydda mot de största kända DDoS-attacker.
- **Attack analys:** Få detaljerade rapporter i steg om fem minuter under en attack och en fullständig sammanfattning efter att attacken har avslut ATS. Strömmande flödes loggar till [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection) eller ett SIEM-system (offline Security information and Event Management) för övervakning i nära real tid under ett angrepp.
- **Angrepps mått:** Sammanfattade mått från varje attack är tillgängliga via Azure Monitor.
- **Attack avisering:** Aviseringar kan konfigureras vid start och stopp av ett angrepp och under angriparens varaktighet, med hjälp av inbyggda angrepps mått. Aviseringar integreras i din operativa program vara, t. ex. Microsoft Azure övervaka loggar, Splunk, Azure Storage, e-post och Azure Portal.
- **Kostnads garanti:** Service krediter för data överföring och skalbarhet för program för dokumenterade DDoS-attacker.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection standard minskning

DDoS Protection standard övervakar faktisk trafik användning och jämför den kontinuerligt med de tröskelvärden som definierats i DDoS-principen. När trafik tröskelvärdet överskrids initieras DDoS-minskning automatiskt. När trafiken returneras under tröskelvärdet tas minskningen bort.

![Diagrammet visar ett flöde som börjar med en kund via Azure Portal via DDoS Protection standard, via offentliga I/o-adresser via princip generering som slutar på två offentliga I/o-adresser.](./media/ddos-protection-overview/mitigation.png)

Under minskningen omdirigeras trafik som skickas till den skyddade resursen av DDoS Protection service och flera kontroller utförs, som följande kontroller:

- Se till att paketen överensstämmer med Internet-specifikationerna och inte är felaktiga.
- Interagera med klienten för att avgöra om trafiken kan vara ett manipulerat paket (t. ex. SYN-auth eller SYN-cookie eller genom att släppa ett paket för källan för att överföra det igen).
- Hastighets begränsnings paket, om ingen annan tvingande metod kan utföras.

DDoS-skyddet blockerar attackerande trafik och vidarebefordrar återstående trafik till dess avsedda destination. Inom några minuter efter att ett angrepp har identifierats meddelas du via Azure Monitor. Genom att konfigurera inloggning DDoS Protection standard telemetri kan du skriva loggarna till tillgängliga alternativ för framtida analys. Mått data i Azure Monitor för DDoS Protection standard är kvar i 30 dagar.

Microsoft samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) för att skapa ett gränssnitt där du kan generera trafik mot DDoS Protection-aktiverade offentliga IP-adresser för simuleringar. Med moln simulerings Bryt punkt kan du:

- Verifiera hur Microsoft Azure DDoS Protection standard skyddar dina Azure-resurser från DDoS-attacker
- Optimera incident svars processen under DDoS-attack
- Dokumentera DDoS-kompatibilitet
- Träna dina nätverks säkerhets team

## <a name="next-steps"></a>Nästa steg

- [Konfigurera DDoS Protection standard](manage-ddos-protection.md)
- [Azure DDoS Protection priser](https://azure.microsoft.com/pricing/details/ddos-protection/)
