---
title: Utgående regler i Azure Load Balancer | Microsoft Docs
description: Använd utgående regler för att definiera adressöversättningar för utgående nätverkstrafik
services: load-balancer
documentationcenter: na
author: KumudD
manager: jpconnock
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/3/2018
ms.author: kumud
ms.openlocfilehash: 50b567b298406b936a11ad5a8737ce4b1e21c0c7
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248732"
---
# <a name="load-balancer-outbound-rules"></a>Utgående regler för belastningsutjämnare

Azure Load Balancer tillhandahåller utgående anslutning från ett virtuellt nätverk förutom inkommande.  Utgående regler gör det enkelt att konfigurera offentliga [Standardbelastningsutjämnare](load-balancer-standard-overview.md)'s utgående nätverksadresser.  Har du fullständig deklarativa kontroll över utgående anslutning till skala och finjustera den här möjligheten för dina specifika behov.

![Utgående regler för belastningsutjämnare](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Du kan använda belastningsutjämnare med utgående regler: 
- Definiera utgående NAT från grunden.
- skala och finjustera beteendet för befintliga utgående NAT. 

Utgående regler kan du styra:
- vilka virtuella datorer ska översättas till som offentliga IP-adresser. 
- hur [utgående SNAT portar](load-balancer-outbound-connections.md#snat) ska allokeras.
- vilka protokoll som används för att ge utgående översättning för.
- vilka varaktighet för utgående anslutningstimeout för inaktivitet.
- Om du vill skicka en TCP-återställning för timeout för inaktivitet (i allmänt tillgänglig förhandsversion). 

Utgående regler Expandera [scenario 2](load-balancer-outbound-connections.md#lb) i beskrivs i den [utgående anslutningar](load-balancer-outbound-connections.md) artikeln och scenario-prioritet förblir som – är.

## <a name="outbound-rule"></a>Utgående regel

Precis som alla regler för belastningsutjämnare Följ utgående regler samma välbekanta syntax som belastningsutjämning och inkommande NAT-regler:

**klientdelens** + **parametrar** + **serverdelspool**

En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av serverdelspoolen_ översättas till den _klientdel_.  Och _parametrar_ ger ytterligare de detaljerade kontroll över den utgående NAT-algoritmen.

API-versionen ”2018-07-01” tillåter en utgående regeldefinition strukturerade på följande sätt:

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
>Effektiva utgående NAT-konfigurationen är sammansatt av alla utgående regler och regler för belastningsutjämning. Utgående regler är inkrementell säkerhetskopiering regler för belastningsutjämning. Granska [inaktiverar utgående NAT för en belastningsutjämningsregel](#disablesnat) hantera effektiva utgående NAT-översättningen när flera regler gäller för en virtuell dator. Du måste [inaktivera utgående SNAT](#disablesnat) när du definierar en utgående regel som använder samma offentliga IP-adress som en regel för belastningsutjämning.

### <a name="scale"></a> Skala utgående NAT med flera IP-adresser

En utgående regel kan användas med bara en enda offentlig IP-adress, förenklar utgående regler arbetet konfiguration för att skala utgående NAT. Du kan använda flera IP-adresser för att planera för storskaliga scenarier och du kan använda utgående regler för att minimera [SNAT resursuttömning](load-balancer-outbound-connections.md#snatexhaust) felbenägna mönster.  

Varje ytterligare IP-adress som tillhandahålls av en klientdel tillhandahåller 64 000 tillfälliga portar för belastningsutjämnaren ska användas som SNAT portar. Även om Utjämning av nätverksbelastning eller inkommande NAT-regler har en enda klientdel, utgående regel utökas frontend-notation och gör att flera klienter per regel.  Antalet tillgängliga portar för SNAT multipliceras med varje offentlig IP-adress med flera klienter per regel och mycket stora scenarier stöds.

Du kan också använda en [offentliga IP-adressprefix](https://aka.ms/lbpublicipprefix) direkt med en utgående regel.  Detta ger för enklare skalning och förenklad vitlistning av flöden från din Azure-distribution. Du kan konfigurera en frontend IP-konfiguration i belastningsutjämnaren resursen att direkt referera till en offentlig IP-adressprefix.  På så sätt kan belastningsutjämnaren exklusiv kontroll över det offentliga IP-adressprefix och utgående regel används automatiskt alla offentliga IP-adresser som ingår i det offentliga IP-adressprefix för utgående anslutningar.  Var och en av IP-adresser inom intervallet för det offentliga IP-adressprefix tillhandahåller 64 000 tillfälliga portar per IP-adress för belastningsutjämnaren ska användas som SNAT portar.   

Du kan inte ha enskilda offentliga IP-adressresurser som skapats från det offentliga IP-adressprefix när du använder det här alternativet som utgående regel måste ha fullständig kontroll över det offentliga IP-adressprefix.  Om du behöver mer bra störrre kontroll kan du skapa enskilda offentliga IP-adressresurs från det offentliga IP-adressprefix och tilldela flera offentliga IP-adresser individuellt till klientdelen av en utgående regel.

### <a name="snatports"></a> Finjustera SNAT porttilldelning

Du kan använda utgående regler för att finjustera den [automatisk SNAT-porttilldelning baserat på backend-poolstorlek](load-balancer-outbound-connections.md#preallocatedports) och allokera mer eller mindre än den automatiska SNAT port allokeringen ger.

Du kan använda följande parameter för att allokera 10 000 SNAT portar per virtuell dator (NIC IP-konfiguration).
 

          "allocatedOutboundPorts": 10000

Varje offentlig IP-adress från alla klienter för en utgående regel bidrar upp till 64 000 tillfälliga portar för oss som SNAT portar.  Belastningsutjämnaren tilldelas SNAT portar i multipler av 8. Om du anger ett värde som inte är delbara med 8 avvisade konfigurationen igen.  Om du försöker att allokera mer SNAT portar än vad som är tillgängliga beror på hur många offentliga IP-adresser, avvisade konfigurationen igen.  Till exempel om du allokerar 10 000 portar per virtuell dator och 7 virtuella datorer i en serverdel skulle pool delar en enda offentlig IP-adress, konfigurationen är nekade (7 x 10,0000 SNAT portar > 64,000 SNAT portar).  Du kan lägga till fler offentliga IP-adresser för klientdelen av utgående regeln för att aktivera scenariot.

Du kan återgå till [automatisk SNAT-porttilldelning baserat på backend-poolstorlek](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antalet portar.

### <a name="idletimeout"></a> Timeout för inaktivitet kontroll utgående flöde

Utgående regler ger en konfigurationsparameter för kontroll av tidsgränsen för inaktivitet utgående flödet och matcha den mot programmets behov.  Utgående inaktiv tidsgränser som standard 4 minuter.  Parametern accepterar ett värde mellan 4 och 66 till specifikt antalet minuter innan tidsgränsen för inaktivitet för flöden som matchar den här viss regel.

Använd följande parameter och ange utgående tidsgränsen för inaktivitet till 1 timme:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Aktivera TCP-återställning på timeout för inaktivitet (förhandsversion)

Standardbeteendet för belastningsutjämnare är att ta bort flödet tyst när utgående tidsgränsen för inaktivitet har uppnåtts.  Med parametern enableTCPReset som du kan aktivera en mer förutsägbar programmets beteende och kontrollera om du vill skicka dubbelriktad TCP-återställning (TCP RSTA) vid tidsgränsen för utgående timeout för inaktivitet. 

Använd följande parameter för att aktivera TCP-återställning på en utgående regel:

           "enableTcpReset": true

Granska [TCP nollställs timeout för inaktivitet (förhandsversion)](https://aka.ms/lbtcpreset) information om regiontillgänglighet.

### <a name="proto"></a> Stöd för både TCP och UDP-transport-protokoll med en enda regel

Vill du förmodligen att använda ”alla” för transport-protokoll för regel för utgående trafik, men du kan också använda regel för utgående trafik till en specifik transport-protokollet om det finns ett behov av att göra detta.

Använd följande parameter för att ange protokollet till TCP och UDP:

          "protocol": "All"

### <a name="disablesnat"></a> Inaktivera utgående NAT för en regel för belastningsutjämning

Som nämnts tidigare ger belastningsutjämningsregler automatisk programmeringen av utgående NAT. Men vissa scenarier dra eller kräver att du inaktiverar automatisk utgående NAT-programmering med regel om du vill kan du styra eller begränsa beteendet för belastningsutjämning.  Utgående regler har scenarier där det är viktigt att stoppa automatiska utgående NAT-programmering.

Du kan använda den här parametern på två sätt:
- Valfritt Undertryckning av med inkommande IP-adress för utgående NAT.  Utgående regler är inkrementell säkerhetskopiering regler för belastningsutjämning och med den här parameteruppsättningen utgående regel som finns i kontrollen.
  
- Finjustera utgående NAT-parametrarna för en IP-adress som används för inkommande och utgående samtidigt.  Automatisk utgående NAT-programmering måste inaktiveras för att tillåta en utgående regel genom att ta kontroll.  Till exempel för att kunna ändra porttilldelning SNAT för en adress också används för inkommande, den här parametern måste anges till true.  Om du försöker använda en utgående regel för att omdefiniera parametrarna för en IP-adress som också används för inkommande och utgående NAT-programmering av regel för belastningsutjämning inte släppts, misslyckas åtgärden att konfigurera en utgående regel.

>[!IMPORTANT]
> Den virtuella datorn inte har utgående anslutning om du anger parametern till true och inte har en utgående regel (eller [på instansnivå offentliga IP-scenariot](load-balancer-outbound-connections.md#ilpip) definiera utgående anslutning.  Vissa åtgärder på den virtuella datorn eller ditt program kan bero på att ha utgående anslutningar som är tillgängliga. Kontrollera att du förstår beroenden för ditt scenario och har vara effekten av den här ändringen.

Du kan inaktivera utgående SNAT på regel med den här Konfigurationsparametern för belastningsutjämning:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametern disableOutboundSNAT som standard false, vilket innebär att belastningsutjämningsregeln **har** tillhandahåller automatisk utgående NAT som en spegelbild av regelkonfigurationen för belastningsutjämning.  

Om du anger disableOutboundSnat till true för belastningsutjämningsregeln släpper belastningsutjämningsregeln kontrollen över annars automatisk utgående NAT-programmering.  Utgående SNAT till följd av belastningsutjämningsregel har inaktiverats.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Återanvända befintliga eller definiera nya serverdelspooler

Utgående regler inför inte ett nytt koncept för att definiera gruppen med virtuella datorer som regeln gäller.  I stället återanvända de konceptet med en backend-adresspool som också används för regler för belastningsutjämning. Du kan använda detta för att förenkla konfigurationen genom att antingen återanvända en befintlig definition för backend-pool eller skapa ett specifikt för en utgående regel.

## <a name="scenarios"></a>Scenarier

### <a name="groom"></a> Rensa utgående anslutningar till en specifik uppsättning offentliga IP-adresser

Du kan använda en utgående regel för att regelbundet rensa utgående anslutningar kommer från en specifik uppsättning offentliga IP-adresser för att underlätta vitlistning scenarier.  Den här offentliga IP-källadressen kan vara samma som används av en belastningsutjämningsregel eller en annan uppsättning offentliga IP-adresser än vad som används av en regel för belastningsutjämning.  

1. Skapa [offentliga IP-adressprefix](https://aka.ms/lbpublicipprefix) (eller den offentliga IP-adresser från offentliga IP-prefix)
2. Skapa en offentlig Standard Load Balancer
3. Skapa klienter som refererar till offentligt IP-prefix (eller offentliga IP-adresser) du vill använda
4. Återanvända en serverdelspool eller skapa en serverdelspool och placera de virtuella datorerna i en serverdelspool för offentlig belastningsutjämnare
5. Konfigurera en utgående regel på den offentliga belastningsutjämnare att programmera utgående NAT för dessa virtuella datorer med klienter
   
Om du inte vill för regel för belastningsutjämning som ska användas för utgående, måste du [inaktivera utgående SNAT](#disablesnat) på regeln för belastningsutjämning.

### <a name="modifysnat"></a> Ändra SNAT porttilldelning

Du kan använda utgående regler för att finjustera den [automatisk SNAT-porttilldelning baserat på backend-poolstorlek](load-balancer-outbound-connections.md#preallocatedports).

Om du har två virtuella datorer som delar en enda offentlig IP-adress för utgående NAT kanske du exempelvis vill öka antalet SNAT-portar som allokerats 1024 standardportarna om du upplever SNAT överbelastning. Varje offentlig IP-adress kan bidra med upp till 64 000 tillfälliga portar.  Om du konfigurerar en utgående regel med en enda offentlig IP-adress klientdel kan distribuera du högst 64 000 SNAT portar på virtuella datorer i serverdelspoolen.  För två virtuella datorer, kan högst 32 000 SNAT portar tilldelas med en utgående regel (2 x 32 000 = 64,000).

Granska [utgående anslutningar](load-balancer-outbound-connections.md) och information om hur [SNAT](load-balancer-outbound-connections.md#snat) portar fördelas och används.

### <a name="outboundonly"></a> Aktivera endast utgående

Du kan använda en offentlig Standard Load Balancer för att ange utgående NAT för en grupp virtuella datorer. I det här scenariot kan du använda en utgående regel, utan behov av alla ytterligare regler.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Utgående NAT för virtuella datorer endast (inte inkommande)

Definiera en offentlig Standard Load Balancer, placera de virtuella datorerna i serverdelspoolen och konfigurera en utgående regel för att programmera utgående NAT och rensa de utgående anslutningarna till kommer från en specifik offentlig IP-adress. Du kan också använda en offentlig IP-adressprefix förenkla vit-lista källan för utgående anslutningar.

1. Skapa en offentlig Standard Load Balancer.
2. Skapa en serverdelspool och placera de virtuella datorerna i en serverdelspool för den offentliga belastningsutjämnare.
3. Konfigurera en utgående regel på offentlig belastningsutjämnare att programmera utgående NAT för dessa virtuella datorer.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Utgående NAT för interna Standard Load Balancer-scenarier

När du använder en intern Standard Load Balancer, är utgående NAT inte tillgängligt förrän utgående anslutning har deklarerats explicit. Du kan definiera utgående anslutning som använder en utgående regel för att skapa utgående anslutning för virtuella datorer bakom en intern Standard Load Balancer med de här stegen:

1. Skapa en offentlig Standard Load Balancer.
2. Skapa en serverdelspool och placera de virtuella datorerna i en serverdelspool för den offentliga belastningsutjämnare utöver den interna belastningsutjämnaren.
3. Konfigurera en utgående regel på offentlig belastningsutjämnare att programmera utgående NAT för dessa virtuella datorer.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Aktivera både TCP och UDP-protokoll för utgående NAT med en offentlig Standard Load Balancer

- När du använder en offentlig Standardbelastningsutjämnare matchar den automatiska utgående NAT-programmering tillhandahålls transportprotokollet av regel för belastningsutjämning.  

   1. Inaktivera utgående SNAT på regeln för belastningsutjämning.
   2. Konfigurera en utgående regel på samma belastningsutjämnare.
   3. Återanvända serverdelspoolen som redan används av dina virtuella datorer.
   4. Ange ”protokoll”: ”alla” som en del av regel för utgående trafik.

- Ingen utgående NAT tillhandahålls när endast inkommande NAT-regler används.

   1. Placera virtuella datorer i en serverdelspool.
   2. Definiera en eller flera frontend IP-konfigurationer med offentliga IP-adresser eller offentliga IP-prefix.
   3. Konfigurera en utgående regel på samma belastningsutjämnare.
   4. Ange ”protokoll”: ”alla” som en del av utgående regel

## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara tillfälliga portar per frontend IP-adress är 64,000.
- En uppsättning konfigurerbara utgående tidsgränsen för inaktivitet är 4 till 66 minuter (240 4000 sekunder).
- Belastningsutjämnaren har inte stöd för ICMP för utgående NAT.
- Portalen kan inte användas för att konfigurera eller visa utgående regler.  Använd mallar, REST API, Az CLI 2.0 eller PowerShell i stället.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du använder [belastningsutjämnare för utgående anslutningar](load-balancer-outbound-connections.md).
- Lär dig mer om [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
- Lär dig mer om [dubbelriktad TCP-återställning på timeout för inaktivitet](load-balancer-tcp-reset.md).
- [Konfigurera regler för utgående trafik med Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
