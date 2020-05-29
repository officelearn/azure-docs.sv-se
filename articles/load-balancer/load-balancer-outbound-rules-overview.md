---
title: Utgående regler – Azure Load Balancer
description: Med den här utbildnings vägen kan du börja använda utgående regler för att definiera utgående översättningar av nätverks adresser.
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
ms.openlocfilehash: e6c7464eb1bf51a4e42d0db98d92459dc39fbb11
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170825"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer utgående regler

Azure Load Balancer tillhandahåller utgående anslutning från ett virtuellt nätverk utöver inkommande.  Utgående regler gör det enkelt att konfigurera offentliga [standard load BALANCERS](load-balancer-standard-overview.md)utgående Network Address Translation.  Du har fullständig deklarativ kontroll över utgående anslutning för att skala och finjustera den här funktionen för dina behov.

![Load Balancer utgående regler](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med utgående regler kan du använda Load Balancer för att: 
- definiera utgående NAT från grunden.
- skala och finjustera beteendet för befintlig utgående NAT. 

Med utgående regler kan du styra:
- vilka virtuella datorer som ska översättas till vilka offentliga IP-adresser. 
- hur [utgående SNAT-portar](load-balancer-outbound-connections.md#snat) ska allokeras.
- vilka protokoll som ska tillhandahålla utgående översättning för.
- vilken varaktighet som ska användas för utgående timeout för utgående anslutning (4-120 minuter).
- Om du vill skicka en TCP-återställning vid inaktivitet

Utgående regler expandera [Scenario 2](load-balancer-outbound-connections.md#lb) i beskrivningen i artikeln [utgående anslutningar](load-balancer-outbound-connections.md) och scenario prioriteten finns kvar.

## <a name="outbound-rule"></a>Utgående regel

Precis som alla Load Balancer regler följer utgående regler samma välkända syntax som belastnings utjämning och inkommande NAT-regler:

**klient**  +  del **parametrar**  +  **backend-pool**

En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av backend-poolen_ som ska översättas till _klient delen_.  _Parametrarna_ och ger ytterligare detaljerad kontroll över den utgående NAT-algoritmen.

Med API-versionen "2018-07-01" tillåts en utgående regel definition som är strukturerad enligt följande:

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
>Den effektiva utgående NAT-konfigurationen är sammansatt av alla regler för utgående trafik och belastnings utjämning. Utgående regler är stegvisa för belastnings Utjämnings regler. Granska [inaktivera utgående NAT för en belastnings Utjämnings regel](#disablesnat) för att hantera den effektiva utgående NAT-översättningen när flera regler gäller för en virtuell dator. Du måste [inaktivera utgående SNAT](#disablesnat) när du definierar en utgående regel som använder samma offentliga IP-adress som en belastnings Utjämnings regel.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Skala utgående NAT med flera IP-adresser

Även om en utgående regel kan användas med bara en enda offentlig IP-adress, underlättar utgående regler konfigurations bördan för skalning av utgående NAT. Du kan använda flera IP-adresser för att planera för storskaliga scenarier och du kan använda utgående regler för att minska mönstren för [SNAT-överbelastningar](troubleshoot-outbound-connection.md#snatexhaust) .  

Varje ytterligare IP-adress som tillhandahålls av en klient del ger 64 000 tillfälliga portar för Load Balancer att använda sig av SNAT-portar. När belastnings utjämning eller inkommande NAT-regler har en enda klient del, expanderar regeln för utgående trafik klient dels begreppet och tillåter flera klient delar per regel.  Med flera klient delar per regel multipliceras antalet tillgängliga SNAT-portar med varje offentlig IP-adress och stora scenarier kan stödjas.

Dessutom kan du använda ett [offentligt IP-prefix](https://aka.ms/lbpublicipprefix) direkt med en utgående regel.  Med det offentliga IP-prefixet får du enklare skalning och förenklad, vit lista över flöden från din Azure-distribution. Du kan konfigurera en IP-konfiguration för klient delen i Load Balancer resurs för att referera till ett offentligt IP-adressprefix direkt.  Detta gör att Load Balancer exklusiv kontroll över det offentliga IP-prefixet och regeln för utgående trafik automatiskt använder alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar.  Var och en av IP-adresserna inom intervallet för det offentliga IP-prefixet ger 64 000 tillfälliga portar per IP-adress för Load Balancer som ska användas som SNAT-portar.   

Du kan inte ha enskilda offentliga IP-adressresurser som skapats från det offentliga IP-prefixet när du använder det här alternativet eftersom regeln för utgående trafik måste ha fullständig kontroll över det offentliga IP-prefixet.  Om du behöver mer detaljerad kontroll kan du skapa en individuell offentlig IP-adressresurs från det offentliga IP-prefixet och tilldela flera offentliga IP-adresser individuellt till klient delen för en utgående regel.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Finjustera SNAT-port tilldelning

Du kan använda utgående regler för att finjustera den [automatiska SNAT-port tilldelningen baserat på storleken på backend-poolen](load-balancer-outbound-connections.md#preallocatedports) och allokera mer eller mindre än den automatiska SNAT-port tilldelningen ger.

Använd följande parameter för att allokera 10 000 SNAT-portar per virtuell dator (NIC IP-konfiguration).
 

          "allocatedOutboundPorts": 10000

Varje offentlig IP-adress från alla klient delar av en utgående regel bidrar upp till 64 000 tillfälliga portar för användning som SNAT-portar.  Load Balancer allokerar SNAT-portar i multipler av 8. Om du anger ett värde som inte är delbart med 8 avvisas konfigurations åtgärden.  Om du försöker allokera fler SNAT-portar än vad som är tillgängligt baserat på antalet offentliga IP-adresser avvisas konfigurations åtgärden.  Om du till exempel allokerar 10 000-portar per virtuell dator och 7 virtuella datorer i en backend-pool delar en enskild offentlig IP-adress, så avvisas konfigurationen (7 x 10 000/SNAT-portar > 64 000/SNAT-portar).  Du kan lägga till fler offentliga IP-adresser till klient delen för den utgående regeln för att aktivera scenariot.

Du kan återgå till [Automatisk SNAT-port tilldelning baserat på storlek på backend-poolen](load-balancer-outbound-connections.md#preallocatedports) genom att ange 0 för antal portar. I så fall kommer de första 50 VM-instanserna att få 1024 portar, 51-100 VM-instanser får 512 och så vidare enligt tabellen.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Kontrol lera timeout för utgående inaktivt flöde

Utgående regler tillhandahåller en konfigurations parameter för att kontrol lera utgående tids gräns för utgående trafik och matcha den mot ditt programs behov.  Timeout för utgående inaktivitet är som standard 4 minuter.  Parametern accepterar ett värde från 4 till 120 till ett specifikt antal minuter för tids gränsen för inaktivitet för flöden som matchar den här regeln.

Använd följande parameter för att ange tids gränsen för utgående inaktivitet till 1 timme:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a>Aktivera timeout för TCP-återställning vid inaktivitet

Standard beteendet för Load Balancer är att släppa flödet tyst när tids gränsen för utgående inaktivitet har nåtts.  Med parametern enableTCPReset kan du aktivera ett mer förutsägbart program beteende och kontrol lera om du vill skicka dubbelriktad TCP-återställning (TCP per-) vid tids gränsen för utgående inaktivitet. 

Använd följande parameter för att aktivera TCP-återställning för en utgående regel:

           "enableTcpReset": true

Granska [TCP-återställning vid inaktivitet](https://aka.ms/lbtcpreset) för mer information, inklusive region tillgänglighet.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Stöd för både TCP-och UDP-transport protokoll med en enda regel

Du kommer förmodligen att vilja använda "alla" för transport protokollet i den utgående regeln, men du kan också använda regeln för utgående trafik till ett särskilt transport protokoll även om det behövs.

Använd följande parameter för att ange protokollet till TCP och UDP:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Inaktivera utgående NAT för en belastnings Utjämnings regel

Som tidigare nämnts tillhandahåller belastnings Utjämnings regler automatisk programmering av utgående NAT. Vissa scenarier är dock förmånen eller kräver att du inaktiverar automatisk programmering av utgående NAT med belastnings Utjämnings regeln så att du kan styra eller förfina beteendet.  Utgående regler har scenarier där det är viktigt att stoppa den automatiska utgående NAT-programmeringen.

Du kan använda den här parametern på två sätt:
- Valfri Undertryckning av Använd den inkommande IP-adressen för utgående NAT.  Utgående regler är stegvisa för belastnings Utjämnings regler och med den här parameter uppsättningen är regeln för utgående trafik kontroll.
  
- Finjustera de utgående NAT-parametrarna för en IP-adress som används för inkommande och utgående samtidigt.  Automatisk utgående NAT-programmering måste inaktive ras för att tillåta en utgående regel att ta kontroll.  För att till exempel ändra SNAT-port tilldelningen för en adress som också används för inkommande, måste den här parametern anges till true.  Om du försöker använda en utgående regel för att omdefiniera parametrarna för en IP-adress som också används för inkommande och inte har publicerat ett utgående NAT-program för belastnings Utjämnings regeln, kommer åtgärden att konfigurera en utgående regel att Miss lyckas.

>[!IMPORTANT]
> Den virtuella datorn har ingen utgående anslutning om du anger den här parametern till true och inte har en utgående regel (eller ett [offentligt IP-scenario på instans nivå](load-balancer-outbound-connections.md#ilpip) för att definiera utgående anslutning.  Vissa åtgärder för din virtuella dator eller ditt program kan bero på att det finns en utgående anslutning tillgänglig. Se till att du förstår beroendena för ditt scenario och att du anser att du har gjort den här ändringen.

Du kan inaktivera utgående SNAT på belastnings Utjämnings regeln med denna konfigurations parameter:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametern disableOutboundSNAT **har** värdet false, vilket innebär att belastnings Utjämnings regeln tillhandahåller automatisk utgående NAT som en spegel avbildning av konfigurationen för belastnings Utjämnings regeln.  

Om du anger disableOutboundSnat till sant för belastnings Utjämnings regeln släpper regeln för belastnings utjämning kontrollen av den automatiska utgående NAT-programmeringen.  Utgående SNAT som ett resultat av belastnings Utjämnings regeln har inaktiverats.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Återanvänd befintliga eller definiera nya backend-pooler

Utgående regler introducerar inte ett nytt begrepp för att definiera gruppen med virtuella datorer som regeln ska gälla för.  De återanvänder i stället begreppet en backend-pool, som också används för belastnings Utjämnings regler. Du kan använda detta för att förenkla konfigurationen genom att antingen återanvända en befintlig definition för Server delens pool eller skapa en särskild för en utgående regel.

## <a name="scenarios"></a>Scenarier

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Rensa utgående anslutningar till en speciell uppsättning offentliga IP-adresser

Du kan använda en regel för utgående trafik för att rensa utgående anslutningar så att de kommer från en speciell uppsättning offentliga IP-adresser för att under lätta vit listning-scenarier.  Den här offentliga IP-adressen kan vara samma som används av en belastnings Utjämnings regel eller en annan uppsättning offentliga IP-adresser än vad som används av en belastnings Utjämnings regel.  

1. Skapa [offentliga IP-prefix](https://aka.ms/lbpublicipprefix) (eller offentliga IP-adresser från offentliga IP-prefix)
2. Skapa en offentlig Standard Load Balancer
3. Skapa frontend-klienter som refererar till det offentliga IP-prefix (eller offentliga IP-adresser) som du vill använda
4. Återanvänd en backend-pool eller skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga Load Balancer
5. Konfigurera en regel för utgående trafik på den offentliga Load Balancer till program utgående NAT för de här virtuella datorerna med hjälp av klient datorerna
   
Om du inte vill att belastnings Utjämnings regeln ska användas för utgående trafik måste du [inaktivera utgående SNAT](#disablesnat) i belastnings Utjämnings regeln.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Ändra SNAT-port tilldelning

Du kan använda utgående regler för att finjustera den [automatiska SNAT-port tilldelningen baserat på storleken på backend-poolen](load-balancer-outbound-connections.md#preallocatedports).

Om du till exempel har två virtuella datorer som delar en enda offentlig IP-adress för utgående NAT, kanske du vill öka antalet SNAT-portar som har allokerats från standard-1024-portarna om du upplever SNAT-belastning. Varje offentlig IP-adress kan bidra upp till 64 000 tillfälliga portar.  Om du konfigurerar en utgående regel med en enda offentlig IP-adresspool kan du distribuera totalt 64 000 SNAT-portar till virtuella datorer i backend-poolen.  För två virtuella datorer kan högst 32 000 SNAT-portar tilldelas med en utgående regel (2x 32 000 = 64 000).

Granska [utgående anslutningar](load-balancer-outbound-connections.md) och information om hur [SNAT](load-balancer-outbound-connections.md#snat) -portar allokeras och används.

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Aktivera endast utgående

Du kan använda en offentlig Standard Load Balancer för att tillhandahålla utgående NAT för en grupp med virtuella datorer. I det här scenariot kan du använda en utgående regel i sig självt, utan att behöva några ytterligare regler.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Utgående NAT enbart för virtuella datorer (ingen inkommande)

Definiera en offentlig Standard Load Balancer, placera de virtuella datorerna i backend-poolen och konfigurera en utgående regel för att program mera NAT och rensa utgående anslutningar så att de härstammar från en speciell offentlig IP-adress. Du kan också använda ett offentligt IP-prefix för att förenkla White-List källan för utgående anslutningar.

1. Skapa en offentlig Standard Load Balancer.
2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool för den offentliga Load Balancer.
3. Konfigurera en utgående regel för den offentliga Load Balancer till program utgående NAT för dessa virtuella datorer.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Utgående NAT för interna Standard Load Balancer scenarier

När du använder en intern Standard Load Balancer är utgående NAT inte tillgängligt förrän den utgående anslutningen uttryckligen har deklarerats. Du kan definiera utgående anslutningar med en utgående regel för att skapa utgående anslutningar för virtuella datorer bakom en intern Standard Load Balancer med dessa steg:

1. Skapa en offentlig Standard Load Balancer.
2. Skapa en backend-pool och placera de virtuella datorerna i en backend-pool för de offentliga Load Balancer utöver de interna Load Balancer.
3. Konfigurera en utgående regel för den offentliga Load Balancer till program utgående NAT för dessa virtuella datorer.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Aktivera både TCP & UDP-protokoll för utgående NAT med en offentlig Standard Load Balancer

- När du använder en offentlig Standard Load Balancer matchas den automatiska utgående NAT-programmeringen med transport protokollet i belastnings Utjämnings regeln.  

   1. Inaktivera utgående SNAT i belastnings Utjämnings regeln.
   2. Konfigurera en utgående regel på samma Load Balancer.
   3. Återanvänd backend-poolen som redan används av dina virtuella datorer.
   4. Ange "protokoll": "alla" som en del av regeln för utgående trafik.

- När endast inkommande NAT-regler används tillhandahålls ingen utgående NAT.

   1. Placera virtuella datorer i en backend-pool.
   2. Definiera en eller flera klient dels-IP-konfigurationer med offentliga IP-adresser eller offentliga IP-prefix.
   3. Konfigurera en utgående regel på samma Load Balancer.
   4. Ange "protokoll": "alla" som en del av regeln för utgående trafik

## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara tillfälliga portar per IP-adress för klient delen är 64 000.
- Intervallet för den konfigurerbara tids gränsen för utgående inaktivitet är 4 till 120 minuter (240 till 7200 sekunder).
- Load Balancer stöder inte ICMP för utgående NAT.
- Utgående regler kan bara tillämpas på en primär IP-konfiguration för ett nätverkskort.  Det finns stöd för flera nätverkskort.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du använder [Load Balancer för utgående anslutningar](load-balancer-outbound-connections.md).
- Läs mer om [standard Load Balancer](load-balancer-standard-overview.md).
- Lär dig mer om [dubbelriktad TCP-återställning vid inaktiv tids gräns](load-balancer-tcp-reset.md).
- [Konfigurera utgående regler med Azure CLI 2,0](configure-load-balancer-outbound-cli.md).
