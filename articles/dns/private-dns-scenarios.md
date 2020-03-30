---
title: Scenarier för privata zoner - Azure DNS
description: I den här artikeln kan du läsa mer om vanliga scenarier för att använda Privata Azure DNS-zoner.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: ab850adb2e9a25778d5f44ba711eb0762fe562c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939341"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenarier för Azure DNS Private Zones

Azure DNS Private Zones ger namnmatchning i ett virtuellt nätverk samt mellan virtuella nätverk. I den här artikeln tittar vi på några vanliga scenarier som kan realiseras med den här funktionen.

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenario: Namnmatchning begränsad till ett enda virtuellt nätverk
I det här fallet har du ett virtuellt nätverk i Azure som har ett antal Azure-resurser i det, inklusive virtuella datorer (VMs). Du vill matcha resurserna inifrån det virtuella nätverket via ett visst domännamn (DNS-zon) och du behöver namnmatchningen för att vara privat och inte tillgänglig från Internet. Dessutom, för de virtuella datorerna i det virtuella nätverket, behöver du Azure för att automatiskt registrera dem i DNS-zonen. 

Det här scenariot beskrivs nedan. Virtuellt nätverk med namnet "A" innehåller två virtuella datorer (VNETA-VM1 och VNETA-VM2). Var och en av dessa har privata IPs associerade. När du har skapat en privat zon med namnet contoso.com och länka det här virtuella nätverket som ett virtuellt nätverk för registrering skapar Azure DNS automatiskt två A-poster i zonen som avbildas. Nu DNS-frågor från VNETA-VM1 för att lösa VNETA-VM2.contoso.com får ett DNS-svar som innehåller den privata IP-adressen för VNETA-VM2. Dessutom får en omvänd DNS-fråga (PTR) för den privata IP-adressen för VNETA-VM1 (10.0.0.1) som utfärdas från VNETA-VM2 ett DNS-svar som innehåller namnet på VNETA-VM1, som förväntat. 

![Upplösning av ett enda virtuellt nätverk](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenario: Namnmatchning i virtuella nätverk

Det här scenariot är det vanligaste fallet där du behöver associera en privat zon med flera virtuella nätverk. Det här scenariot kan passa arkitekturer som Hub-and-Spoke-modellen där det finns ett centralt virtuellt hubbnätverk som flera andra virtuella ekrar är anslutna till. Det centrala virtuella hubben kan länkas som virtuellt nätverk för registrering till en privat zon, och de virtuella ekrarna kan länkas som virtuella lösningsnätverk. 

Följande diagram visar en enkel version av det här scenariot där det bara finns två virtuella nätverk - A och B. A betecknas som ett virtuellt nätverk för registrering och B betecknas som ett virtuellt lösningsnätverk. Avsikten är att båda virtuella nätverken ska dela en gemensam zon contoso.com. När zonen skapas och virtuella nätverk för lösning och registrering är länkade till zonen registrerar Azure automatiskt DNS-poster för de virtuella datorerna (VNETA-VM1 och VNETA-VM2) från det virtuella nätverket A. Du kan också manuellt lägga till DNS-poster i zonen för virtuella datorer i det virtuella nätverket Lösning B. Med den här inställningen kommer du att observera följande beteende för vidarebefordra och omvänd DNS-frågor:
* En DNS-fråga från VNETB-VM1 i det virtuella nätverket B för VNETA-VM1.contoso.com får ett DNS-svar som innehåller den privata IP-adressen för VNETA-VM1.
* En omvänd DNS-fråga (PTR) från VNETB-VM2 i det virtuella nätverket Upplösning B för 10.1.0.1 får ett DNS-svar som innehåller FQDN-VNETB-VM1.contoso.com.  
* En omvänd DNS-fråga (PTR) från VNETB-VM3 i det virtuella nätverket Upplösning B för 10.0.0.1 får NXDOMAIN. Anledningen är att omvända DNS-frågor endast begränsas till samma virtuella nätverk. 


![Flera virtuella nätverksupplösningar](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenario: Split-Horizon-funktionalitet

I det här fallet har du ett användningsfall där du vill förverkliga olika DNS-upplösningsbeteende beroende på var klienten sitter (inne i Azure eller ute på internet), för samma DNS-zon. Du kan till exempel ha en privat och offentlig version av ditt program som har olika funktioner eller beteenden, men du vill använda samma domännamn för båda versionerna. Det här scenariot kan realiseras med Azure DNS genom att skapa en offentlig DNS-zon samt en privat zon, med samma namn.

I följande diagram visas det här scenariot. Du har ett virtuellt nätverk A som har två virtuella datorer (VNETA-VM1 och VNETA-VM2) som har både privata IP-adresser och offentliga IP-adresser tilldelade. Du skapar en offentlig DNS-zon som kallas contoso.com och registrerar offentliga IPs för dessa virtuella datorer som DNS-poster inom zonen. Du kan också skapa en privat DNS-zon som också kallas contoso.com ange A som virtuellt nätverk för registrering. Azure registrerar automatiskt de virtuella datorerna som A-poster i den privata zonen och pekar på deras privata IPs.

Nu när en Internet-klient utfärdar en DNS-fråga för att slå upp VNETA-VM1.contoso.com returnerar Azure den offentliga IP-posten från den offentliga zonen. Om samma DNS-fråga utfärdas från en annan virtuell dator (till exempel VNETA-VM2) i samma virtuella nätverk A returneras den privata IP-posten från den privata zonen. 

![Dela Brian-upplösning](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Nästa steg
Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner).

Lär dig hur du [skapar en privat DNS-zon](./private-dns-getstarted-powershell.md) i Azure DNS.

Lär dig mer om DNS-zoner och poster genom att besöka: [ÖVERSIKT ÖVER DNS-zoner och poster](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

