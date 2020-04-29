---
title: Integrera Azure Firewall med Azure Standard Load Balancer
description: Du kan integrera en Azure-brandvägg i ett virtuellt nätverk med en Azure-Standard Load Balancer (antingen offentlig eller intern).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78196741"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrera Azure Firewall med Azure Standard Load Balancer

Du kan integrera en Azure-brandvägg i ett virtuellt nätverk med en Azure-Standard Load Balancer (antingen offentlig eller intern). 

Den föredragna designen är att integrera en intern belastningsutjämnare med din Azure-brandvägg, eftersom det här är en mycket enklare design. Du kan använda en offentlig belastningsutjämnare om du redan har en distribution och vill hålla den på plats. Du måste dock vara medveten om ett problem med asymmetrisk routning som kan bryta funktioner med det offentliga scenariot för belastnings utjämning.

Mer information om Azure Load Balancer finns i [Vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Offentlig belastningsutjämnare

Med en offentlig belastningsutjämnare distribueras belastningsutjämnaren med en offentlig IP-adress för klient delen.

### <a name="asymmetric-routing"></a>Asymmetrisk routning

Asymmetrisk routning är den plats där ett paket tar en väg till målet och tar en annan sökväg när den återgår till källan. Det här problemet uppstår när ett undernät har en standard väg som går till brand väggens privata IP-adress och du använder en offentlig belastningsutjämnare. I det här fallet tas den inkommande belastnings Utjämnings trafiken emot via dess offentliga IP-adress, men retur vägen går genom brand väggens privata IP-adress. Eftersom brand väggen är tillstånds känslig, släpps det returnerade paketet eftersom brand väggen inte är medveten om en sådan etablerad session.

### <a name="fix-the-routing-issue"></a>Åtgärda problemet med routningen

När du distribuerar en Azure-brandvägg i ett undernät är ett steg att skapa en standard väg för det undernät som dirigerar paket genom brand väggens privata IP-adress som finns på AzureFirewallSubnet. Mer information finns i [Självstudier: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](tutorial-firewall-deploy-portal.md#create-a-default-route).

När du introducerar brand väggen i ditt scenario för belastningsutjämnare vill du att din Internet trafik ska komma genom brand väggens offentliga IP-adress. Därifrån tillämpar brand väggen sina brand Väggs regler och överför paketen till den offentliga IP-adressen för belastningsutjämnaren. Det är där problemet uppstår. Paket kommer in i brand väggens offentliga IP-adress, men återgår till brand väggen via den privata IP-adressen (med standard vägen).
Undvik det här problemet genom att skapa ytterligare en värd väg för brand väggens offentliga IP-adress. Paket som går till brand väggens offentliga IP-adress dirigeras via Internet. Detta förhindrar standard vägen till brand väggens privata IP-adress.

![Asymmetrisk routning](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exempel på routningstabell

Följande vägar är till exempel för en brand vägg på offentlig IP-20.185.97.136 och privat IP-10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Routningstabell](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Exempel på NAT-regel

I följande exempel översätter en NAT-regel RDP-trafik till brand väggen vid 20.185.97.136 över till belastningsutjämnaren på 20.42.98.220:

> [!div class="mx-imgBorder"]
> ![NAT-regel](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Hälsotillståndsavsökningar

Kom ihåg att du behöver en webb tjänst som körs på värdarna i belastningsutjämnaren om du använder TCP-hälsosökningar till port 80 eller HTTP/HTTPS-avsökningar.

## <a name="internal-load-balancer"></a>Intern lastbalanserare

Med en intern belastningsutjämnare distribueras belastningsutjämnaren med en privat klient dels-IP-adress.

Det finns inget problem med asymmetrisk routning i det här scenariot. Inkommande paket kommer till brand väggens offentliga IP-adress, översätts till belastningsutjämnarens privata IP-adress och återgår sedan till brand väggens privata IP-adress med samma retur Sök väg.

Så du kan distribuera det här scenariot på liknande sätt som i det offentliga scenariot för belastnings utjämning, men utan att behöva använda den offentliga brand väggens värd väg för IP-adresser.

## <a name="additional-security"></a>Ytterligare säkerhet

Om du vill förbättra säkerheten i ditt belastningsutjämnade scenario kan du använda nätverks säkerhets grupper (NSG: er).

Du kan till exempel skapa en NSG i backend-undernätet där de belastningsutjämnade virtuella datorerna finns. Tillåt inkommande trafik från brand väggens IP-adress/port.

![Nätverkssäkerhetsgrupp](media/integrate-lb/nsg-01.png)

Mer information om NSG: er finns i [säkerhets grupper](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).