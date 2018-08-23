---
title: Standard Azure DDoS Protection-översikt | Microsoft Docs
description: Läs mer om Azure DDoS Protection-tjänsten.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: 95fc257485aeea396185089b2b73a6e73ee2bfee
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42056957"
---
# <a name="azure-ddos-protection-standard-overview"></a>Översikt över Azure DDoS Protection Standard

Distribuerade attacker denial of service (DDoS) är några av de största tillgänglighet och säkerhet frågor mot kunder som migrerar sina program till molnet. DDoS-attacker försöker att få slut på ett programs resurser, som gör programmet tillgängligt för behöriga användare. DDoS-attacker kan riktas till valfri slutpunkt som kan nås offentligt via internet.

Azure DDoS protection, tillsammans med programmet Metodtips för design, ger skydd mot DDoS-attacker. Azure DDoS protection får du med följande tjänstnivåer:

- **Grundläggande**: aktiveras automatiskt som en del av Azure-plattformen, utan extra kostnad. Ständigt aktiverad övervakning och i realtid minskning av vanliga attacker på nätverksnivå, ger samma försvar som används av Microsoft online services. Hela skalan för Azures globala nätverket kan användas för att distribuera och minimera attack trafik mellan regioner. Skydd tillhandahålls för IPv4 och IPv6 Azure [offentliga IP-adresser](virtual-network-public-ip-address.md).
- **Standard**: ger ytterligare skyddsfunktioner över grundläggande tjänstenivå som är finjusterade specifikt för Azure Virtual Network-resurser. DDoS Protection Standard är enkelt att använda, och kräver inga ändringar i programmet. Appskyddsprinciper är justerade via dedikerade övervaknings- och machine learning-algoritmer. Principer som används för den offentliga IP-adresser som är kopplade till resurser som har distribuerats i virtuella nätverk, till exempel Azure Load Balancer Azure Application Gateway och Azure Service Fabric-instanser. I realtid telemetri är tillgängligt via Azure Monitor vyer under ett angrepp, och för historik. Skydd på programnivå kan läggas till via den [Azure Application Gateway Web Application Firewall](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Skydd tillhandahålls för IPv4 Azure [offentliga IP-adresser](virtual-network-public-ip-address.md).

![Azure DDoS Protection Standard](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typer av DDoS-attacker som DDoS Protection Standard minskar

DDoS Protection Standard kan minimera följande typer av attacker:

- **Överför attacker**: den attack målet är att översvämma nätverksnivå med en betydande mängd till synes legitima trafik. Den innehåller UDP översvämningar, förstärkning översvämning och andra falska paket översvämningar. DDoS Protection Standard minskar risken för potentiella flera gigabyte angrepp genom att absorbera och Skrubba dem, med Azures globala nätverk skala automatiskt.
- **Protokoll-attacker**: dessa attacker, kanske ett mål användas, genom att utnyttja en svaghet i layer 3 och layer 4-protokollstacken. Den innehåller, SYN-översvämning attacker, reflektionsattacker och andra protokoll-attacker. DDoS Protection Standard minskar risken för sådana attacker så skilja mellan skadlig och legitima trafik genom att interagera med klienten och blockera skadlig trafik. 
- **Resurs (program) attacker på programnivå**: dessa attacker rikta web application paket, för att störa överföring av data mellan värdar. Attackerna omfattar HTTP protokollbrott SQL injection, cross site scripting och andra layer 7-attacker. Använd Azure [Application Gateway waf](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), med DDoS Protection Standard, att ge skydd mot angrepp. Det finns även andra leverantörers webb-programmet brandväggen erbjudanden tillgängliga i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS Protection Standard skyddar resurser i ett virtuellt nätverk, inklusive den offentliga IP-adresser som är associerade med virtuella datorer, belastningsutjämnare och programgatewayer. När de kombineras med brandväggen för webbaserade program för Application Gateway, kan DDoS Protection Standard ger fullständig lager 3 till layer 7 kapaciteten för problemlösning.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standardfunktioner

![DDoS-funktioner](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection standardfunktioner omfattar:

- **Inbyggd plattformsintegrering:** internt integrerade i Azure. Innehåller konfiguration via Azure portal. DDoS Protection Standard förstår dina resurser och resurskonfigurationer.
- **NYCKELFÄRDIGT skydd:** förenklad konfiguration skyddar direkt alla resurser i ett virtuellt nätverk när DDoS Protection Standard är aktiverat. Det krävs inga åtgärder eller användaren definition. DDoS Protection Standard attacken omedelbart och automatiskt, när den identifieras.
- **Ständigt aktiverad övervakning:** din mönster i programtrafiken övervakas 24 timmar per dag, 7 dagar i veckan letar du efter indikatorer för DDoS-attacker. Minskning utförs när appskyddsprinciper överskrids.
- **Anpassningsbar justering:** Intelligent trafik profilering lär sig programmets trafik över tid, och väljer och uppdaterar den profil som är mest lämplig för din tjänst. Profilen justerar allt trafik ändras med tiden.
- **Nivå 3 till nivå 7-skydd:** ger fullständig stack DDoS-skydd, när det används med en brandvägg för webbaserade program.
- **Omfattande minskning skala:** över 60 olika angreppstyper kan undvikas med globala kapacitet att skydda mot största kända DDoS-attacker.
- **Angrepp mått:** Summarized mått från varje attack är tillgängliga via Azure Monitor.
- **Attack avisering:** aviseringar kan konfigureras vid start och stopp av ett angrepp, och med hjälp av inbyggda attack mätvärden över den attack varaktighet. Aviseringar integrera i din operational programvara som Microsoft Azure Log Analytics, Splunk, Azure Storage, e-post och Azure-portalen.
- **Kostnad garanti:** överföra Data och program skalbar tjänstkrediter för dokumenterad DDoS-attacker.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection Standard minskning

DDoS Protection Standard övervakar faktiska trafik användning och ständigt jämför den med de tröskelvärden som definierats i DDoS-princip. När trafik tröskelvärdet överskrids initieras automatiskt DDoS-åtgärd. När trafik lämnar under tröskelvärdet, tas minskningen bort.

![Åtgärd](./media/ddos-protection-overview/mitigation.png)

Under migrering av trafik som skickas till den skyddade resursen omdirigeras av tjänsten DDoS protection och flera kontroller som utförs, till exempel följande kontroller:

- Se till att paket som motsvarar internet specifikationer och inte är felaktig.
- Interagera med klienten för att fastställa om trafiken potentiellt är en falsk paket (t.ex.: SYN Auth eller SYN Cookie eller genom att släppa ett paket för källan för att snabbåterställningen det).
- Gräns för överföringshastigheten paket, om ingen annan metod för tvingande kan utföras.

DDoS protection blockerar attack trafik och vidarebefordrar återstående trafiken till sin destination. Inom några minuter för identifiering av attack du får ett meddelande med hjälp av mätvärden för Azure Monitor. Genom att konfigurera loggning på DDoS Protection standardmoduler, kan du skriva loggarna till alternativen för framtida analys. Mått bevaras i Azure Monitor för DDoS Protection Standard i 30 dagar.

Microsoft samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) att skapa ett gränssnitt där du kan generera trafik mot DDoS Protection-aktiverade offentliga IP-adresser för simulering. Simuleringen brytpunkt molnet kan du:

- Kontrollera hur Microsoft Azure DDoS Protection Standard skyddar dina Azure-resurser mot DDoS-attacker
- Optimera din incidenthanteringsprocess medan under DDoS-attack
- Dokumentera DDoS-efterlevnad
- Träna dina network security team

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Standard för DDoS-skydd](manage-ddos-protection.md)
