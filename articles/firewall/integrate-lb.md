---
title: Integrera Azure Firewall med Azure Standard Load Balancer
description: Du kan integrera en Azure-brandvägg i ett virtuellt nätverk med en Azure Standard Load Balancer (antingen offentlig eller intern).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196741"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrera Azure Firewall med Azure Standard Load Balancer

Du kan integrera en Azure-brandvägg i ett virtuellt nätverk med en Azure Standard Load Balancer (antingen offentlig eller intern). 

Den föredragna designen är att integrera en intern belastningsutjämnare med din Azure-brandvägg, eftersom det här är en mycket enklare design. Du kan använda en offentlig belastningsutjämnare om du redan har en distribuerad och du vill behålla den på plats. Du måste dock vara medveten om ett asymmetriskt routningsproblem som kan bryta funktionaliteten med scenariot för offentlig belastningsutjämnare.

Mer information om Azure Load Balancer finns i [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Offentlig belastningsutjämnare

Med en offentlig belastningsutjämnare distribueras belastningsutjämnaren med en offentlig IP-adress för klientdel.

### <a name="asymmetric-routing"></a>Asymmetrisk routning

Asymmetrisk routning är där ett paket tar en sökväg till målet och tar en annan sökväg när du återvänder till källan. Det här problemet uppstår när ett undernät har en standardväg som går till brandväggens privata IP-adress och du använder en offentlig belastningsutjämnare. I det här fallet tas den inkommande belastningsutjämnartrafiken emot via sin offentliga IP-adress, men retursökvägen går genom brandväggens privata IP-adress. Eftersom brandväggen är tillståndskänslig, droppar det återvändande paketet eftersom brandväggen inte är medveten om en sådan etablerad session.

### <a name="fix-the-routing-issue"></a>Åtgärda routningsproblemet

När du distribuerar en Azure-brandvägg i ett undernät är ett steg att skapa en standardväg för undernätet som dirigerar paket via brandväggens privata IP-adress som finns på AzureFirewallSubnet. Mer information finns i [Självstudiekurs: Distribuera och konfigurera Azure-brandväggen med Azure-portalen](tutorial-firewall-deploy-portal.md#create-a-default-route).

När du introducerar brandväggen i ditt belastningsutjämna scenario vill du att internettrafiken ska komma in genom brandväggens offentliga IP-adress. Därifrån tillämpar brandväggen sina brandväggsregler och NATs paketen till din belastningsutjämnares offentliga IP-adress. Det är här problemet uppstår. Paket anländer till brandväggens offentliga IP-adress, men återgår till brandväggen via den privata IP-adressen (med standardvägen).
Undvik det här problemet genom att skapa ytterligare en värdväg för brandväggens offentliga IP-adress. Paket som går till brandväggens offentliga IP-adress dirigeras via Internet. På så sätt undviker du att ta standardvägen till brandväggens privata IP-adress.

![Asymmetrisk routning](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exempel på flödestabell

Följande vägar är till exempel för en brandvägg på den offentliga IP-adressen 20.185.97.136 och privat IP-adress 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Routningstabell](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>EXEMPEL PÅ NAT-regel

I följande exempel översätter en NAT-regel RDP-trafik till brandväggen på 20.185.97.136 över till belastningsutjämnaren på 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![NAT-regel](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Kom ihåg att du måste ha en webbtjänst som körs på värdarna i belastningsutjämnarpoolen om du använder TCP-hälsoavsökningar för port 80 eller HTTP/HTTPS-avsökningar.

## <a name="internal-load-balancer"></a>Intern lastbalanserare

Med en intern belastningsutjämnare distribueras belastningsutjämnaren med en privat klientdels-IP-adress.

Det finns inget asymmetriskt routningsproblem med det här scenariot. De inkommande paketen anländer till brandväggens offentliga IP-adress, översätts till belastningsutjämnarens privata IP-adress och återgår sedan till brandväggens privata IP-adress med samma retursökväg.

Därför kan du distribuera det här scenariot som liknar scenariot med offentlig belastningsutjämnare, men utan att det behövs för den offentliga IP-adressvärdensväg för brandväggen.

## <a name="additional-security"></a>Ytterligare säkerhet

Om du vill öka säkerheten i det belastningsbalanserade scenariot ytterligare kan du använda nätverkssäkerhetsgrupper (NSG).

Du kan till exempel skapa en NSG på serverdelsundernätet där de belastningsbalanserade virtuella datorerna finns. Tillåt inkommande trafik som kommer från brandväggens IP-adress/port.

![Nätverkssäkerhetsgrupp](media/integrate-lb/nsg-01.png)

Mer information om NSG finns i [Säkerhetsgrupper](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).