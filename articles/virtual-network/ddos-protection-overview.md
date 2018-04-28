---
title: Standard översikt över Azure DDoS-skydd | Microsoft Docs
description: Lär dig mer om tjänsten Azure DDoS-skydd.
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
ms.openlocfilehash: 705f69f9143e3d2b27a3099f340218aaa12931f8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="azure-ddos-protection-standard-overview"></a>Översikt över Azure DDoS-skydd Standard

Distribuerade DOS-attacker (DDoS) är några av de största tillgänglighet och säkerhet frågor mot kunder som vill flytta sina program till molnet. En DDoS-attacker försöker få slut på resurser för ett program, gör programmet tillgängligt för behöriga användare. DDoS-attacker kan vara mål för valfri slutpunkt som är offentligt tillgänglig via internet.

Azure DDoS-skydd, tillsammans med programmet Metodtips för designen, ger skydd mot DDoS-attacker. DDos-skydd som Azure tillhandahåller följande tjänstnivåer:

- **Grundläggande**: aktiveras automatiskt som en del av Azure-plattformen, utan extra kostnad. Övervakning av nätverkstrafik som alltid är på och realtid minskning av vanliga attacker på nätverksnivå, ger samma försvar som används av Microsoft online services. Hela skalan av Azures globalt nätverk kan användas för att distribuera och minska riskerna för angrepp trafik över regioner. Skydd tillhandahålls för IPv4 och IPv6-Azure [offentliga IP-adresser](virtual-network-public-ip-address.md).
- **Standard**: ger ytterligare minskning funktioner över grundläggande tjänstnivån som är inställda specifikt för Azure Virtual Network-resurser. DDoS-skydd Standard är enkel att aktivera, och kräver inga ändringar i programmet. Skyddsprinciper justerade via dedikerade övervaknings- och maskininlärningsalgoritmer. Principer som tillämpas på den offentliga IP-adresser som är kopplade till resurser som är distribuerad i virtuella nätverk, till exempel Azure belastningsutjämnare och Azure Programgateway Azure Service Fabric-instanser. Realtid telemetri är tillgängliga via Azure-Monitor vyer under en attack och för historik. Layer Programskydd kan läggas till via den [Brandvägg för Azure Application Gateway webbaserade program](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Skydd tillhandahålls för IPv4 Azure [offentliga IP-adresser](virtual-network-public-ip-address.md).

![Standard för Azure DDoS-skydd](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typer av DDoS-attacker som DDoS-skydd Standard minskar

DDoS-skydd Standard kan minimera följande typer av attacker:

- **Överför attacker**: den attack målet är att översvämma nätverksnivån med en stor mängd till synes legitima trafik. Den omfattar UDP översvämningar förstärkning översvämningar och andra falska paket översvämningar. DDoS-skydd Standard minskar potentiella flera gigabyte angrepp genom absorbera och skrubbningsjobb med Azures globalt nätverk skala dem automatiskt.
- **Protokollet attacker**: angreppen återge ett mål oåtkomliga, genom att utnyttja en svaghet i nivå 3 och lager 4 protokoll-stacken. Den omfattar SYN översvämning attacker reflektionsattacker och andra protokoll-attacker. DDoS-skydd Standard minskar dessa attacker skilja skadliga och legitima trafik genom att interagera med klienten och blockera skadlig trafik. 
- **Resurs (program) layer attacker**: angrepp riktade web application paket för att störa överföring av data mellan värdar. Attackerna som är HTTP-protokollet överträdelser, SQL injection, globala webbplatsskript och andra layer 7-attacker. Använd Azure [Programgateway Brandvägg för webbaserade program](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), med DDoS-skydd Standard, att ge skydd mot angrepp. Det finns även tredje parts web application brandväggen erbjudanden i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS-skydd Standard skyddar resurser i ett virtuellt nätverk, inklusive offentliga IP-adresser som är kopplade till virtuella datorer, belastningsutjämnare och programgatewayer. När tillsammans med Brandvägg för webbaserade program i Programgateway ge DDoS-skydd Standard fullständig layer 3 layer 7 kapaciteten för migrering.

## <a name="ddos-protection-standard-features"></a>Standardfunktioner för DDoS-skydd

![DDoS-funktioner](./media/ddos-protection-overview/ddosfeatures.png)

DDoS-skydd Standardfunktionerna innefattar:

- **Inbyggd plattformsintegrering:** internt integrerat i Azure. Inkluderar konfiguration via Azure-portalen. DDoS-skydd Standard förstår dina resurser och resurskonfigurationen.
- **Nyckelfärdig skydd:** förenklad konfigurationen omedelbart skyddar alla resurser i ett virtuellt nätverk som Standard till DDoS-skydd är aktiverat. Det krävs inga åtgärder eller användaren definition. DDoS-skydd Standard minskar omedelbart och automatiskt angrepp, när den har identifierats.
- **Övervakning av alltid i nätverkstrafik:** trafikmönster ditt program övervakas 24-timmarsformat per dag, 7 dagar i veckan söker efter indikatorer för DDoS-attacker. Minskning utförs när skyddsprinciper överskrids.
- **Anpassningsbar justera:** Intelligent trafik profilering lär sig programmets trafik över tid, och väljer och uppdaterar den profil som är mest lämpliga för din tjänst. Profilen justerar när trafik ändras med tiden.
- **Nivå 3 till nivå 7 skydd:** ger fullständig stack DDoS-skydd när det används med en brandvägg för webbaserade program.
- **Omfattande lösning skala:** över 60 olika attack typer kan begränsas med globala kapacitet att skydda mot största kända DDoS-attacker.
- **Angrepp mått:** Summarized från varje attacker är tillgängligt via Azure-Monitor.
- **Attack aviseringar:** aviseringar kan konfigureras på start och stopp av ett angrepp och med hjälp av inbyggda attack mätvärden via den attack varaktighet. Aviseringar integrera din operativa program som Microsoft Azure logganalys, Splunk, Azure Storage, e-post och Azure-portalen.
- **Kostnad garanti:** överföra Data och program skalbar servicekrediter för dokumenterade DDoS-attacker.

## <a name="ddos-protection-standard-mitigation"></a>DDoS-skydd Standard lösning

DDoS-skydd Standard övervakar faktiska trafik användning och ständigt jämför den med de tröskelvärden som anges i DDoS-principen. När trafiken tröskelvärdet överskrids initieras DDoS minskning automatiskt. När trafiken returnerar under tröskelvärdet, tas minskning bort.

![Åtgärd](./media/ddos-protection-overview/mitigation.png)

Under migrering av trafik som skickas till den skyddade resursen omdirigeras av tjänsten DDoS-skydd och flera kontroller utförs, till exempel följande kontroller:

- Se till att paket som överensstämmer med internet-specifikationer och inte är felaktig.
- Interagera med klienten för att avgöra om trafiken är potentiellt falska paket (t.ex.: SYN Auth eller SYN Cookie eller genom att släppa ett paket att skicka den källa).
- Gräns för överföringshastigheten paket, om ingen annan metod för tvingande kan utföras.

DDoS-skydd blockerar attack trafik och vidarebefordrar återstående trafiken till sin destination. Inom några minuter för angreppsidentifiering visas ett meddelande med hjälp av Azure-Monitor mätvärden. Genom att konfigurera loggning på DDoS-skydd Standard telemetri kan skriva du loggarna till alternativen för framtida analys. Mätvärden i Azure-Monitor för DDoS-skydd Standard behålls i 30 dagar.

Microsoft samarbetar med [BreakingPoint moln](https://www.ixiacom.com/products/breakingpoint-cloud) att skapa ett gränssnitt där du kan skapa trafik mot DDoS-skydd-aktiverade offentliga IP-adresser för simulering. Simuleringen brytpunkt molnet kan du:

- Kontrollera hur Microsoft Azure DDoS-skydd Standard skyddar Azure-resurser från DDoS-attacker
- Optimera incidenter processen under tiden för DDoS-attack
- Dokumentet DDoS-kompatibilitet
- Träna din network security team

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Standard för DDoS-skydd](manage-ddos-protection.md)