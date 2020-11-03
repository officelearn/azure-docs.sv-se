---
title: Utgående proxy-Azure Load Balancer
description: Beskriver hur Azure Load Balancer används som proxy för utgående Internet anslutning
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 185bb47677e978a3098f39024995da6399f90658
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241777"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Utgående proxy-Azure Load Balancer

En Azure Load Balancer kan användas som proxy för utgående Internet-anslutning. Belastningsutjämnaren tillhandahåller utgående anslutning för Server dels instanserna. 

Den här konfigurationen använder **käll Network Address Translation (SNAT)**. SNAT skriver om IP-adressen för Server delen till den offentliga IP-adressen för belastningsutjämnaren. 

SNAT aktiverar **IP-maskerande** av Server dels instansen. Den här maskerade tjänsten förhindrar att externa källor har en direkt adress till Server dels instanserna. 

Att dela en IP-adress mellan server dels instanser minskar kostnaden för statiska offentliga IP-adresser och har stöd för scenarier som fören klar IP-listor med trafik från kända offentliga IP-adresser. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Dela portar över resurser

Om Server dels resurser i en belastningsutjämnare inte har offentliga IP-adresser på instans nivå (ILPIP), upprättar de utgående anslutningar via klient delens IP-adress för den offentliga belastningsutjämnaren.

Portar används för att generera unika identifierare som används för att underhålla distinkta flöden. Internet använder en fem tuple för att ge den här skillnaden.

Fem tuple består av:

* Mål-IP-adress
* Målport
* Käll-IP-adress
* Käll port och protokoll för att ge den här skillnaden.

Om en port används för inkommande anslutningar kommer den att ha en **lyssnare** för inkommande anslutnings begär Anden på den porten och kan inte användas för utgående anslutningar. 

För att upprätta en utgående anslutning måste en **tillfällig port** användas för att tillhandahålla målet med en port som används för att kommunicera och underhålla ett distinkt trafikflöde. 

Varje IP-adress har 65 535 portar. De första 1024 portarna är reserverade som **system portar**. Varje port kan antingen användas för inkommande eller utgående anslutningar för TCP och UDP. 

Av de återstående portarna ger Azure 64 000 för användning som **tillfälliga portar**. När en IP-adress läggs till som en IP-konfiguration för klient delen kan dessa tillfälliga portar användas för SNAT.

Via utgående regler kan de här SNAT-portarna distribueras till Server dels instanser för att de ska kunna dela den offentliga IP-adressen för belastningsutjämnaren för utgående anslutningar.

Nätverk på värden för varje server dels instans kommer att göra SNAT till paket som ingår i en utgående anslutning. Värden skriver om käll-IP-adressen till en av de offentliga IP-adresserna. Värden skriver om käll porten för varje utgående paket till någon av SNAT-portarna.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Uttömda portar

Varje anslutning till samma mål-IP-adress och mål Port kommer att använda en SNAT-port. Den här anslutningen upprätthåller ett distinkt **trafikflöde** från Server dels instansen eller **klienten** till en **Server**. Den här processen ger servern en distinkt port som trafiken ska riktas mot. Utan den här processen är klient datorn inte medveten om vilket flöde ett paket ingår i.

Föreställ dig att du har flera webbläsare att gå till https://www.microsoft.com , vilket är:

* Mål-IP = 23.53.254.142
* Målport = 443
* Protokoll = TCP

Utan olika mål portar för retur trafiken (den SNAT-port som används för att upprätta anslutningen) har klienten inget sätt att separera ett frågeresultat från en annan.

Utgående anslutningar kan burst-överföras. En server dels instans kan allokeras för otillräckliga portar. Om du inte aktiverar **åter användning av anslutningar** ökar risken för att antalet SNAT- **portar** är.

Nya utgående anslutningar till en mål-IP-adress Miss fungerar när port överbelastningen sker. Anslutningar kommer att lyckas när en port blir tillgänglig. Den här utbelastningen inträffar när 64 000-portarna från en IP-adress sprids tunn över flera Server dels instanser. Information om hur du minskar antalet SNAT-portar finns i [fel söknings guiden](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

För TCP-anslutningar använder belastningsutjämnaren en enskild SNAT-port för varje mål-IP och port. Den här MultiUse aktiverar flera anslutningar till samma mål-IP med samma SNAT-port. Den här MultiUse är begränsad om anslutningen inte är till olika mål portar.

För UDP-anslutningar använder belastningsutjämnaren en **port-begränsad kon NAT** -algoritm, som förbrukar en SNAT-port per mål-IP, oavsett mål porten. 

En port återanvänds för ett obegränsat antal anslutningar. Porten återanvänds endast om mål-IP-adressen eller porten är annorlunda.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Port tilldelning

Varje offentlig IP-adress som tilldelas till klient delens IP-adress för belastningsutjämnaren ges 64 000 SNAT-portar för dess medlemmar i Server delens pool. Portar kan inte delas med medlemmar i backend-poolen. Ett antal SNAT-portar kan bara användas av en enda server dels instans för att säkerställa att RETUR paket dirigeras korrekt. 

Vi rekommenderar att du använder en explicit utgående regel för att konfigurera SNAT-port tilldelning. Den här regeln maximerar antalet SNAT-portar som varje server dels instans har tillgänglig för utgående anslutningar. 

Om du använder automatisk allokering av utgående SNAT genom en belastnings Utjämnings regel definierar fördelnings tabellen din port tilldelning.

Följande <a name="snatporttable"></a> tabell visar förallokeringar av SNAT-portar för nivåer av backend-poolens storlekar:

| Poolstorlek (VM-instanser) | Förallokerade SNAT-portar per IP-konfiguration |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 – 1000 | 32 | 

>[!NOTE]
> Om du har en backend-pool med en Max storlek på 10 kan varje instans ha 64000/10 = 6 400 portar om du definierar en explicit utgående regel. Enligt tabellen ovan kommer bara att ha 1 024 om du väljer automatisk allokering.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Utgående regler och Virtual Network NAT

Azure Load Balancer utgående regler och Virtual Network NAT är alternativ som är tillgängliga för utgående från ett virtuellt nätverk.

Mer information om utgående regler finns i [utgående regler](outbound-rules.md).

Mer information om Azure Virtual Network NAT finns i [Vad är azure Virtual Network NAT](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Villkor

*   Portar frigörs efter 15 sekunder om en **TCP** -per-och e-sekund tas emot eller skickas
*   Portar frigörs efter 240 sekunder om en **FINACK** tas emot eller skickas
*   När en anslutning är inaktiv utan att några nya paket skickas, frigörs portarna efter 4 – 120 minuter.
  * Det här tröskelvärdet kan konfigureras via utgående regler.
*   Varje IP-adress ger 64 000 portar som kan användas för SNAT.
*   Varje port kan användas för både TCP-och UDP-anslutningar till en mål-IP-adress
  * En UDP SNAT-port krävs om mål porten är unik eller inte. För varje UDP-anslutning till en mål-IP används en UDP SNAT-port.
  * En TCP SNAT-port kan användas för flera anslutningar till samma mål-IP förutsatt att mål portarna är olika.
*   SNAT-belastningen inträffar när en server dels instans tar slut på de fick SNAT-portarna. En belastningsutjämnare kan fortfarande ha oanvända SNAT-portar. Om en server dels instans använder SNAT-portar som överstiger de tilldelade SNAT-portarna kan den inte upprätta nya utgående anslutningar.

## <a name="next-steps"></a>Nästa steg

*   [Felsök utgående anslutnings fel på grund av SNAT-belastning](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Granska SNAT-mått](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) och bekanta dig med det korrekta sättet att filtrera, dela och visa dem.

