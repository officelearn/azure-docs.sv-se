---
title: "Standard översikt över Azure DDoS-skydd | Microsoft Docs"
description: "Lär dig mer om tjänsten Azure DDoS-skydd."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 6b26108b000bac56fe7d49a3a634f2be9d7543a8
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Översikt över Azure DDoS-skydd Standard

Distribuerade DOS-attacker (DDoS) är en av de största tillgänglighet och säkerhet frågor mot kunder flytta sina program till molnet. En DDoS-attacker försöker få slut på resurser för ett program, gör programmet tillgängligt för behöriga användare. DDoS-attacker kan vara mål för valfri slutpunkt som är offentligt tillgänglig via Internet.

Azure DDoS-skydd, tillsammans med programmet Metodtips för designen, ger skydd mot DDoS-attacker. DDos-skydd som Azure tillhandahåller följande tjänstnivåer: 

- **Azure DDoS-skydd grundläggande**: aktiveras automatiskt som en del av Azure-plattformen, utan extra kostnad. Finns alltid i trafik övervaknings- och realtid minskning av vanliga på nätverksnivå attacker ger samma försvar som används av Microsoft online services. Hela skalan av Azures globalt nätverk kan användas för att distribuera och minska riskerna för angrepp trafik över regioner. Skydd tillhandahålls för IPv4 och IPv6-Azure [offentliga IP-adresser](virtual-network-public-ip-address.md).
- **Azure DDoS-skydd Standard** ger ytterligare minskning funktioner inställda specifikt för Azure Virtual Network-resurser. Det är enkelt att aktivera, och kräver inga ändringar i programmet. Skyddsprinciper justerade via dedikerade övervaknings- och machine learning algoritmer och tillämpas på den offentliga IP-adresser som är kopplade till resurser som är distribuerad i virtuella nätverk, till exempel Azure belastningsutjämnare, Azure Application Gateway och Azure Service Fabric-instanser. Realtid telemetri är tillgängliga via Azure-Monitor vyer under en attack och för historik. Programmet skydd kan läggas till via [programmet Gateway Brandvägg för webbaserade program](https://azure.microsoft.com/services/application-gateway). Skydd tillhandahålls för IPv4 Azure [offentliga IP-adresser](virtual-network-public-ip-address.md). 

![Standard för Azure DDoS-skydd](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

> [!IMPORTANT]
> Azure DDoS-skydd Standard är för närvarande under förhandsgranskning. Skydd har angetts för Azure-resurser som har en Azure offentliga IP-adress som är kopplade till den, till exempel virtuella datorer, belastningsutjämnare och programgatewayer. Du behöver [registrera](http://aka.ms/ddosprotection) för tjänsten innan du kan aktivera DDoS-skydd Standard för din prenumeration. Efter registrering Azure DDoS team kontakter du och guider dig genom aktivering. DDoS-skydd Standard finns i östra USA, östra USA 2, västra USA, västra centrala USA, Nordeuropa, Västeuropa, västra Japan, östra, Östasien och Sydostasien regioner. Under förhandsgranskningen gör debiteras inte du för att använda tjänsten.

Vi rekommenderar att du försöker DDoS-skydd Standard i utvecklings-, test- eller produktion miljöer. Använd följande resurser för att ge feedback om din upplevelse:
- [Azure DDoS-skydd på Microsoft Azure-forumet](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Azure DDoS-skydd på MSDN-Forum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Azure DDos-skydd på Stack Overflow](https://stackoverflow.com/tags/azure-ddos/info)

För supportfrågor, kan du [skapar ett Azure supportärende](../azure-supportability/how-to-create-azure-support-request.md). DDoS-skydd Standard är i förhandsvisning, tillhandahålls support för bästa prestanda.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typer av DDoS-attacker som DDoS-skydd Standard minskar

DDoS-skydd Standard kan minimera dessa typer av angrepp:

- **Överför attacker**: den attack målet är att översvämma nätverksnivån med en stor mängd till synes legitima trafik. Den omfattar UDP översvämningar förstärkning översvämningar och andra falska paket översvämningar. DDoS-skydd Standard minskar potentiella flera gigabyte angrepp genom absorbera och skrubbningsjobb dem, som utnyttja Azures globalt nätverk skala automatiskt. 
- **Protokollet attacker**: angreppen återge ett mål otillgänglig genom att utnyttja en svaghet i nivå 3 och lager 4 protokoll-stacken. Den omfattar SYN översvämning attacker reflektionsattacker och andra protokoll-attacker. DDoS-skydd Standard minskar dessa attacker skilja skadliga och legitima trafik genom att interagera med klienten och blockera skadlig trafik. 
- **Application layer attacker**: angrepp riktade web application-paket för att störa överföring av data mellan värdar. Den innehåller HTTP-protokollet överträdelser, SQL injection, globala webbplatsskript och andra layer 7-attacker. Använd Azure [Programgateway Brandvägg för webbaserade program](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), med DDoS-skydd Standard, att ge skydd mot angrepp. 

DDoS-skydd Standard skyddar resurser i ett virtuellt nätverk, inklusive offentliga IP-adresser som är kopplade till virtuella datorer, belastningsutjämnare och programgatewayer. När tillsammans med Brandvägg för webbaserade program i Programgateway ge DDoS-skydd Standard fullständig layer 3 layer 7 kapaciteten för migrering.

## <a name="ddos-protection-standard-features"></a>Standardfunktioner för DDoS-skydd

![DDoS-funktioner](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS-skydd Standardfunktionerna innefattar: 

- **Inbyggd plattformsintegrering:** internt integrerat i Azure och innehåller konfigurationen med hjälp av Azure portal och PowerShell. DDoS-skydd Standard förstår dina resurser och resurskonfigurationen.
- **Övervakning av alltid i nätverkstrafik:** trafikmönster ditt program övervakas 24-timmarsformat per dag, 7 dagar i veckan söker efter indikatorer för DDoS-attacker. Minskning utförs när skyddsprinciper överskrids.
- **Nyckelfärdig skydd:** förenklad konfigurationen omedelbart skyddar alla resurser i ett virtuellt nätverk som Standard till DDoS-skydd är aktiverat. Det krävs inga åtgärder eller användaren definition. DDoS-skydd Standard minskar omedelbart och automatiskt angrepp, när den har identifierats.
- **Anpassningsbar justera:** Intelligent trafik profilering lär sig programmets trafik över tid, och väljer och uppdaterar den profil som är mest lämpliga för din tjänst. Profilen justerar när trafik ändras med tiden.
- **Nivå 3 till nivå 7 skydd:** ger fullständig stack DDoS-skydd när det används med en Programgateway.
- **Omfattande lösning skala:** över 60 olika attack typer kan begränsas med globala kapacitet att skydda mot största kända DDoS-attacker. 
- **Angrepp mått:** Summarized från varje attacker är tillgängligt via Azure-Monitor.
- **Attack aviseringar:** aviseringar kan konfigureras på start och stopp av ett angrepp och med hjälp av inbyggda attack mätvärden via den attack varaktighet. Aviseringar integrera din operativa program som Microsoft Operations Management Suite, Splunk, Azure Storage, e-post och Azure-portalen.
- **Kostnad garanti:** överföra Data och program skalbar servicekrediter för dokumenterade DDoS-attacker.

## <a name="ddos-protection-standard-mitigation"></a>DDoS-skydd Standard lösning

Microsofts DDoS-skydd-tjänsten övervakar faktiska trafik användning och ständigt jämför den med de tröskelvärden som anges i DDoS-principen. När trafiken gränsen överskrids, initieras DDoS minskning automatiskt. När trafiken returnerar under tröskelvärdet, tas minskning bort.

Vid migrering av trafik som skickas till den skyddade resursen omdirigeras av tjänsten DDoS-skydd och flera kontroller utförs. De här kontrollerna vanligtvis utför följande funktioner:

- Se till att paket som överensstämmer med Internet-specifikationer och inte är felaktig.
- Interagera med klienten för att avgöra om trafiken är potentiellt falska paket (t.ex.: SYN Auth eller SYN Cookie eller genom att släppa ett paket att skicka den källa).
- Gräns för överföringshastigheten paket, om ingen annan metod för tvingande kan utföras.

DDoS-skydd block angrepp trafik och vidarebefordrar återstående trafiken till sin destination. Inom några minuter för angreppsidentifiering visas ett meddelande med hjälp av Azure-Monitor mätvärden. Genom att konfigurera loggning på DDoS-skydd Standard telemetri kan skriva du loggarna till alternativen för framtida analys. Mätvärden i Azure-Monitor för DDoS-skydd Standard behålls för närvarande i 30 dagar.

Vi meddela inte kunder att simulera sina egna DDoS-attacker. I stället kunder kan använda support kanalen för att begära en DDoS angrepp simuleringen körs av Azure-nätverk. En ingenjör att kontakta dig för att ordna information för DDoS-attacker (portar, protokoll, mål-IP-adresser) och boka en tid att schemalägga testet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du hanterar DDoS-skydd Standard med hjälp av [Azure PowerShell](ddos-protection-manage-ps.md) eller [Azure-portalen](ddos-protection-manage-portal.md).
