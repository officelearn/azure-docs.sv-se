---
title: Felsök problem med Azure Virtual Network NAT-anslutning
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Felsök problem med Virtual Network NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2020
ms.author: allensu
ms.openlocfilehash: 185556e3045cb1a879bc256f4f2932549de71cd8
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274944"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Felsök problem med Azure Virtual Network NAT-anslutning

Den här artikeln hjälper administratörer att diagnostisera och lösa anslutnings problem när de använder Virtual Network NAT.

>[!NOTE] 
>Virtual Network NAT är tillgängligt som offentlig för hands version för tillfället. För närvarande är det bara tillgängligt i en begränsad uppsättning [regioner](nat-overview.md#region-availability). Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problem

- [SNAT-belastning](#snat-exhaustion).
- [ICMP-Ping fungerar inte](#icmp-ping-is-failing).

Följ stegen i följande avsnitt för att lösa problemen.

## <a name="resolution"></a>Lösning

### <a name="snat-exhaustion"></a>SNAT-belastning

En enda [NAT-gateway-resurs](nat-gateway-resource.md) stöder från 64 000 upp till 1 000 000 samtidiga flöden.  Varje IP-adress ger 64 000 SNAT-portar till tillgängligt lager. Du kan använda upp till 16 IP-adresser per NAT-gateway-resurs.  SNAT-mekanismen beskrivs [här](nat-gateway-resource.md#source-network-address-translation) i detalj.

#### <a name="steps"></a>Steg:

1. Undersök hur programmet skapar utgående anslutning (till exempel kod granskning eller paket fångst). 
2. Avgöra om den här aktiviteten är förväntad eller om programmet är felgenererat.  Använd mått i Azure Monitor för att styrka dina resultat.
3. Utvärdera om lämpliga mönster följs.
4. Utvärdera om SNAT-portens överbelastning ska begränsas med ytterligare IP-adresser tilldelade till NAT-gateway-resursen.

#### <a name="design-pattern"></a>Design mönster:

Dra alltid nytta av anslutnings åter användning och anslutningspoolen närhelst det är möjligt.  Med det här mönstret undviker du problem med resurs överbelastningen och kan resultera i förutsägbart beteende. Primitiver för dessa mönster finns i många utvecklings bibliotek och ramverk.
- Överväg att använda [asynkrona avsöknings mönster](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) för långvariga åtgärder för att frigöra anslutnings resurser för andra åtgärder.
- Långvariga flöden (till exempel återanvända TCP-anslutningar) bör använda TCP keepalive-eller Application Layer-keepalive för att undvika tids gränser för mellanliggande system.
- Återställnings [mönster](https://docs.microsoft.com/azure/architecture/patterns/retry) bör användas för att undvika aggressiva återförsök/burst vid tillfälliga fel eller återställning av fel.
Att skapa en ny TCP-anslutning för varje HTTP-åtgärd (kallas även "atomiska anslutningar") är en anti-mönster.  Atomiska anslutningar förhindrar att ditt program skalar bra och slöseri med resurser.  Pipelinerar alltid flera åtgärder i samma anslutning.  Ditt program kommer att ha nytta av transaktions hastighet och resurs kostnader.  När ditt program använder kryptering av transport skikt (t. ex. TLS), är det en betydande kostnad för bearbetningen av nya anslutningar.  Granska [moln design mönster i Azure](https://docs.microsoft.com/azure/architecture/patterns/) om du vill ha ytterligare tips.

#### <a name="mitigations"></a>Åtgärder

Du kan skala utgående anslutningar på följande sätt:

| Scenario | Åtgärd |
|---|---|
| Du har konkurrens för SNAT-portar och antalet SNAT-portar under perioder med hög användning. | Avgör om du kan lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix. Detta tillägg tillåter att upp till 16 IP-adresser sammanlagt till din NAT-gateway. Det här tillägget ger mer lager för tillgängliga SNAT-portar (64 000 per IP-adress) och gör att du kan skala ditt scenario ytterligare.|
| Du har redan fått 16 IP-adresser och har fortfarande överbelastning av SNAT-portar. | Distribuera din program miljö över flera undernät och ange en NAT-gateway-resurs för varje undernät. |

>[!NOTE]
>Det är viktigt att förstå varför SNAT-belastning sker. Se till att du använder rätt mönster för skalbara och tillförlitliga scenarier.  Att lägga till fler SNAT-portar i ett scenario utan att förstå orsaken till behovet bör vara en sista utväg. Om du inte förstår varför ditt scenario använder trycket på SNAT-port inventeringen och lägger till fler SNAT-portar i inventeringen genom att lägga till fler IP-adresser, fördröjs bara samma överbelastnings problem när ditt program skalas.  Du kan maskera andra ineffektiva och anti-mönster.

### <a name="icmp-ping-is-failing"></a>ICMP-Ping fungerar inte

[Virtual Network NAT](nat-overview.md) stöder IPv4 UDP-och TCP-protokoll. ICMP stöds inte och förväntas inte fungera.  Använd i stället TCP-anslutnings test (till exempel "TCP-ping") och UDP-specifika program lager för att validera slut punkt till slut punkt anslutning.

Följande tabell kan användas som start punkt för vilka verktyg som ska användas för att starta tester.

| Operativsystem | Allmän test av TCP-anslutning | Test av TCP-program | UDP |
|---|---|---|---|
| Linux | NC (allmän anslutnings test) | Sväng (TCP-programnivå test) | programspecifik |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-webbegäran](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | programspecifik |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Virtual Network NAT](nat-overview.md)
- Lär dig om [NAT gateway-resurs](nat-gateway-resource.md)
