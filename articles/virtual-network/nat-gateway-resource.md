---
title: Utforma virtuella nätverk med NAT-gateway-resurser
titleSuffix: Azure Virtual Network NAT
description: Lär dig hur du utformar virtuella nätverk med NAT gateway-resurser.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 26de7a6d05bde8d80e22bd8801ae9b5dc8faeb36
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669564"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>Utforma virtuella nätverk med NAT-gateway-resurser (offentlig för hands version)

NAT-gateway-resurser ingår i [Virtual Network NAT](nat-overview.md) och tillhandahålla utgående Internet anslutning för ett eller flera undernät i ett virtuellt nätverk. Under nätet för de virtuella nätverks tillstånd som NAT-gatewayen ska användas för. NAT tillhandahåller käll Network Address Translation (SNAT) för ett undernät.  NAT-gateway-resurser anger vilka statiska IP-adresser som virtuella datorer använder när de skapar utgående flöden. Statiska IP-adresser kommer från offentliga IP-adressresurser, resurser för offentliga IP-prefix eller både och. En NAT-gateway-resurs kan använda upp till 16 statiska IP-adresser från båda.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT för utgående till Internet">
</p>

*Bild: Virtual Network NAT för utgående till Internet*


>[!NOTE] 
>Virtual Network NAT är tillgängligt som offentlig för hands version för tillfället. För närvarande är det bara tillgängligt i en begränsad uppsättning [regioner](nat-overview.md#region-availability). Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="how-to-deploy-nat"></a>Så här distribuerar du NAT

Att konfigurera och använda NAT-gateway har avsiktligt gjort enkla:  

NAT-gateway-resurs:
- Skapa en regional eller zonindelade (zon isolerad) NAT-gateway-resurs,
- Tilldela IP-adresser,
- Ändra tids gräns för inaktivitet (valfritt).

Virtuellt nätverk:
- Konfigurera undernät för virtuellt nätverk för att använda en NAT-gateway.

Användardefinierade vägar är inte nödvändiga.

## <a name="resource"></a>Resurs

Resursen är utformad för att vara enkel som du kan se från följande Azure Resource Manager exempel i ett mall-liknande format.  Det här mall-liknande-formatet visas här för att illustrera koncepten och strukturen.  Ändra exemplet för dina behov.  Det här dokumentet är inte avsett som en själv studie kurs.

I följande diagram visas skrivbara referenser mellan de olika Azure Resource Manager resurserna.  Pilen anger riktningen för referensen, från vilken den är skrivbar. Granska 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Virtual Network NAT-objektmodellen">
</p>

*Bild: Virtual Network NAT-objektmodellen*

NAT rekommenderas för de flesta arbets belastningar om du inte har ett speciellt beroende på [poolbaserade Load Balancer utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md).  

Du kan migrera från vanliga scenarier för belastnings utjämning, inklusive [utgående regler](../load-balancer/load-balancer-outbound-rules-overview.md), till NAT-gateway. För att migrera, flytta resurserna för offentliga IP-adresser och offentliga IP-adresser från belastningsutjämnare till NAT-gateway. Nya IP-adresser för NAT gateway krävs inte. Offentlig standard-IP och prefix kan återanvändas så länge som summan inte överstiger 16 IP-adresser. Planera för migrering med avbrott i tjänsten i åtanke under över gången.  Du kan minimera avbrottet genom att automatisera processen. Testa migreringen i en mellanlagrings miljö först.  Under över gången påverkas inte inkommande ursprungliga flöden.

I följande exempel skapas en NAT gateway-resurs med namnet _myNATGateway_ i regionen _USA, östra 2, AZ 1_ med en tids gräns på _4 minuters_ inaktivitet. De angivna utgående IP-adresserna är:
- En uppsättning offentliga IP-adressresurser _myIP1_ och _myIP2_ och 
- En uppsättning offentliga IP-prefix resurser _myPrefix1_ och _myPrefix2_. 

Det totala antalet IP-adresser som anges av alla fyra IP-adressresurser får inte överstiga 16 IP-adresser totalt. Valfritt antal IP-adresser mellan 1 och 16 tillåts.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

När NAT-gateway-resursen har skapats kan den användas i ett eller flera undernät i ett virtuellt nätverk. Ange vilka undernät som använder denna NAT gateway-resurs. En NAT-gateway kan inte omfatta mer än ett virtuellt nätverk. Det är inte nödvändigt att tilldela samma NAT-gateway till alla undernät i ett virtuellt nätverk. Enskilda undernät kan konfigureras med olika NAT-gateway-resurser.

Scenarier som inte använder tillgänglighets zoner är regionala (ingen zon har angetts). Om du använder tillgänglighets zoner kan du ange en zon för att isolera NAT till en speciell zon. Zon-redundans stöds inte. Granska NAT- [tillgänglighets zoner](#availability-zones).


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
NAT-gatewayer definieras med en egenskap i ett undernät i ett virtuellt nätverk. Flöden som skapats av virtuella datorer i under nätet _mySubnet1_ för virtuella nätverk _myVNet_ använder NAT-gatewayen. Alla utgående anslutningar använder de IP-adresser som är kopplade till _myNatGateway_ som käll-IP-adress.


## <a name="design-guidance"></a>Design vägledning

Läs det här avsnittet för att bekanta dig med att tänka på när du utformar virtuella nätverk med NAT.  

1. [Kostnads optimering](#cost-optimization)
1. [Samexistens av inkommande och utgående](#coexistence-of-inbound-and-outbound)
2. [Hantera grundläggande resurser](#managing-basic-resources)
3. [Tillgänglighetszoner](#availability-zones)

### <a name="cost-optimization"></a>Kostnadsoptimering

[Tjänst slut punkter](virtual-network-service-endpoints-overview.md) och [privat länk](../private-link/private-link-overview.md) är två alternativ för att överväga för att optimera kostnader där NAT inte behövs.  All trafik som dirigeras till tjänstens slut punkter eller privata länkar bearbetas inte av det virtuella nätverkets NAT.  

Tjänst slut punkter binder Azure-tjänsteresurser till ditt virtuella nätverk och styr åtkomsten till dina Azure-tjänst resurser. När du till exempel använder Azure Storage ska du använda en tjänst slut punkt för lagring för att undvika att data som bearbetas NAT debiteras. Tjänstens slut punkter är kostnads fria.

Privat länk exponerar Azure PaaS service (eller andra tjänster som är värdbaserade med privat länk) som en privat slut punkt i ett virtuellt nätverk.  Privat länk faktureras baserat på varaktighet och bearbetade data.

Utvärdera om någon av eller båda dessa metoder passar bra för ditt scenario och använd vid behov.

### <a name="coexistence-of-inbound-and-outbound"></a>Samexistens av inkommande och utgående

NAT-gatewayen är kompatibel med:

 - Standard Load Balancer
 - Offentlig standard-IP
 - Allmänt standard-IP-prefix

Börja med standard-SKU: er när du utvecklar en ny distribution.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT för utgående till Internet">
</p>

*Bild: Virtual Network NAT för utgående till Internet*

Det enda Internet-utgående scenariot som tillhandahålls av NAT-gateway kan utökas med inkommande från Internet-funktioner. Varje resurs är medveten om i vilken riktning ett flöde har sitt ursprung. I ett undernät med NAT-gateway ersätts alla utgående till Internet-scenarier av NAT-gatewayen. Inkommande från Internet scenarier tillhandahålls av respektive resurs.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT och virtuell dator med offentlig IP på instans nivå

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Virtual Network NAT och virtuell dator med offentlig IP på instans nivå">
</p>

*Bild: Virtual Network NAT och virtuell dator med offentlig IP på instans nivå*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | Virtuell dator med offentlig IP på instans nivå |
| Utgående | NAT Gateway |

Den virtuella datorn kommer att använda NAT-gateway för utgående trafik.  Inkommande ursprungligt kommer inte att påverkas.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT och virtuell dator med offentlig Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Virtual Network NAT och virtuell dator med offentlig Load Balancer">
</p>

*Bild: Virtual Network NAT och virtuell dator med offentlig Load Balancer*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | offentlig Load Balancer |
| Utgående | NAT Gateway |

Eventuell utgående konfiguration från en belastnings Utjämnings regel eller utgående regler ersätts av NAT-gatewayen.  Inkommande ursprungligt kommer inte att påverkas.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT och virtuell dator med offentlig IP på instans nivå och offentliga Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Virtual Network NAT och virtuell dator med offentlig IP på instans nivå och offentliga Load Balancer">
</p>

*Bild: Virtual Network NAT och virtuell dator med offentlig IP på instans nivå och offentliga Load Balancer*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | Virtuell dator med offentlig IP på instans nivå och offentlig Load Balancer |
| Utgående | NAT Gateway |

Eventuell utgående konfiguration från en belastnings Utjämnings regel eller utgående regler ersätts av NAT-gatewayen.  Den virtuella datorn kommer också att använda NAT-gateway för utgående trafik.  Inkommande ursprungligt kommer inte att påverkas.

### <a name="managing-basic-resources"></a>Hantera grundläggande resurser

Standard Load Balancer, offentlig IP och offentliga IP-prefix är kompatibla med NAT-gateway. NAT-gatewayer körs i omfånget för ett undernät. Den grundläggande SKU: n för dessa tjänster måste distribueras i ett undernät utan NAT-gateway. Denna separation gör att båda SKU-varianterna kan finnas i samma virtuella nätverk.

NAT-gatewayer har företräde framför utgående scenarier i under nätet. En grundläggande belastningsutjämnare eller offentlig IP-adress (och en hanterad tjänst som skapats med dem) kan inte justeras med rätt översättningar. NAT-gatewayen tar kontroll över utgående trafik till Internet trafik på ett undernät. Inkommande trafik till grundläggande belastningsutjämnare och offentlig IP-adress är inte tillgänglig. Inkommande trafik till en grundläggande belastningsutjämnare och en offentlig IP-adress som kon figurer ATS på en virtuell dator är inte tillgänglig.

### <a name="availability-zones"></a>Tillgänglighetszoner

Även om du inte har tillgänglighets zoner är NAT elastiskt och kan överleva flera infrastruktur komponents problem. När tillgänglighets zoner är en del av ditt scenario bör du konfigurera NAT för en speciell zon.  Kontroll Plans åtgärderna och data planet är begränsade till den angivna zonen. Ett annat haveri i en zon än där ditt scenario finns förväntas vara utan påverkan på NAT. Utgående trafik från virtuella datorer i samma zon kan inte utföras på grund av zon isolering.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtual Network NAT med tillgänglighets zoner">
</p>

*Bild: Virtual Network NAT med tillgänglighets zoner*

En zon isolerad NAT gateway kräver att IP-adresser matchar zonen i NAT-gatewayen. NAT-gatewayens resurser med IP-adresser från en annan zon eller utan en zon stöds inte.

Virtuella nätverk och undernät är regionala och inte zonindelade justerade. En virtuell dator måste finnas i samma zon som NAT-gatewayen för ett zonindelade löfte av utgående anslutningar. Zon isolering skapas genom att skapa en zonindelade "stack" per tillgänglighets zon. Ett zonindelade löfte finns inte vid korsning av zoner i en zonindelade NAT-gateway eller med en regional NAT-gateway med zonindelade-VM: ar.

När du distribuerar skalnings uppsättningar för virtuella datorer som ska användas med NAT distribuerar du en zonindelade skalnings uppsättning i sitt eget undernät och kopplar den matchande zonens NAT-gateway till det under nätet. Om du använder zoner som spänner över skalnings uppsättningar (en skalnings uppsättning i två eller flera zoner) kommer NAT inte att tillhandahålla ett zonindelade löfte.  NAT stöder inte zon-redundans.  Endast regional eller zon isolering stöds.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="zon – utsträckning Virtual Network NAT">
</p>

*Bild: zon – utsträckning Virtual Network NAT*

Egenskapen zoner är inte föränderligt.  Distribuera om NAT-gateway-resursen med den avsedda regionala eller zon inställningen.

>[!NOTE] 
>IP-adresser som själva inte är zoner – redundanta om ingen zon anges.  Klient delen för en [standard Load Balancer är zon-redundant](../load-balancer/load-balancer-standard-availability-zones.md#frontend) om en IP-adress inte har skapats i en speciell zon.  Detta gäller inte för NAT.  Endast regional eller zon isolering stöds.

## <a name="source-network-address-translation"></a>Käll nätverks adress Översättning

Med käll Network Address Translation (SNAT) skrivs källan för ett flöde om till härstamma från en annan IP-adress.  Resurser för NAT-gateway använder en variant av SNAT som vanligt vis kallas port adress översättning (PAT). PAT skriver om käll-och käll porten. Med SNAT finns det ingen fast relation mellan antalet privata adresser och deras översatta offentliga adresser.  

### <a name="fundamentals"></a>Grunder

Nu ska vi titta på ett exempel på fyra flöden för att förklara det grundläggande konceptet.  NAT-gatewayen använder offentlig IP-adressresurs 65.52.0.2.

| Flöde | Käll tupel | Mål tupel |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Dessa flöden kan se ut så här när PAT har ägt rum:

| Flöde | Käll tupel | SNAT'ed-käll tupel | Mål tupel | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

Målet kommer att se källan till flödet som 65.52.0.2 (SNAT-källans tupel) med den tilldelade porten som visas.  PAT som visas i tabellen ovan kallas även för port maskerad SNAT.  Flera privata källor är maskerade bakom en IP-adress och port.

Ta inte ett beroende på det speciella sätt som käll portarna tilldelas.  Föregående är en illustration av det grundläggande konceptet.

SNAT som tillhandahålls av NAT skiljer sig från [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) i flera olika delar.

### <a name="on-demand"></a>På begäran

NAT tillhandahåller SNAT-portar på begäran för nya utgående trafik flöden. Alla tillgängliga SNAT-portar i lagret används av en virtuell dator på undernät som kon figurer ATS med NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Virtual Network NAT på begäran för utgående SNAT">
</p>

*Bild: Virtual Network NAT på begäran för utgående SNAT*

Alla IP-konfigurationer för en virtuell dator kan skapa utgående flöden vid behov.  I förväg allokeras planeringen per instans, inklusive överetablering per instans, vilket inte krävs.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Skillnader i utfalls scenarier">
</p>

*Bild: skillnader i utfalls scenarier*

När en SNAT-port släpps, är den tillgänglig för användning av alla virtuella datorer på undernät som kon figurer ATS med NAT.  Med allokering på begäran kan dynamiska och Divergent arbets belastningar på undernät använda SNAT-portar som de behöver.  Så länge som det finns SNAT-port inventeringen kommer SNAT-flöden att lyckas. SNAT-portens frekventa fläckar drar nytta av den större inventeringen i stället. SNAT-portar är inte kvar för virtuella datorer som inte aktivt behöver dem.

### <a name="scaling"></a>Skalning

NAT behöver tillräckligt med SNAT-port inventering för det fullständiga utgående scenariot. Skalning av NAT är i första hand en funktion i hanteringen av den delade, tillgängliga SNAT-port inventeringen. Tillräckligt med lager måste finnas för att åtgärda det utgående utgående flödet för alla undernät som är kopplade till en NAT-gateway-resurs.

SNAT mappar flera privata adresser till en offentlig adress och använder flera offentliga IP-adresser för skalning.

En NAT-gateway-resurs kommer att använda 64 000 portar (SNAT-portar) för en offentlig IP-adress.  De här SNAT-portarna blir den tillgängliga inventeringen för mappningen av privata till offentliga flöden. Och om du lägger till fler offentliga IP-adresser ökar de tillgängliga Inventory SNAT-portarna. NAT-gatewayens resurser kan skala upp till 16 IP-adresser och 1 miljon SNAT-portar.  TCP och UDP är separata SNAT-port inventeringar och orelaterade.

NAT-gateway-resurser autentiseringsuppsättningarna åter användning av käll portar. I skalnings syfte bör du förutsätta att varje flöde kräver en ny SNAT-port och skala det totala antalet tillgängliga IP-adresser för utgående trafik.

### <a name="protocols"></a>Protokoll

NAT-gatewayens resurser interagerar med IP-och IP-transportnivån för UDP-och TCP-flöden och är oberoende till program nivå nytto laster.  Andra IP-protokoll stöds inte.

### <a name="timers"></a>Timers

Tids gränsen för inaktivitet kan justeras från 4 minuter (standard) till 120 minuter (2 timmar) för alla flöden.  Dessutom kan du återställa inaktiv timer med trafik i flödet.  Ett rekommenderat mönster för att uppdatera långa inaktiva anslutningar och slut punkts Live-identifiering är TCP keepalive.  TCP keepalive-poster visas som dubbla ack-filer till slut punkterna, är låga kostnader och är osynliga för program lagret.

Följande timers används för Port versionen av SNAT:

| Timer | Värde |
|---|---|
| TCP RÄNTETRANS | 60 sekunder |
| TCP-VARJE | 10 sekunder |
| TCP halv öppen | 30 sekunder |

En SNAT-port är tillgänglig för åter användning till samma mål-IP-adress och mål Port efter 5 sekunder.

>[!NOTE] 
>Dessa timer-inställningar kan komma att ändras. Värdena används för att under lätta fel sökningen och du bör inte ta ett beroende på vissa timers just nu.

## <a name="limitations"></a>Begränsningar

- NAT är kompatibelt med standard-SKU offentlig IP, offentliga IP-prefix och belastnings Utjämnings resurser.   Grundläggande resurser (till exempel grundläggande belastningsutjämnare) och alla produkter som härletts från dem är inte kompatibla med NAT.  Grundläggande resurser måste placeras i ett undernät som inte har kon figurer ATS med NAT.
- IPv4-adress familjen stöds.  NAT interagerar inte med IPv6-adress familjen.
- NSG Flow-loggning stöds inte när NAT används.
- NAT kan inte omfatta flera virtuella nätverk.

## <a name="preview-participation"></a>Förhandsgranska deltagande

Följ [anvisningarna för att aktivera din prenumeration](nat-overview.md#public-preview-participation).

## <a name="feedback"></a>Feedback

Vi vill veta hur vi kan förbättra tjänsten. Dela din [feedback om den offentliga för hands versionen](https://aka.ms/natfeedback) med oss.  Och du kan föreslå och rösta på det vi ska bygga nästa på [UserVoice för NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [NAT för virtuella nätverk](nat-overview.md).
- Självstudie för att verifiera NAT-gateway
  * [Azure CLI](tutorial-create-validate-nat-gateway-cli.md),
  * [PowerShell](tutorial-create-validate-nat-gateway-cli.md),
  * [Portalen](tutorial-create-validate-nat-gateway-cli.md)
- Snabb start för distribution av en NAT-gateway-resurs
  * [Azure CLI](./quickstart-create-nat-gateway-cli.md),
  * [PowerShell](./quickstart-create-nat-gateway-powershell.md),
  * [Portal](./quickstart-create-nat-gateway-portal.md).
- Lär dig mer om [tillgänglighets zoner](../availability-zones/az-overview.md).
- Läs mer om [standard Load Balancer](../load-balancer/load-balancer-standard-overview.md).
- Lär dig mer om [tillgänglighets zoner och standard Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md).
- Läs mer om resurs-API för NAT-gateway
  * [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways),
  * [Azure CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest),
  * [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway).
- [Berätta för oss vad du ska bygga härnäst i UserVoice](https://aka.ms/natuservoice).
- [Ge feedback om den offentliga för hands versionen](https://aka.ms/natfeedback).
