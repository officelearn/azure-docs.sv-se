---
title: Scenarier för privat Azure DNS-zoner | Microsoft Docs
description: Översikt över vanliga scenarier för att använda Azure privata DNS-zoner.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: de543913d4f8264fa8e5b3bca0c510c99c479cae
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32771879"
---
# <a name="azure-dns-private-zones-scenarios"></a>Scenarier för Azure privata DNS-zoner
Azure DNS privata zoner ange namnmatchning i ett virtuellt nätverk samt mellan virtuella nätverk. I den här artikeln titta vi på några vanliga scenarier som kan genomföras med den här funktionen. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenario: Namnmatchning begränsade till ett enda virtuellt nätverk
I det här scenariot har du ett virtuellt nätverk i Azure som innehåller ett antal Azure-resurser, inklusive virtuella datorer (VM). Du vill lösa resurser i det virtuella nätverket via ett visst domännamn (DNS-zon) och du måste namnmatchningen ska privata och inte kan nås från internet. För de virtuella datorerna inom VNET måste dessutom Azure att automatiskt registrera dem i DNS-zonen. 

Det här scenariot illustreras nedan. Virtuellt nätverk med namnet ”A” innehåller två virtuella datorer (VNETA VM1 och VNETA VM2). Var och en av dessa ha privata IP-adresser som är associerade. När du skapar en zon för privat namnet contoso.com och länka det här virtuella nätverket som ett virtuellt nätverk för registrering av skapas Azure DNS automatiskt två A-poster i zonen som visas. DNS-frågor från VNETA VM1 att lösa VNETA VM2.contoso.com visas nu ett DNS-svar som innehåller VNETA VM2 privat IP. Dessutom får en omvänd DNS-fråga (PTR) för privat IP VNETA VM1 (10.0.0.1) utfärdat av VNETA VM2 ett DNS-svar som innehåller namnet på VNETA VM1 som förväntat. 

![Enkel lösning för virtuella nätverk](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenario: Namnmatchning mellan virtuella nätverk

Det här scenariot är ofta fallet där du måste associera en privat zon med flera virtuella nätverk. Det här scenariot rymmer arkitekturer, till exempel NAV och eker-modell där det finns ett centralt virtuella Hub-nätverk som virtuella nätverk är kopplade till vilken flera andra ekrar. Central hubb virtuella nätverket kan länkas som registrering av virtuellt nätverk till en privat zon och ekrar virtuella nätverk kan länkas som upplösning virtuella nätverk. 

Följande diagram visar en enkel version av det här scenariot där det finns bara två virtuella nätverk - A och b En utses ett virtuellt nätverk för registrering och B är utsedd till ett virtuellt nätverk för namnmatchning. Avsikten är att båda virtuella nätverk för att dela en gemensam zonen contoso.com. När zonen skapas och upplösning och registrering av virtuella nätverk är kopplade till zonen, kommer Azure automatiskt registrera DNS-poster för de virtuella datorerna (VNETA VM1 och VNETA VM2) från det virtuella nätverket A. Du kan även manuellt lägga till DNS-poster i zonen för virtuella datorer i det virtuella nätverket för lösning B. Observera följande beteende för framåtriktad och omvänd DNS-frågor med de här inställningarna:
* En DNS-fråga från VNETB VM1 i upplösning virtuella nätverk B, för VNETA VM1.contoso.com får ett DNS-svar som innehåller VNETA VM1 privat IP.
* En omvänd DNS-PTR-frågan från VNETB VM2 i upplösning virtuella nätverk B för 10.1.0.1, får ett DNS-svar som innehåller FQDN VNETB-VM1.contoso.com. Anledningen är att omvänd DNS-frågor är begränsade till samma virtuella nätverk. 
* En omvänd DNS-PTR-frågan från VNETB VM3 i upplösning virtuella nätverk B för 10.0.0.1, får NXDOMAIN. Anledningen är att omvänd DNS-frågor endast är begränsade till samma virtuella nätverk. 


![Lösningar för flera virtuella nätverk](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenario: Delning funktioner

I det här scenariot har du ett användningsfall där du vill utnyttja olika DNS-matchningen beroende på där klienten placeras (inuti Azure eller ut på internet) för samma DNS-zonen. Till exempel du kanske en privata och offentliga version av programmet som har olika funktioner eller -beteende, men du vill använda samma domännamn för båda versionerna. Det här scenariot kan realiseras med Azure DNS genom att skapa en offentlig DNS-zon som en privat zon med samma namn.

Följande diagram visar det här scenariot. Du har ett virtuellt nätverk A som har två virtuella datorer (VNETA VM1 och VNETA VM2) som har både privata IP-adresser och offentliga IP-adresser tilldelas. Du skapar en offentlig DNS-zon som kallas contoso.com och registrera den offentliga IP-adresser för dessa virtuella datorer som DNS-poster i zonen. Du kan också skapa ett privat DNS-zonen contoso.com som anger att en registrering av virtuellt nätverk kallas även. Azure registrerar automatiskt de virtuella datorerna som A-poster i zonen privat, pekar på sina privata IP-adresser.

Nu när en internet-klient skickar en DNS-fråga för att leta upp VNETA VM1.contoso.com, returnerar Azure den offentliga IP-adressposten från zonen offentliga. Om samma DNS-frågan har utfärdats från en annan virtuell dator (till exempel: VNETA VM2) i samma virtuella nätverk A, Azure returnerar privata IP-adressposten från zonen privata. 

![Dela Brian upplösning](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Nästa steg
Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner).

Lär dig hur du [skapar ett privat DNS-zon](./private-dns-getstarted-powershell.md) i Azure DNS.

Lär dig mer om DNS-zoner och poster genom att besöka: [DNS-zoner och innehåller en översikt över](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

