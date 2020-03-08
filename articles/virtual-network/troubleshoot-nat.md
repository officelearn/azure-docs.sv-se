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
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674319"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Felsök problem med Azure Virtual Network NAT-anslutning

Den här artikeln hjälper administratörer att diagnostisera och lösa anslutnings problem när de använder Virtual Network NAT.

>[!NOTE] 
>Virtual Network NAT är tillgängligt som offentlig för hands version för tillfället. För närvarande är det bara tillgängligt i en begränsad uppsättning [regioner](nat-overview.md#region-availability). Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problem

* [SNAT-belastning](#snat-exhaustion)
* [ICMP-Ping fungerar inte](#icmp-ping-is-failing)
* [Anslutnings problem](#connectivity-failures)
* [IPv6-samexistens](#ipv6-coexistence)

Följ stegen i följande avsnitt för att lösa problemen.

## <a name="resolution"></a>Lösning

### <a name="snat-exhaustion"></a>SNAT-belastning

En enda [NAT-gateway-resurs](nat-gateway-resource.md) stöder från 64 000 upp till 1 000 000 samtidiga flöden.  Varje IP-adress ger 64 000 SNAT-portar till tillgängligt lager. Du kan använda upp till 16 IP-adresser per NAT-gateway-resurs.  SNAT-mekanismen beskrivs [här](nat-gateway-resource.md#source-network-address-translation) i detalj.

Ofta är rotor saken av SNAT-belastning ett antimönster för hur utgående anslutning upprättas och hanteras.  Granska det här avsnittet noggrant.

#### <a name="steps"></a>Steg

1. Undersök hur programmet skapar utgående anslutning (till exempel kod granskning eller paket fångst). 
2. Avgöra om den här aktiviteten är förväntad eller om programmet är felgenererat.  Använd [mått](nat-metrics.md) i Azure Monitor för att styrka dina resultat. Använd kategorin "misslyckades" för måttet för SNAT-anslutningar.
3. Utvärdera om lämpliga mönster följs.
4. Utvärdera om SNAT-portens överbelastning ska begränsas med ytterligare IP-adresser tilldelade till NAT-gateway-resursen.

#### <a name="design-patterns"></a>Designmönster

Dra alltid nytta av anslutnings åter användning och anslutningspoolen närhelst det är möjligt.  Dessa mönster undviker problem med resurs överbelastningen och ger ett förutsägbart, tillförlitligt och skalbart beteende. Primitiver för dessa mönster finns i många utvecklings bibliotek och ramverk.

_**Lösning:**_ Använd lämpliga mönster

- Överväg att använda [asynkrona avsöknings mönster](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) för långvariga åtgärder för att frigöra anslutnings resurser för andra åtgärder.
- Långvariga flöden (till exempel återanvända TCP-anslutningar) bör använda TCP keepalive-eller Application Layer-keepalive för att undvika tids gränser för mellanliggande system.
- Återställnings [mönster](https://docs.microsoft.com/azure/architecture/patterns/retry) bör användas för att undvika aggressiva återförsök/burst vid tillfälliga fel eller återställning av fel.
Att skapa en ny TCP-anslutning för varje HTTP-åtgärd (kallas även "atomiska anslutningar") är en anti-mönster.  Atomiska anslutningar förhindrar att ditt program skalar bra och slöseri med resurser.  Pipelinerar alltid flera åtgärder i samma anslutning.  Ditt program kommer att ha nytta av transaktions hastighet och resurs kostnader.  När ditt program använder kryptering av transport skikt (t. ex. TLS), är det en betydande kostnad för bearbetningen av nya anslutningar.  Granska [moln design mönster i Azure](https://docs.microsoft.com/azure/architecture/patterns/) om du vill ha ytterligare tips.

#### <a name="possible-mitigations"></a>Möjliga åtgärder

_**Lösning:**_ Skala utgående anslutningar på följande sätt:

| Scenario | Grund |Åtgärd |
|---|---|---|
| Du har konkurrens för SNAT-portar och antalet SNAT-portar under perioder med hög användning. | Kategorin "misslyckades" för [mått](nat-metrics.md) i SNAT-anslutningar i Azure Monitor visar tillfälliga eller permanenta fel över tid och hög anslutnings volym.  | Avgör om du kan lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix. Detta tillägg tillåter att upp till 16 IP-adresser sammanlagt till din NAT-gateway. Det här tillägget ger mer lager för tillgängliga SNAT-portar (64 000 per IP-adress) och gör att du kan skala ditt scenario ytterligare.|
| Du har redan fått 16 IP-adresser och har fortfarande överbelastning av SNAT-portar. | Försök att lägga till ytterligare IP-adress misslyckades. Det totala antalet IP-adresser från offentliga IP-adressresurser eller resurser för offentliga IP-prefix överskrider totalt 16. | Distribuera din program miljö över flera undernät och ange en NAT-gateway-resurs för varje undernät.  Utvärdera dina design mönster på ett optimerat sätt baserat på föregående [vägledning](#design-patterns). |

>[!NOTE]
>Det är viktigt att förstå varför SNAT-belastning sker. Se till att du använder rätt mönster för skalbara och tillförlitliga scenarier.  Att lägga till fler SNAT-portar i ett scenario utan att förstå orsaken till behovet bör vara en sista utväg. Om du inte förstår varför ditt scenario använder trycket på SNAT-port inventeringen och lägger till fler SNAT-portar i inventeringen genom att lägga till fler IP-adresser, fördröjs bara samma överbelastnings problem när ditt program skalas.  Du kan maskera andra ineffektiva och anti-mönster.

### <a name="icmp-ping-is-failing"></a>ICMP-Ping fungerar inte

[Virtual Network NAT](nat-overview.md) stöder IPv4 UDP-och TCP-protokoll. ICMP stöds inte och förväntas inte fungera.  

_**Lösning:**_ Använd i stället TCP-anslutnings test (till exempel "TCP-ping") och UDP-specifika program lager för att validera slut punkt till slut punkt anslutning.

Följande tabell kan användas som start punkt för vilka verktyg som ska användas för att starta tester.

| Operativsystem | Allmän test av TCP-anslutning | Test av TCP-program | UDP |
|---|---|---|---|
| Linux | NC (allmän anslutnings test) | Sväng (TCP-programnivå test) | programspecifik |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-webbegäran](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | programspecifik |

### <a name="connectivity-failures"></a>Anslutnings problem

Anslutnings problem med [Virtual Network NAT](nat-overview.md) kan bero på flera olika problem:

* tillfällig eller beständig [SNAT-belastning](#snat-exhaustion) på NAT-gatewayen
* tillfälliga haverier i Azure-infrastrukturen, 
* tillfälliga haverier i sökvägen mellan Azure och det offentliga Internet målet 
* tillfälliga eller permanenta haverier på det offentliga Internet målet.

Använd verktyg som följande för att verifiera anslutningen. [ICMP-Ping stöds inte](#icmp-ping-is-failing).

| Operativsystem | Allmän test av TCP-anslutning | Test av TCP-program | UDP |
|---|---|---|---|
| Linux | NC (allmän anslutnings test) | Sväng (TCP-programnivå test) | programspecifik |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-webbegäran](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | programspecifik |

#### <a name="snat-exhaustion"></a>SNAT-belastning

Granska avsnittet om [SNAT-inblåsning](#snat-exhaustion) i den här artikeln.

#### <a name="azure-infrastructure"></a>Azure-infrastruktur

Även om Azure övervakar och arbetar med sin infrastruktur med fantastiska försiktighet kan det uppstå tillfälliga fel eftersom det inte finns någon garanti för att överföringen är förlustfri.  Använd design mönster som tillåter SYN återöverföringar för TCP-program. Använd anslutnings-timeout tillräckligt stor för att tillåta TCP-dataöverföring för att minska den tillfälliga påverkan som orsakas av ett förlorat SYN-paket.

_**Lösa**_

* Sök efter [SNAT-belastning](#snat-exhaustion).
* Konfigurations parametern i en TCP-stack som styr SYN återöverförings beteendet kallas RTO ([timeout för återöverföring](https://tools.ietf.org/html/rfc793)). RTO-värdet är justerbart, men vanligt vis 1 sekund eller högre som standard med exponentiella säkerhets kopieringar.  Om programmets anslutnings-timeout är för kort (till exempel 1 sekund) kan du se sporadisk tids gräns för anslutning.  Öka tids gränsen för program anslutningen.
* Om du anser att längre, oväntade tids gränser med standard program beteenden, öppnar du ett support ärende för ytterligare fel sökning.

Vi rekommenderar inte artificiellt minska TCP-anslutningens tids gräns eller justera RTO-parametern.

#### <a name="public-internet-transit"></a>offentlig Internet överföring

Sannolikheten för tillfälliga haverier ökar med en längre sökväg till målet och fler mellanliggande system. Det förväntas att tillfälliga problem kan öka i frekvens över [Azure-infrastrukturen](#azure-infrastructure). 

Följ samma rikt linjer som i avsnittet föregående [Azure-infrastruktur](#azure-infrastructure) .

#### <a name="internet-endpoint"></a>Internet slut punkt

Föregående avsnitt gäller utöver de överväganden som rör Internet slut punkten som din kommunikation upprättas med. Andra faktorer som kan påverka anslutningen lyckas är:

* trafik hantering på mål sidan, inklusive
- API-hastighet som begränsas av mål Sidan
- DDoS-åtgärder för volym begränsning eller transport skikt
* brand vägg eller andra komponenter på målet 

Normalt är paket insamlingarna på källan samt destination (om de är tillgängliga) för att fastställa vad som sker.

_**Lösa**_

* Sök efter [SNAT-belastning](#snat-exhaustion). 
* Verifiera anslutningen till en slut punkt i samma region eller någon annan stans för jämförelse.  
* Om du skapar hög volym-eller transaktions frekvens test bör du undersöka om du minskar hastigheten för felaktiga händelser.
* Om ändrings takten påverkar antalet fel, kontrollerar du om begränsningar för API-hastighet eller andra begränsningar på mål sidan kan ha nåtts.
* Om din undersökning är tveksam kan du öppna ett support ärende för ytterligare fel sökning.

#### <a name="tcp-resets-received"></a>Mottagna TCP-reuppsättningar

Om du söker efter TCP-återställningar (TCP-paket) som tagits emot på den virtuella käll datorn kan de genereras av NAT-gatewayen på den privata sidan för flöden som inte är identifierade som pågående.  En möjlig orsak är att TCP-anslutningen har nått tids gränsen för inaktivitet.  Du kan justera tids gränsen för inaktivitet från 4 minuter till upp till 120 minuter.

TCP-återställning genereras inte på den offentliga sidan av NAT-gatewayens resurser. Om du får TCP-återställningar på mål sidan, genereras de av den virtuella käll datorns stack och inte NAT-gateway-resursen.

_**Lösa**_

* Granska [design mönster](#design-patterns) rekommendationer.  
* Öppna ett support ärende om du behöver ytterligare fel sökning.

### <a name="ipv6-coexistence"></a>IPv6-samexistens

[Virtual Network NAT](nat-overview.md) stöder IPv4 UDP-och TCP-protokoll och distribution i ett [undernät med IPv6-prefix stöds inte](nat-overview.md#limitations).

_**Lösning:**_ Distribuera NAT-gateway i ett undernät utan IPv6-prefix.

Du kan ange intresse för ytterligare funktioner via [Virtual Network NAT UserVoice](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Virtual Network NAT](nat-overview.md)
* Lär dig om [NAT gateway-resurs](nat-gateway-resource.md)
* Lär dig mer om [mått och aviseringar för NAT gateway-resurser](nat-metrics.md).
* [Berätta för oss vad du ska bygga härnäst för Virtual Network NAT i UserVoice](https://aka.ms/natuservoice).

