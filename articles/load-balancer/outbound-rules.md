---
title: Utgående regler Azure Load Balancer
description: Den här artikeln förklarar hur du konfigurerar utgående regler för att styra utgående av Internet trafik med Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002656"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Utgående regler Azure Load Balancer

Med utgående regler kan du konfigurera offentliga standard Load Balancer utgående SNAT (käll Network Address Translation). Med den här konfigurationen kan du använda en eller flera offentliga IP-adresser för belastningsutjämnaren som proxy.

Den här konfigurationen aktiverar:

* IP-maskerad
* Förenkla dina listor över tillåtna.
* Minskar antalet offentliga IP-resurser för distribution.

Med utgående regler har du fullständig deklarativ kontroll över utgående Internet anslutning. Med utgående regler kan du skala och finjustera den här funktionen för dina behov. 

Utgående regler kommer bara att följas om den virtuella datorns VM inte har en offentlig IP-adress på instans nivå (ILPIP).

![Load Balancer utgående regler](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Med utgående regler kan du uttryckligen definiera beteendet för utgående **SNAT** .

Med utgående regler kan du styra:

* **Vilka virtuella datorer som översätts till offentliga IP-adresser.**
     * Två regler var backend-pool A använder IP-adress A och B, backend-pool B använder IP-adress C och D.
* **Hur utgående SNAT-portar anges.**
     * Backend-pool B är den enda poolen som gör utgående anslutningar, ge alla SNAT-portar till backend-pool B och ingen till backend-pool A.
* **Vilka protokoll som ska tillhandahålla utgående översättning för.**
     * Backend-pool B behöver UDP-portar för utgående. Backend-poolen kräver TCP. Ge TCP-portar till A-och UDP-portar till B.
* **Vilken varaktighet som ska användas för utgående timeout för utgående anslutning (4-120 minuter).**
     * Om det finns tids krävande anslutningar med keepalive-anslutningar ska du reservera inaktiva portar för tids krävande anslutningar i upp till 120 minuter. Anta att inaktuella anslutningar är övergivna och frigör portar på 4 minuter för nya anslutningar 
* **Om du vill skicka en TCP-återställning vid inaktivitet.**
     * När tids gränsen för inaktiva anslutningar är slut, skickar vi en TCP-till-klient och-server så att de vet att flödet överges?

## <a name="outbound-rule-definition"></a>Regel definition för utgående trafik

Utgående regler följer samma välbekanta syntax som belastnings utjämning och inkommande NAT- **regler:**  +  Server dels**parametrar**i  +  **backend-poolen**. 

En utgående regel konfigurerar utgående NAT för _alla virtuella datorer som identifieras av backend-poolen_ som ska översättas till _klient delen_.  

_Parametrarna_ ger ytterligare detaljerad kontroll över den utgående NAT-algoritmen.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skala utgående NAT med flera IP-adresser

Varje ytterligare IP-adress som tillhandahålls av en klient del ger ytterligare 64 000 tillfälliga portar för belastningsutjämnare som ska användas som SNAT-portar. 

Använd flera IP-adresser för att planera för storskaliga scenarier. Använd utgående regler för att minska [SNAT-belastningen](troubleshoot-outbound-connection.md#snatexhaust). 

Du kan också använda ett [offentligt IP-prefix](https://aka.ms/lbpublicipprefix) direkt med en utgående regel. 

Ett offentligt IP-prefix ökar skalningen av distributionen. Prefixet kan läggas till i listan över tillåtna flöden för flöden som kommer från dina Azure-resurser. Du kan konfigurera en IP-konfiguration för klient delen i belastningsutjämnaren för att referera till ett offentligt IP-adressprefix.  

Belastningsutjämnaren har kontroll över det offentliga IP-prefixet. Regeln för utgående trafik använder automatiskt alla offentliga IP-adresser som finns i det offentliga IP-prefixet för utgående anslutningar. 

Var och en av IP-adresserna inom det offentliga IP-prefixet ger ytterligare 64 000 tillfälliga portar per IP-adress för belastningsutjämnare som ska användas som SNAT-portar.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Utgående tids gräns för flödes inaktivitet och TCP-återställning

Utgående regler tillhandahåller en konfigurations parameter för att kontrol lera utgående tids gräns för utgående trafik och matcha den mot ditt programs behov. Timeout för utgående inaktivitet är som standard 4 minuter. Mer information finns i [Konfigurera tids gränser för inaktivitet](load-balancer-tcp-idle-timeout.md). 

Standard beteendet för belastningsutjämnare är att släppa flödet tyst när tids gränsen för utgående inaktivitet har nåtts. `enableTCPReset`Parametern möjliggör ett förutsägbart program beteende och kontroll. Parametern avgör om dubbelriktad TCP-återställning (TCP per-) ska skickas vid tids gränsen för timeout vid utgående inaktivitet. 

Granska [TCP-återställning vid inaktivitet](https://aka.ms/lbtcpreset) för mer information, inklusive region tillgänglighet.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Skydda och kontrol lera utgående anslutningar explicit

Regler för belastnings utjämning ger automatisk programmering av utgående NAT. Vissa scenarier eller kräver att du inaktiverar automatisk programmering av utgående NAT med belastnings Utjämnings regeln. Genom att inaktivera via regeln kan du styra eller förfina beteendet.  

Du kan använda den här parametern på två sätt:

1. Förebyggande av inkommande IP-adress för utgående SNAT. Inaktivera utgående SNAT i belastnings Utjämnings regeln.
  
2. Justera de utgående **SNAT** -parametrarna för en IP-adress som används för inkommande och utgående samtidigt. Den automatiska utgående NAT-filen måste inaktive ras för att tillåta en utgående regel att ta kontroll. Om du vill ändra SNAT-port tilldelningen för en adress som också används för inkommande `disableOutboundSnat` måste parametern vara inställd på True. 

Det går inte att konfigurera en utgående regel om du försöker omdefiniera en IP-adress som används för inkommande.  Inaktivera utgående NAT för belastnings Utjämnings regeln först.

>[!IMPORTANT]
> Den virtuella datorn har inte någon utgående anslutning om du anger den här parametern till true och inte har någon utgående regel för att definiera utgående anslutning.  Vissa åtgärder för din virtuella dator eller ditt program kan bero på att det finns en utgående anslutning tillgänglig. Se till att du förstår beroendena för ditt scenario och att du anser att du har gjort den här ändringen.

Ibland är det inte oönskat för en virtuell dator att skapa ett utgående flöde. Det kan finnas ett krav för att hantera vilka destinationer som tar emot utgående flöden eller vilka destinationer som startar inkommande flöden. Använd [nätverks säkerhets grupper](../virtual-network/security-overview.md) för att hantera de destinationer som den virtuella datorn når. Använd NSG: er för att hantera vilka offentliga destinationer som startar inkommande flöden.

När du tillämpar en NSG på en belastningsutjämnad virtuell dator, bör du tänka på [tjänst taggarna](../virtual-network/security-overview.md#service-tags) och [Standard säkerhets reglerna](../virtual-network/security-overview.md#default-security-rules). 

Se till att den virtuella datorn kan ta emot hälso avsöknings begär Anden från Azure Load Balancer.

Om en NSG blockerar hälso avsöknings begär Anden från AZURE_LOADBALANCER-Standardtaggen Miss lyckas din VM-avsökning och den virtuella datorn är markerad som ej tillgänglig. Belastningsutjämnaren slutar skicka nya flöden till den virtuella datorn.

## <a name="limitations"></a>Begränsningar

- Det maximala antalet användbara tillfälliga portar per IP-adress för klient delen är 64 000.
- Intervallet för den konfigurerbara tids gränsen för utgående inaktivitet är 4 till 120 minuter (240 till 7200 sekunder).
- Load Balancer stöder inte ICMP för utgående NAT.
- Utgående regler kan bara tillämpas på en primär IP-konfiguration för ett nätverkskort.  Du kan inte skapa en utgående regel för den sekundära IP-adressen för en virtuell dator eller NVA. Det finns stöd för flera nätverkskort.
- Alla virtuella datorer i en **tillgänglighets uppsättning** måste läggas till i backend-poolen för utgående anslutning. 
- Alla virtuella datorer i en **skalnings uppsättning för virtuella** datorer måste läggas till i backend-poolen för utgående anslutning.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure standard Load Balancer](load-balancer-overview.md)
- Se våra [vanliga frågor och svar om Azure Load Balancer](load-balancer-faqs.md)

