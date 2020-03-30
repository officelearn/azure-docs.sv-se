---
title: Utgående regler - Azure Load Balancer
description: Med den här utbildningssökvägen börjar du använda utgående regler för att definiera utgående översättningar av nätverksadresser.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304199"
---
# <a name="load-balancer-outbound-rules"></a>Utgående regler för belastningsutjämnare

Azure Load Balancer tillhandahåller utgående anslutning från ett virtuellt nätverk förutom inkommande.  Utgående regler gör det enkelt att konfigurera offentliga [standardbelastningsutjämnings](load-balancer-standard-overview.md)utgående nätverksadressöversättning.  Du har full deklarativ kontroll över utgående anslutning för att skala och justera denna förmåga till dina specifika behov.

![Utgående regler för belastningsutjämnare](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med utgående regler kan du använda Belastningsutjämnare för att: 
- definiera utgående NAT från grunden.
- skala och justera beteendet för befintlig utgående NAT. 

Med utgående regler kan du styra:
- vilka virtuella datorer som ska översättas till vilka offentliga IP-adresser. 
- hur [utgående SNAT-portar](load-balancer-outbound-connections.md#snat) ska fördelas.
- vilka protokoll som ska tillhandahålla utgående översättning för.
- vilken varaktighet som ska användas för utgående anslutningsutlösa tidsgräns (4-120 minuter).
- om du vill skicka en TCP-återställning på inaktiv tidsgränsen

Utgående regler [utökar scenario 2](load-balancer-outbound-connections.md#lb) i beskrivningen i artikeln [för utgående anslutningar](load-balancer-outbound-connections.md) och scenarioprioriteten förblir som den är.

## <a name="outbound-rule"></a>Utgående regel

Precis som alla belastningsutjämna regler följer utgående regler samma välbekanta syntax som belastningsutjämning och inkommande NAT-regler:

**frontend** + **parametrar** + **backend pool**

En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av serverdelspoolen_ som ska översättas till _klientdelen_.  Och _parametrar_ ger ytterligare finkornig kontroll över den utgående NAT-algoritmen.

API-versionen "2018-07-01" tillåter en utgående regeldefinition strukturerad enligt följande:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Den effektiva utgående NAT-konfigurationen är en blandning av alla utgående regler och belastningsutjämningsregler. Utgående regler är inkrementella till belastningsutjämningsregler. Granska [inaktivera utgående NAT för en belastningsutjämningsregel för](#disablesnat) att hantera den effektiva utgående NAT-översättningen när flera regler gäller för en virtuell dator. Du måste [inaktivera utgående SNAT](#disablesnat) när du definierar en utgående regel som använder samma offentliga IP-adress som en belastningsutjämningsregel.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Skala utgående NAT med flera IP-adresser

Även om en utgående regel kan användas med bara en enda offentlig IP-adress, underlättar utgående regler konfigurationsbördan för skalning av utgående NAT. Du kan använda flera IP-adresser för att planera för storskaliga scenarier och du kan använda utgående regler för att minska [SNAT-utmattningsbenägna](load-balancer-outbound-connections.md#snatexhaust) mönster.  

Varje ytterligare IP-adress som tillhandahålls av en klientdel ger 64 000 tillfälliga portar som lastjämförelser ska användas som SNAT-portar. Medan belastningsutjämning eller inkommande NAT-regler har en enda frontend, utökar den utgående regeln frontend-begreppet och tillåter flera frontends per regel.  Med flera frontends per regel multipliceras antalet tillgängliga SNAT-portar med varje offentlig IP-adress och stora scenarier kan stödjas.

Dessutom kan du använda ett [offentligt IP-prefix](https://aka.ms/lbpublicipprefix) direkt med en utgående regel.  Med hjälp av offentligt IP-prefix kan du skala och förenkla vitlista med flöden som kommer från din Azure-distribution. Du kan konfigurera en klientdels-IP-konfiguration i belastningsutjämnaren för att referera till ett offentligt IP-adressprefix direkt.  Detta gör att belastningsutjämnaren exklusiv kontroll över det offentliga IP-prefixet och den utgående regeln kommer automatiskt att använda alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar.  Var och en av IP-adresserna inom intervallet för det offentliga IP-prefixet ger 64 000 tillfälliga portar per IP-adress för belastningsutjämnare som ska användas som SNAT-portar.   

Du kan inte ha enskilda offentliga IP-adressresurser som skapats från det offentliga IP-prefixet när du använder det här alternativet eftersom den utgående regeln måste ha fullständig kontroll över det offentliga IP-prefixet.  Om du behöver mer finkornig kontroll kan du skapa enskilda offentliga IP-adressresurs från det offentliga IP-prefixet och tilldela flera offentliga IP-adresser individuellt till klientdelen för en utgående regel.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Justera SNAT-portallokering

Du kan använda utgående regler för att justera den [automatiska SNAT-portallokeringen baserat på backend-poolstorlek](load-balancer-outbound-connections.md#preallocatedports) och allokera mer eller mindre än den automatiska SNAT-portallokeringen ger.

Använd följande parameter för att allokera 10 000 SNAT-portar per virtuell dator (NIC IP-konfiguration).
 

          "allocatedOutboundPorts": 10000

Varje offentlig IP-adress från alla frontends av en utgående regel bidrar med upp till 64 000 tillfälliga portar för användning som SNAT-portar.  Belastningsutjämnaren allokerar SNAT-portar i multiplar av 8. Om du anger ett värde som inte är delbart med 8 avvisas konfigurationsåtgärden.  Om du försöker allokera fler SNAT-portar än vad som är tillgängligt baserat på antalet offentliga IP-adresser avvisas konfigurationsåtgärden.  Om du till exempel allokerar 10 000 portar per virtuell dator och 7 virtuella datorer i en serverdelspool skulle dela en enda offentlig IP-adress, avvisas konfigurationen (7 x 10 000 SNAT-portar > 64 000 SNAT-portar).  Du kan lägga till fler offentliga IP-adresser i frontend för den utgående regeln för att aktivera scenariot.

Du kan återgå till [automatisk SNAT-portallokering baserat på serverdapoolstorlek](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antal portar. I så fall får de första 50 VM-instanserna 1024 portar, 51-100 VM-instanser får 512 och så vidare enligt tabellen.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Kontroll av utgående flödesutlösande timeout

Utgående regler ger en konfigurationsparameter för att styra den utgående tidsgränsen för inaktivt flöde och matcha den med behoven i ditt program.  Utgående tidsutlösa tidsutgångar som standard är 4 minuter.  Parametern accepterar ett värde från 4 till 120 till specifika antalet minuter för inaktiv tidsgränsen för flöden som matchar den här regeln.

Använd följande parameter för att ange tidsgränsen för utgående inaktiv tidsutskrift till 1 timme:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> Aktivera TCP-återställning vid inaktiv timeout

Standardbeteendet för belastningsutjämnaren är att släppa flödet tyst när den utgående tidsgränsen för inaktiv har uppnåtts.  Med parametern enableTCPReset kan du aktivera ett mer förutsägbart programbeteende och styra om du vill skicka dubbelriktad TCP-återställning (TCP RST) vid den tidpunkt efter utgående tidsgränsen för inaktiv inaktivitet. 

Använd följande parameter för att aktivera TCP-återställning på en utgående regel:

           "enableTcpReset": true

Granska [TCP-återställning vid inaktiv tidsgränsen](https://aka.ms/lbtcpreset) för mer information, inklusive regiontillgänglighet.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Stöd för både TCP- och UDP-transportprotokoll med en enda regel

Du kommer förmodligen att vilja använda "Alla" för transportprotokollet för den utgående regeln, men du kan också tillämpa den utgående regeln på ett visst transportprotokoll också om det finns ett behov av att göra det.

Använd följande parameter för att ställa in protokollet till TCP och UDP:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Inaktivera utgående NAT för en belastningsutjämningsregel

Som tidigare nämnts ger belastningsutjämningsregler automatisk programmering av utgående NAT. Vissa scenarier gynnar eller kräver dock att du inaktiverar automatisk programmering av utgående NAT genom belastningsutjämningsregeln så att du kan styra eller förfina beteendet.  Utgående regler har scenarier där det är viktigt att stoppa den automatiska utgående NAT-programmeringen.

Du kan använda den här parametern på två sätt:
- Dämpning som tillval för att använda den inkommande IP-adressen för utgående NAT.  Utgående regler är inkrementella till belastningsutjämningsregler och med den här parameteruppsättningen är den utgående regeln i kontroll.
  
- Justera de utgående NAT-parametrarna för en IP-adress som används för inkommande och utgående samtidigt.  Den automatiska utgående NAT-programmeringen måste inaktiveras så att en utgående regel kan ta kontroll.  Om du till exempel vill ändra SNAT-portallokeringen för en adress som också används för inkommande, måste den här parametern anges till true.  Om du försöker använda en utgående regel för att definiera om parametrarna för en IP-adress som också används för inkommande och inte har släppt utgående NAT-programmering av belastningsutjämningsregeln, misslyckas åtgärden för att konfigurera en utgående regel.

>[!IMPORTANT]
> Den virtuella datorn har inte utgående anslutning om du anger den här parametern till true och inte har en utgående regel (eller [offentligt IP-scenario på instansnivå](load-balancer-outbound-connections.md#ilpip) för att definiera utgående anslutning.  Vissa åtgärder för den virtuella datorn eller ditt program kan bero på att det finns utgående anslutningar tillgängliga. Se till att du förstår beroenden i ditt scenario och har övervägt effekten av att göra den här ändringen.

Du kan inaktivera utgående SNAT på belastningsutjämningsregeln med den här konfigurationsparametern:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametern disableOutboundSNAT är som standard false, **does** vilket innebär att belastningsutjämningsregeln tillhandahåller automatisk utgående NAT som en spegelbild av belastningsutjämningsregelkonfigurationen.  

Om du ställer in disableOutboundSnat på true på belastningsutjämningsregeln frigör belastningsutjämningsregeln kontrollen över den annars automatiska utgående NAT-programmeringen.  Utgående SNAT som ett resultat av belastningsutjämningsregeln är inaktiverad.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Återanvända befintliga eller definiera nya backend-pooler

Utgående regler introducerar inte ett nytt koncept för att definiera den grupp av virtuella datorer som regeln ska gälla för.  I stället återanvänder de konceptet med en backend pool, som också används för belastningsutjämningsregler. Du kan använda detta för att förenkla konfigurationen genom att antingen återanvända en befintlig serverdpooldefinition eller skapa en specifik för en utgående regel.

## <a name="scenarios"></a>Scenarier

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Groom utgående anslutningar till en viss uppsättning offentliga IP-adresser

Du kan använda en utgående regel för att förbereda utgående anslutningar som ska visas som kommer från en viss uppsättning offentliga IP-adresser för att underlätta vitlistningsscenarier.  Den här källans offentliga IP-adress kan vara samma som används av en belastningsutjämningsregel eller en annan uppsättning offentliga IP-adresser än vad som används av en belastningsutjämningsregel.  

1. Skapa [offentligt IP-prefix](https://aka.ms/lbpublicipprefix) (eller offentliga IP-adresser från offentligt IP-prefix)
2. Skapa en offentlig Standard Load Balancer
3. Skapa frontends som refererar till det offentliga IP-prefix (eller offentliga IP-adresser) som du vill använda
4. Återanvända en backend-pool eller skapa en backend-pool och placera de virtuella datorerna i en backend-pool av den offentliga belastningsutjämnaren
5. Konfigurera en utgående regel för den offentliga belastningsutjämnaren för att programmera utgående NAT för dessa virtuella datorer med hjälp av frontends
   
Om du inte vill att belastningsutjämningsregeln ska användas för utgående måste du [inaktivera utgående SNAT](#disablesnat) på belastningsutjämningsregeln.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Ändra SNAT-portallokering

Du kan använda utgående regler för att justera den [automatiska SNAT-portallokeringen baserat på backend-poolstorlek](load-balancer-outbound-connections.md#preallocatedports).

Om du till exempel har två virtuella datorer som delar en enda offentlig IP-adress för utgående NAT kan du öka antalet SNAT-portar som allokerats från standardportarna för 1024 om du upplever SNAT-utmattning. Varje offentlig IP-adress kan bidra med upp till 64 000 tillfälliga portar.  Om du konfigurerar en utgående regel med en enda offentlig IP-adress frontend kan du distribuera totalt 64 000 SNAT-portar till virtuella datorer i serverdelspoolen.  För två virtuella datorer kan högst 32 000 SNAT-portar tilldelas med en utgående regel (2x 32 000 = 64 000).

Granska [utgående anslutningar](load-balancer-outbound-connections.md) och information om hur [SNAT-portar](load-balancer-outbound-connections.md#snat) allokeras och används.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Endast aktivera utgående

Du kan använda en offentlig standardbelastningsutfärdare för att tillhandahålla utgående NAT för en grupp virtuella datorer. I det här fallet kan du använda en utgående regel av sig själv, utan att behöva ytterligare regler.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Utgående NAT endast för virtuella datorer (ingen inkommande)

Definiera en offentlig standardbelastningsutjämning, placera de virtuella datorerna i serverdapoolen och konfigurera en utgående regel för att programmera utgående NAT och vårda de utgående anslutningarna så att de kommer från en viss offentlig IP-adress. Du kan också använda ett offentligt IP-prefix förenkla vitlista källan till utgående anslutningar.

1. Skapa en offentlig standardbelastningsutjämningsapparat.
2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool av den offentliga belastningsutjämnaren.
3. Konfigurera en utgående regel för den offentliga belastningsutjämnaren för att programmera utgående NAT för dessa virtuella datorer.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Utgående NAT för interna standardscenarier för belastningsutjämnare

När du använder en intern standardbelastningsutjämnare är utgående NAT inte tillgänglig förrän utgående anslutning uttryckligen har deklarerats. Du kan definiera utgående anslutning med hjälp av en utgående regel för att skapa utgående anslutning för virtuella datorer bakom en intern standardbelastningsutjämnare med följande steg:

1. Skapa en offentlig standardbelastningsutjämningsapparat.
2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool av den offentliga belastningsutjämnaren utöver den interna belastningsutjämnaren.
3. Konfigurera en utgående regel för den offentliga belastningsutjämnaren för att programmera utgående NAT för dessa virtuella datorer.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Aktivera både TCP-& UDP-protokoll för utgående NAT med en offentlig standardbelastningsutjämning

- När du använder en offentlig standardbelastningsutjämning matchar den automatiska utgående NAT-programmeringen transportprotokollet för belastningsutjämningsregeln.  

   1. Inaktivera utgående SNAT på belastningsutjämningsregeln.
   2. Konfigurera en utgående regel på samma belastningsutjämnare.
   3. Återanvänd den backend-pool som redan används av dina virtuella datorer.
   4. Ange "protokoll": "Alla" som en del av den utgående regeln.

- När endast inkommande NAT-regler används anges ingen utgående NAT.

   1. Placera virtuella datorer i en backend-pool.
   2. Definiera en eller flera IP-konfigurationer med offentliga IP-adresser eller offentligt IP-prefix.
   3. Konfigurera en utgående regel på samma belastningsutjämnare.
   4. Ange "protokoll": "Alla" som en del av den utgående regeln

## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara efemära portar per ip-adress för klientdel är 64 000.
- Intervallet för den konfigurerbara utgående tidsgränsen för inaktiv är 4 till 120 minuter (240 till 7200 sekunder).
- Belastningsutjämnaren stöder inte ICMP för utgående NAT.
- Utgående regler kan endast tillämpas på primär IP-konfiguration av ett nätverkskort.  Flera nätverkskort stöds.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du använder [belastningsutjämnaren för utgående anslutningar](load-balancer-outbound-connections.md).
- Läs mer om [standardbelastningsutjämningsdon](load-balancer-standard-overview.md).
- Lär dig mer om [dubbelriktad TCP-återställning vid inaktiv timeout](load-balancer-tcp-reset.md).
- [Konfigurera utgående regler med Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
