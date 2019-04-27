---
title: Scenarier för Azure DNS Private Zones
description: Översikt över vanliga scenarier för att använda Azure DNS Private Zones.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 03/15/2018
ms.author: victorh
ms.openlocfilehash: 409595febded7b242eae876ebb2cb35ae4999e5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686862"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS Private Zones scenarier
Azure DNS Private Zones tillhandahåller namnmatchning i ett virtuellt nätverk samt mellan virtuella nätverk. I den här artikeln ska titta vi på några vanliga scenarier som kan genomföras med hjälp av den här funktionen. 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>Scenario: Namnmatchning begränsade till ett enda virtuellt nätverk
I det här scenariot har du ett virtuellt nätverk i Azure som innehåller ett antal Azure-resurser, inklusive virtuella datorer (VM). Du vill lösa resurser från i det virtuella nätverket via ett visst domännamn (DNS-zon) och du behöver namnmatchning för att vara privat och inte kan nås från internet. För de virtuella datorerna i det virtuella nätverket måste dessutom Azure för att automatiskt registrera dem i DNS-zonen. 

Det här scenariot beskrivs nedan. Virtuellt nätverk med namnet ”A” innehåller två virtuella datorer (VNETA VM1 och VNETA VM2). Var och en av dessa har privata IP-adresser som är associerade. När du skapar en privat zon med namnet contoso.com och länka det här virtuella nätverket som ett virtuellt nätverk för registrering, skapar Azure DNS automatiskt två A-poster i zonen som du ser. Nu, är VNETA-VM1 för att lösa VNETA VM2.contoso.com DNS-frågor får ett DNS-svar som innehåller VNETA VM2 privat IP. Dessutom får en omvänd DNS-fråga (PTR) för den privata IP av VNETA-VM1 (10.0.0.1) som utfärdats av VNETA VM2 ett DNS-svar som innehåller namnet på VNETA-VM1 är som förväntat. 

![Enkel lösning för virtuella nätverk](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>Scenario: Namnmatchning för virtuella nätverk

Det här scenariot är vanligare fallet där du vill koppla en privat zon med flera virtuella nätverk. Det här scenariot kan passa arkitekturer, till exempel NAV och eker-modellen där det finns en central virtuella navnätverket virtuella nätverk är anslutna till vilken flera andra ekrar. Central hubbens virtuella nätverk kan länka som virtuellt registreringsnätverk till en privat zon och virtuella ekernätverk kan länkas som virtuella matchningsnätverk. 

Följande diagram visar en enkel version av det här scenariot där det finns bara två virtuella nätverk - A och B. A är utsedd till ett virtuellt nätverk för registrering och B är utsedd till ett virtuellt nätverk för matchning. Avsikten är att båda virtuella nätverken du delar en gemensam zonen contoso.com. När zonen skapas och de virtuella nätverken upplösning och registrering är länkade till zonen, Azure kommer automatiskt att registreras DNS-poster för de virtuella datorerna (VNETA VM1 och VNETA VM2) från det virtuella nätverket A. Du kan även manuellt lägga till DNS-poster i zonen för virtuella datorer i det virtuella matchningsnätverket B. Med den här konfigurationen kan du lägga märke till följande beteende för proxy och omvänd DNS-frågor:
* En DNS-fråga från VNETB VM1 i det virtuella matchningsnätverk B och för VNETA VM1.contoso.com får ett DNS-svar som innehåller VNETA VM1 privat IP.
* En omvänd DNS (PTR)-fråga från VNETB VM2 i det virtuella matchningsnätverk B och för 10.1.0.1, får ett DNS-svar som innehåller FQDN VNETB-VM1.contoso.com. Anledningen är att omvänd DNS-frågor är begränsade till samma virtuella nätverk. 
* En omvänd DNS (PTR)-fråga från VNETB VM3 i det virtuella matchningsnätverk B och för 10.0.0.1, får NXDOMAIN. Anledningen är att omvänd DNS-frågor endast är begränsade till samma virtuella nätverk. 


![Lösningar för flera virtuella nätverk](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>Scenario: Split-Horizon-funktioner

I det här scenariot har du ett användningsfall där du vill att utnyttja olika DNS-matchningen beroende på där klienten är placerad (inuti Azure eller ut på internet) för samma DNS-zonen. Exempel: du kan ha en privata och offentliga versionen av ditt program som har olika funktioner eller beteende, men du vill använda samma domännamn för båda versionerna. Det här scenariot kan realiseras med Azure DNS genom att skapa en offentlig DNS-zon som en privat zon med samma namn.

Följande diagram visar det här scenariot. Du har ett virtuellt nätverk A som har två virtuella datorer (VNETA VM1 och VNETA VM2) som har både privata IP-adresser och offentliga IP-adresser tilldelas. Du skapar en offentlig DNS-zon med namnet contoso.com och registrera den offentliga IP-adresser för dessa virtuella datorer som DNS-poster i zonen. Du kan också skapa en privat DNS-zon som kallas även contoso.com som anger att en virtuellt registreringsnätverk. Azure registrerar automatiskt de virtuella datorerna som A-poster i den privata zonen som pekar till sina privata IP-adresser.

Nu när en internet-klient skickar en DNS-fråga för att leta upp VNETA VM1.contoso.com, returneras Azure den offentliga IP-adressposten från offentlig zon. Om samma DNS-frågan har utfärdats från en annan virtuell dator (till exempel: VNETA-VM2) i samma virtuella nätverk A, kommer Azure att returnera privata IP-adressposten från den privata zonen. 

![Dela Brian upplösning](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>Nästa steg
Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner).

Lär dig hur du [skapa en privat DNS-zon](./private-dns-getstarted-powershell.md) i Azure DNS.

Läs mer om DNS-zoner och poster genom att besöka: [DNS-zoner och poster översikt](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

