---
title: Utforma virtuella nätverk med NAT-gatewayresurser
titleSuffix: Azure Virtual Network NAT
description: Lär dig hur du utformar virtuella nätverk med NAT-gatewayresurser.
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
ms.date: 03/31/2020
ms.author: allensu
ms.openlocfilehash: 405d9bc09462f2940567080ec86775baf066d70d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584571"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Designa virtuella nätverk med NAT-gatewayresurser

NAT-gatewayresurser är en del av [VIRTUAL Network NAT](nat-overview.md) och tillhandahåller utgående Internetanslutning för ett eller flera undernät i ett virtuellt nätverk. Undernätet för det virtuella nätverket anger vilka NAT-gateway som ska användas. NAT tillhandahåller SNAT (Source Network Address Translation) för ett undernät.  NAT-gatewayresurser anger vilka statiska IP-adresser virtuella datorer använder när utgående flöden skapas. Statiska IP-adresser kommer från offentliga IP-adressresurser, offentliga IP-prefixresurser eller båda. En NAT-gatewayresurs kan använda upp till 16 statiska IP-adresser från antingen.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuellt nätverk NAT för utgående till Internet">
</p>

*Bild: Virtuellt nätverk NAT för utgående till Internet*

## <a name="how-to-deploy-nat"></a>Så här distribuerar du NAT

Konfigurera och använda NAT gateway är avsiktligt enkelt:  

NAT-gatewayresurs:
- Skapa en regional eller zonbaserad (zonosolerad) NAT-gatewayresurs,
- Tilldela IP-adresser,
- Om det behövs kan du ändra timeout för inaktiv TCP (valfritt).  Granska [timers](#timers) <ins>innan</ins> du ändrar standardvärdet.

Virtuellt nätverk:
- Konfigurera det virtuella nätverksundernätet för att använda en NAT-gateway.

Användardefinierade vägar är inte nödvändiga.

## <a name="resource"></a>Resurs

Resursen är utformad för att vara enkel som du kan se i följande Azure Resource Manager-exempel i ett mallliknande format.  Det här mallliknande formatet visas här för att illustrera begreppen och strukturen.  Ändra exemplet för dina behov.  Det här dokumentet är inte avsett som en självstudiekurs.

Följande diagram visar skrivbara referenser mellan de olika Azure Resource Manager-resurserna.  Pilen visar referensens riktning och kommer från den plats där den kan skrivas. Granska 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="NAT-objektmodell för virtuellt nätverk">
</p>

*Bild: NAT-objektmodell för virtuellt nätverk*

NAT rekommenderas för de flesta arbetsbelastningar om du inte har ett specifikt beroende av [poolbaserad utgående anslutning av belastningsutjämnaren](../load-balancer/load-balancer-outbound-connections.md).  

Du kan migrera från standardscenarier för belastningsutjämnare, inklusive [utgående regler,](../load-balancer/load-balancer-outbound-rules-overview.md)till NAT-gateway. Om du vill migrera flyttar du de offentliga ip- och offentliga ip-prefixresurserna från belastningsutjämnads frontends till NAT-gatewayen. Nya IP-adresser för NAT-gateway krävs inte. Standard offentlig IP och prefix kan återanvändas så länge summan inte överstiger 16 IP-adresser. Planera för migrering med avbrott i tjänsten i åtanke under övergången.  Du kan minimera avbrottet genom att automatisera processen. Testa migreringen i en mellanlagringsmiljö först.  Under övergången påverkas inte inkommande ursprungsflöden.

Följande exempel är ett utdrag från en Azure Resource Manager-mall.  Den här mallen distribuerar flera resurser, inklusive en NAT-gateway.  Mallen har följande parametrar i det här exemplet:

- **natgatewayname** - Namnet på NAT-gatewayen.
- **plats** - Azure-region där resursen finns.
- **publicipname** - Namn på den utgående offentliga IP som är associerad med NAT-gatewayen.
- **vnetname** - Namn på det virtuella nätverket.
- **undernät** - Namnet på det undernät som är associerat med NAT-gatewayen.

Det totala antalet IP-adresser som tillhandahålls av alla IP-adress- och prefixresurser får inte överstiga totalt 16 IP-adresser. Valfritt antal IP-adresser mellan 1 och 16 är tillåtet.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

När NAT-gatewayresursen har skapats kan den användas i ett eller flera undernät i ett virtuellt nätverk. Ange vilka undernät som ska använda den här NAT-gatewayresursen. En NAT-gateway kan inte sträcka sig över mer än ett virtuellt nätverk. Det krävs inte att samma NAT-gateway tilldelas alla undernät i ett virtuellt nätverk. Enskilda undernät kan konfigureras med olika NAT-gatewayresurser.

Scenarier som inte använder tillgänglighetszoner är regionala (ingen zon har angetts). Om du använder tillgänglighetszoner kan du ange en zon för att isolera NAT till en viss zon. Zonredundans stöds inte. Granska [NAT-tillgänglighetszoner](#availability-zones).

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

NAT-gateways definieras med en egenskap i ett undernät i ett virtuellt nätverk. Flöden som skapas av virtuella datorer i **undernätsundernät i** virtuellt nätverk **vnetname** använder NAT-gatewayen. Alla utgående anslutningar använder IP-adresserna som är associerade med **natgatewayname** som källa-IP-adress.

Mer information om Azure Resource Manager-mallen som används i det här exemplet finns i:

- [Snabbstart: Skapa en NAT-gateway - Resource Manager-mall](quickstart-create-nat-gateway-template.md)
- [NAT för virtuellt nätverk](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Design vägledning

Läs det här avsnittet om du vill bekanta dig med överväganden för att utforma virtuella nätverk med NAT.  

1. [Kostnadsoptimering](#cost-optimization)
1. [Samexistens mellan inkommande och utgående](#coexistence-of-inbound-and-outbound)
2. [Hantera grundläggande resurser](#managing-basic-resources)
3. [Tillgänglighetszoner](#availability-zones)

### <a name="cost-optimization"></a>Kostnadsoptimering

[Tjänstslutpunkter](virtual-network-service-endpoints-overview.md) och [privat länk](../private-link/private-link-overview.md) är alternativ att överväga för att optimera kostnaden. NAT behövs inte för dessa tjänster. Trafik som dirigeras till tjänstslutpunkter eller privat länk bearbetas inte av det virtuella nätverkets NAT.  

Tjänstslutpunkter binder Azure-tjänstresurser till ditt virtuella nätverk och styr åtkomsten till dina Azure-tjänstresurser. När du till exempel använder Azure-lagring använder du en tjänstslutpunkt för lagring för att undvika databehandlade NAT-avgifter. Tjänstslutpunkter är kostnadsfria.

Privat länk exponerar Azure PaaS-tjänst (eller andra tjänster som finns med privat länk) som en privat slutpunkt i ett virtuellt nätverk.  Privat länk faktureras baserat på varaktighet och data som bearbetas.

Utvärdera om någon eller båda av dessa metoder är en bra passform för ditt scenario och använda efter behov.

### <a name="coexistence-of-inbound-and-outbound"></a>Samexistens mellan inkommande och utgående

NAT-gatewayen är kompatibel med:

 - Standardbelastningsutjämnare
 - Offentlig standard-IP
 - Offentligt IP-standardprefix

När du utvecklar en ny distribution, börja med vanliga SKU: er.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuellt nätverk NAT för utgående till Internet">
</p>

*Bild: Virtuellt nätverk NAT för utgående till Internet*

Det endast internet-utgående scenariot som tillhandahålls av NAT-gatewayen kan utökas med inkommande från Internet-funktioner. Varje resurs är medveten om i vilken riktning ett flöde har sitt ursprung. I ett undernät med en NAT-gateway ersätts alla utgående till Internet-scenarier av NAT-gatewayen. Inkommande från Internet-scenarier tillhandahålls av respektive resurs.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT och VIRTUELL med offentlig IP-adress på instansnivå

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="NAT och virtuell dator med offentlig IP-adress på instansnivå">
</p>

*Bild: NAT och virtuell dator med offentlig IP-adress på instansnivå*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | Virtuell dator med offentlig IP-adress på instansnivå |
| Utgående | NAT Gateway |

VM använder NAT-gateway för utgående.  Inkommande ursprung påverkas inte.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT och VM med offentlig belastningsutjämnare

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="NAT och virtuell dator med offentlig belastningsjämn">
</p>

*Bild: NAT och virtuell dator med offentlig belastningsjämn utjämning*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | offentliga belastningsutjämnaren |
| Utgående | NAT Gateway |

Alla utgående konfigurationer från en belastningsutjämningsregel eller utgående regler ersätts av NAT-gatewayen.  Inkommande ursprung påverkas inte.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT och VM med offentlig IP-adress på instansnivå och offentlig belastningsutjämnare

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="NAT och virtuell dator med offentlig IP-adress på instansnivå och offentlig belastningsutjämning">
</p>

*Bild: NAT och virtuell dator med offentlig IP-adress på instansnivå och offentlig belastningsutjämnare*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | Virtuell dator med offentlig IP-adress på instansnivå och offentlig belastningsutjämnare |
| Utgående | NAT Gateway |

Alla utgående konfigurationer från en belastningsutjämningsregel eller utgående regler ersätts av NAT-gatewayen.  Den virtuella datorn kommer också att använda NAT-gateway för utgående.  Inkommande ursprung påverkas inte.

### <a name="managing-basic-resources"></a>Hantera grundläggande resurser

Standardbelastningsutjämnare, offentlig IP och offentligt IP-prefix är kompatibla med NAT-gateway. NAT-gateways fungerar inom ramen för ett undernät. Den grundläggande SKU:n för dessa tjänster måste distribueras i ett undernät utan en NAT-gateway. Den här separationen gör att båda SKU-varianterna kan samexistera i samma virtuella nätverk.

NAT-gateways har företräde framför utgående scenarier för undernätet. Grundläggande belastningsutjämnare eller offentlig IP (och alla hanterade tjänster som skapats med dem) kan inte justeras med rätt översättningar. NAT-gatewayen tar kontroll över utgående till Internet-trafik i ett undernät. Inkommande trafik till grundläggande belastningsutjämnare och offentlig ip är inte tillgänglig. Inkommande trafik till en grundläggande belastningsutjämnare och, eller en offentlig ip som konfigurerats på en virtuell dator, är inte tillgänglig.

### <a name="availability-zones"></a>Tillgänglighetszoner

#### <a name="zone-isolation-with-zonal-stacks"></a>Zonisolering med zonstaplar

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtuellt nätverk NAT med zonisolering, skapa flera "zonal stacks"">
</p>

*Bild: Virtuellt nätverk NAT med zonisolering, skapa flera "zonal stackar"*

Även utan tillgänglighetszoner är NAT flexibel och kan överleva flera fel på infrastrukturkomponenter.  Tillgänglighetszoner bygger på den här återhämtningen med zonisoleringsscenarier för NAT.

Virtuella nätverk och deras undernät är regionala konstruktioner.  Undernät är inte begränsade till en zon.

Det finns ett zonlöfte om zonisolering när en instans för virtuella datorer med hjälp av en NAT-gatewayresurs ligger i samma zon som NAT-gatewayresursen och dess offentliga IP-adresser. Mönstret som du vill använda för zonisolering skapar en "zonstack" per tillgänglighetszon.  Den här "zonstapeln" består av instanser av virtuella datorer, NAT-gatewayresurser, offentliga IP-adress- och/eller prefixresurser i ett undernät som antas betjäna endast samma zon.   Styrplanets operationer och dataplan justeras sedan mot och begränsas till den angivna zonen. 

Fel i en annan zon än där ditt scenario finns förväntas vara utan påverkan på NAT. Utgående trafik från virtuella datorer i samma zon misslyckas på grund av zonisolering.  

#### <a name="integrating-inbound-endpoints"></a>Integrera inkommande slutpunkter

Om ditt scenario kräver inkommande slutpunkter har du två alternativ:

| Alternativ | Mönster | Exempel | Pro | Con |
|---|---|---|---|---|
| (1) | **Justera** de inkommande slutpunkterna med respektive **zonstaplar** som du skapar för utgående. | Skapa en standardbelastningsutjämnare med zonindel frontend. | Samma hälsomodell och felläge för inkommande och utgående. Enklare att använda. | Enskilda IP-adresser per zon kan behöva maskeras av ett vanligt DNS-namn. |
| (2) | **Överlagra** zonstaplarna med en inkommande slutpunkt **för inkommande korszoner.** | Skapa en standardbelastningsutjämnare med zonupptraklig frontend. | Enkel IP-adress för inkommande slutpunkt. | Varierande hälsomodell och fellägen för inkommande och utgående.  Mer komplext att använda. |

>[!NOTE]
> En zon-isolerad NAT-gateway kräver IP-adresser för att matcha zonen för NAT-gatewayen. NAT-gatewayresurser med IP-adresser från en annan zon eller utan zon är inte tillåtna.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Utgående scenarier över flera zoner stöds inte

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="Nat för virtuellt nätverk är inte kompatibelt med zonspann undernät">
</p>

*Bild: Nat för virtuellt nätverk är inte kompatibelt med zonpannor*

Du kan inte uppnå ett zonlöfte med NAT-gatewayresurser när instanser av virtuella datorer distribueras i flera zoner i samma undernät.   Och även om det fanns flera zonbaserade NAT-gateways kopplade till ett undernät, skulle den virtuella datorn instansen inte vet vilken NAT gateway-resurs att välja.

Ett zonlöfte finns inte när a) zonen för en virtuell datorinstans och zonerna för en zonal NAT-gateway inte justeras, eller b) en regional NAT-gatewayresurs används med zoninstanser för virtuella datorer.

Scenariot verkar fungera, men dess hälsomodell och felläge är odefinierat från en tillgänglighetszons synvinkel. Överväg att gå med zonal stackar eller alla regionala istället.

>[!NOTE]
>Egenskapen zoner för en NAT-gatewayresurs kan inte stängas av.  Distribuera om NAT-gatewayresursen med den avsedda regional- eller zoninställningen.

>[!NOTE] 
>IP-adresser i sig är inte zonundant om ingen zon har angetts.  Klientdelen för en [standardbelastningsutjämnare är zonundant](../load-balancer/load-balancer-standard-availability-zones.md#frontend) om en IP-adress inte skapas i en viss zon.  Detta gäller inte NAT.  Endast regional eller zonisolering stöds.

## <a name="source-network-address-translation"></a>Översättning av källnätverksadress

Översättning av källnätverksadress (SNAT) skriver om källan till ett flöde som kommer från en annan IP-adress.  NAT gateway-resurser använder en variant av SNAT som vanligtvis refereras till pat (portadressöversättning). PAT skriver om källadressen och källporten. Med SNAT finns det ingen fast relation mellan antalet privata adresser och deras översatta offentliga adresser.  

### <a name="fundamentals"></a>Grunder

Låt oss titta på ett exempel på fyra flöden för att förklara grundkonceptet.  NAT-gatewayen använder den offentliga IP-adressresursen 65.52.0.2.

| Flöde | Källa tuple | Destinationstuppdepel |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Dessa flöden kan se ut så här efter PAT har ägt rum:

| Flöde | Källa tuple | SNAT'ed källa tuple | Destinationstuppdepel | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

Målet ser källan till flödet som 65.52.0.2 (SNAT-källuppppelpel) med den tilldelade porten visad.  PAT som visas i föregående tabell kallas också port maskerad SNAT.  Flera privata källor maskeras bakom en IP och port.

Ta inte ett beroende på det specifika sättet källportar tilldelas.  Föregående är en illustration av det grundläggande begreppet bara.

SNAT som tillhandahålls av NAT skiljer sig från [belastningsutjämnare](../load-balancer/load-balancer-outbound-connections.md) i flera avseenden.

### <a name="on-demand"></a>På begäran

NAT tillhandahåller SNAT-portar på begäran för nya utgående trafikflöden. Alla tillgängliga SNAT-portar i lagret används av alla virtuella datorer på undernät som konfigurerats med NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Utgående SNAT för virtuellt nätverk på begäran">
</p>

*Bild: Utgående SNAT för virtuellt nätverk på begäran*

Alla IP-konfigurationer för en virtuell dator kan skapa utgående flöden på begäran efter behov.  Pre-allokering, per instans planering inklusive per instans värsta fall överetablering, krävs inte.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Skillnader i utmattningsscenarier">
</p>

*Figur: Skillnader i utmattningsscenarier*

När en SNAT-port har släppts är den tillgänglig för användning av alla virtuella datorer i undernät som konfigurerats med NAT.  Allokering på begäran gör att dynamiska och divergerande arbetsbelastningar på undernät kan använda SNAT-portar som de behöver.  Så länge det finns SNAT-portlager tillgängligt kommer SNAT-flöden att lyckas. SNAT port hotspots dra nytta av större lager istället. SNAT-portar lämnas inte oanvända för virtuella datorer som inte aktivt behöver dem.

### <a name="scaling"></a>Skalning

Skalning NAT är främst en funktion för att hantera den delade, tillgängliga SNAT-portlagret. NAT behöver tillräckligt med SNAT-portlager för förväntade utgående toppflöden för alla undernät som är kopplade till en NAT-gatewayresurs.  Du kan använda offentliga IP-adressresurser, offentliga IP-prefixresurser eller båda för att skapa SNAT-portlager.

SNAT mappar privata adresser till en eller flera offentliga IP-adresser, skriver om källadress och källport i processerna. En NAT-gatewayresurs använder 64 000 portar (SNAT-portar) per konfigurerad offentlig IP-adress för den här översättningen. NAT gateway-resurser kan skala upp till 16 IP-adresser och 1M SNAT-portar. Om en offentlig IP-prefixresurs tillhandahålls tillhandahåller varje IP-adress i prefixet SNAT-portlager. Och om du lägger till fler offentliga IP-adresser ökar de tillgängliga SNAT-portarna för lager. TCP och UDP är separata SNAT-portlager och orelaterade.

NAT gateway-resurser återanvänder opportunistiskt källportar. För skalning bör du anta att varje flöde kräver en ny SNAT-port och skala det totala antalet tillgängliga IP-adresser för utgående trafik.

### <a name="protocols"></a>Protokoll

NAT gateway-resurser interagerar med IP- och IP-transporthuvuden för UDP- och TCP-flöden och är agnostiker till nyttolaster för programlager.  Andra IP-protokoll stöds inte.

### <a name="timers"></a>Timers

>[!IMPORTANT]
>Lång tomgång timer kan i onödan öka sannolikheten för SNAT utmattning. Ju längre timer du anger, desto längre NAT kommer att hålla fast vid SNAT-portar tills de så småningom inaktiv timeout. Om dina flöden är inaktiva tidsutgång, kommer de att misslyckas så småningom ändå och i onödan förbrukaR SNAT-port inventering.  Flöden som misslyckas vid 2 timmar skulle ha misslyckats vid standard 4 minuter också. Öka den inaktiva timeout är en sista utväg alternativ som bör användas sparsamt. Om ett flöde aldrig går inaktivt påverkas det inte av inaktiv timer.

TCP-timeout för inaktiv kan justeras från 4 minuter (standard) till 120 minuter (2 timmar) för alla flöden.  Dessutom kan du återställa inaktiv timer med trafik på flödet.  Ett rekommenderat mönster för att uppdatera långa inaktiva anslutningar och identifiering av slutpunktsbesynlighet är TCP-keepalives.  TCP keepalives visas som dubbla AK:er till slutpunkterna, är låga omkostnader och osynliga för programlagret.

Följande timers används för SNAT-portutgivning:

| Timer | Värde |
|---|---|
| TCP FIN | 60 sekunder |
| TCP-ert | 10 sekunder |
| TCP halv öppen | 30 sekunder |

En SNAT-port är tillgänglig för återanvändning till samma mål-IP-adress och målport efter 5 sekunder.

>[!NOTE] 
>Dessa timerinställningar kan komma att ändras. Värdena tillhandahålls för felsökning och du bör inte vara beroende av specifika timers just nu.

## <a name="limitations"></a>Begränsningar

- NAT är kompatibelt med vanliga offentliga IP-, offentliga IP-prefix- och belastningsutjämnareresurser.   Grundläggande resurser (till exempel grundläggande belastningsutjämnare) och alla produkter som härleds från dem är inte kompatibla med NAT.  Grundläggande resurser måste placeras i ett undernät som inte har konfigurerats med NAT.
- IPv4-adressfamilj stöds.  NAT interagerar inte med IPv6-adressfamiljen.  NAT kan inte distribueras i ett undernät med ett IPv6-prefix.
- NSG-flödesloggning stöds inte när NAT.NSG flow logging isn't supported when using NAT.
- NAT kan inte sträcka sig över flera virtuella nätverk.


## <a name="feedback"></a>Feedback

Vi vill veta hur vi kan förbättra servicen. Saknar en förmåga? Gör ditt argument för vad vi ska bygga nästa på [UserVoice för NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [NAT för virtuella nätverk](nat-overview.md).
* Lär dig mer om [mått och aviseringar för NAT-gatewayresurser](nat-metrics.md).
* Lär dig mer om [felsökning av NAT-gatewayresurser](troubleshoot-nat.md).
* Självstudiekurs för att validera NAT Gateway
  - [Azure CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-cli.md)
  - [Portalen](tutorial-create-validate-nat-gateway-cli.md)
* Snabbstart för distribution av en NAT-gatewayresurs
  - [Azure CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portalen](./quickstart-create-nat-gateway-portal.md)
  - [Mall](./quickstart-create-nat-gateway-template.md)
* Lär dig mer om API för NAT-gateway-resurs
  - [REST-API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* Läs mer om [tillgänglighetszoner](../availability-zones/az-overview.md).
* Läs mer om [standardbelastningsutjämnare](../load-balancer/load-balancer-standard-overview.md).
* Lär dig mer om [tillgänglighetszoner och standardbelastningsutjämnare](../load-balancer/load-balancer-standard-availability-zones.md).
* [Berätta vad du ska bygga härnäst för VIRTUELLT NÄTVERK NAT i UserVoice](https://aka.ms/natuservoice).


