---
title: "Standard översikt över Azure DDoS-skydd | Microsoft Docs"
description: "Lär dig mer om tjänsten Azure DDoS-skydd."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 76da0d4e805c732d40a7bd02e5c70973c792e26c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Översikt över Azure DDoS-skydd Standard

>[!IMPORTANT]
>Azure DDoS-skydd Standard är för närvarande under förhandsgranskning. Ett begränsat antal Azure-resurser stöd DDoS-skydd Standard och i ett begränsat antal regioner. Du behöver [registrera dig för tjänsten](http://aka.ms/ddosprotection) under begränsad förhandsgranskningen att hämta DDoS-skydd Standard aktiverats för din prenumeration. Du kontaktas av Azure DDoS-teamet vid registreringen och vägleder dig genom processen för aktivering. DDoS-skydd Standard finns i oss Öst oss Väst och West centrala oss regioner. Under förhandsgranskningen gör debiteras inte du för att använda tjänsten.

Distribuerade Denial of Service (DDoS)-attacker är en av de största tillgängligheten och säkerhet avser Internetriktade kunder flytta sina program till molnet. En DDoS-attacker försöker få slut på resurser för ett program gör programmet tillgängligt för behöriga användare. DDoS-attacker kan vara mål för valfri slutpunkt som är offentligt tillgänglig via Internet.

Azure DDoS-skydd kombinerat med bästa praxis för programmet design tillsammans ger skydd mot angrepp. Dessa två tjänstnivåer tillhandahålls: 

- **Azure DDoS-skydd grundläggande** -redan automatiskt är aktiverad som en del av Azure-plattformen utan extra kostnad. Finns alltid i trafik övervaknings- och realtid minskning av vanliga på nätverksnivå innehåller samma försvar som används av Microsoft online services.  Hela skalan av Azures globalt nätverk kan användas för att distribuera och minska riskerna för angrepp trafik över regioner. 
- **Azure DDoS-skydd Standard** -ger ytterligare minskning funktioner inställda specifikt för nätverksresurser på virtuella datorer. Det är enkelt att aktivera och kräver inga ändringar i programmet. Skyddsprinciper justerade via dedikerade övervaknings- och machine learning algoritmer och tillämpas på offentliga IP-adresser som är kopplade till virtuella nätverksresurser, till exempel belastningsutjämnare, Programgateway och Service Fabric-instanser.  Realtid telemetri är tillgängliga via Azure-Monitor vyer under en attack och för historik. Programmet skydd kan läggas till via [programmet Gateway Brandvägg för webbaserade program](https://azure.microsoft.com/services/application-gateway/). 

![Standard för Azure DDoS-skydd](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

Vi rekommenderar att du försöker DDoS-skydd Standard i utvecklings-, test- eller produktion miljöer. Använd följande resurser för att ge feedback om din upplevelse:
- [Azure DDoS-skydd på Microsoft Azure-forumet](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Azure DDoS-skydd på MSDN-Forum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Azure DDos-skydd på Stack Overflow](https://stackoverflow.com/tags/azure-ddos/info)

För supportfrågor, kan du [skapar ett Azure supportärende](../azure-supportability/how-to-create-azure-support-request.md). DDoS-skydd Standard är i förhandsvisning, tillhandahålls support för bästa prestanda.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Typer av DDoS-attacker som DDoS-skydd Standard minskar

DDoS-skydd Standard kan minimera dessa typer av angrepp:

- **Överför attacker** -den attack målet är att översvämma nätverksnivån med stor mängd till synes legitima trafik. Det innehåller UDP översvämningar, förstärkning översvämningar och andra falska paket översvämningar. DDoS-skydd Standard minskar potentiella flera gigabyte angrepp genom absorbera & skrubbningsjobb dem utnyttja Azures globalt nätverk skala automatiskt. 
- **Protokollet attacker** -angrepp återge ett mål otillgänglig genom att utnyttja en svaghet i protokollstacken Layer 3 och 4 för lager. Den omfattar SYN översvämning attacker reflektionsattacker och andra protokoll-attacker. DDoS-skydd Standard minskar angreppen skilja skadliga och legitima trafik genom att interagera med klienten och blockera skadlig trafik. 
- **Application layer attacker** -angrepp riktade web application-paket för att störa överföring av data mellan värdar. Den innehåller överträdelser av HTTP-protokollet, SQL injection, globala webbplatsskript och andra layer 7-attacker. Med programmet Gateway Brandvägg DDoS-skydd Standard för att ge skydd mot angrepp. 

DDoS-skydd Standard skyddar resurser i ett virtuellt nätverk, inklusive offentliga IP-adresser som är kopplade till virtuella datorer, interna belastningsutjämnare och programgatewayer. När tillsammans med programmet Gateway Brandvägg SKU ge DDoS-skydd Standard fullständig L3 till L7 minskning kapaciteten.

## <a name="ddos-protection-standard-features"></a>Standardfunktioner för DDoS-skydd

![DDoS-funktioner](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS-skydd Standardfunktionerna innefattar: 

- **Inbyggd plattformsintegrering:** DDoS-skydd Standard är inbyggt i Azure och innehåller konfigurationen med hjälp av Azure portal och PowerShell. DDoS-skydd Standard förstår dina resurser och resurskonfigurationen.
- **Övervakning av alltid i nätverkstrafik:** trafikmönster ditt program är övervakade 24 x 7, söker efter indikatorer för DDoS-attacker. Minskning utförs när skyddsprinciper överskrids.
- **Nyckelfärdig skydd:** förenklad konfigurationen omedelbart skyddar alla resurser i ett virtuellt nätverk som Standard till DDoS-skydd är aktiverat. Inga åtgärder eller användaren krävs - DDoS-skydd Standard omedelbart och automatiskt minskar angrepp när den har identifierats.
- **Anpassningsbar justera:** Intelligent trafik profilering lär sig programmets trafik över tid, och väljer och uppdaterar den profil som är mest lämpliga för din tjänst. Profilen justerar när trafik ändras med tiden.
- **L3 L7 skydd med en Programgateway:** programmet Gateway Brandvägg funktioner ger fullständig stack DDoS-skydd.
- **Omfattande lösning skala:** över 60 olika attack typer kan begränsas med globala kapacitet att skydda mot största kända DDoS-attacker. 
- **Angrepp mått:** Summarized från varje attacker är tillgängligt via Azure-Monitor.
- **Attack aviseringar:** aviseringar kan konfigureras vid start och stopp av ett angrepp och över angrepp varaktighet använder inbyggda attack mått. Aviseringar integrera din operativa program som OMS, Splunk, Azure Storage, e-post och Azure-portalen.
- **Kostnad garanti:** överföra Data och program skalbar servicekrediter för dokumenterade DDoS-attacker.

## <a name="ddos-protection-standard-mitigation"></a>DDoS-skydd Standard lösning

Microsofts DDoS-skydd-tjänsten övervakar faktiska trafik användning och ständigt jämför den med de tröskelvärden som anges i DDoS-principen. När trafiken gränsen överskrids, initieras DDoS minskning automatiskt. När trafiken returnerar under tröskelvärdet, tas minskning bort.

Vid migrering av trafik till den skyddade resursen omdirigeras av tjänsten DDoS-skydd och flera kontroller utförs. De här kontrollerna utför vanligtvis följande funktion:

- Se till att paket som överensstämmer med Internet-specifikationer och inte är felaktig.
- Interagera med klienten för att avgöra om det är potentiellt falska paket (t.ex.: SYN Auth eller SYN Cookie eller genom att släppa ett paket att skicka den källa).
- Gräns för överföringshastigheten paket om ingen annan metod för tvingande inte kan utföras.

DDoS-skydd-block angrepp trafik och återstående vidarebefordra trafik till avsedda mål. Inom några minuter för angreppsidentifiering visas ett meddelande med hjälp av Azure-Monitor mätvärden. Genom att konfigurera loggning på DDoS-skydd Standard telemetri kan skriva du loggarna till alternativen för framtida analys. Mätvärden i Azure-Monitor för DDoS-skydd Standard behålls för närvarande i 30 dagar.

Vi meddela inte kunder att simulera sina egna DDoS-attacker. I stället kunder kan använda support kanalen för att begära en DDoS angrepp simuleringen körs av Azure-nätverk. En ingenjör att kontakta dig för att ordna information för DDoS-attacker (portar, protokoll, mål-IP-adresser) och boka en tid att schemalägga testet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du hanterar DDoS-skydd Standard med hjälp av [Azure PowerShell](ddos-protection-manage-ps.md) eller [Azure-portalen](ddos-protection-manage-portal.md).
