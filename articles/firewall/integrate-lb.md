---
title: Integrera Azure-brandväggen med Azure Standard Load Balancer
description: Lär dig hur du integrerar Azure-brandvägg med Azure Standard Load Balancer
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193789"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Integrera Azure-brandväggen med Azure Standard Load Balancer

Du kan integrera en Azure-brandvägg i ett virtuellt nätverk med en Azure Standard Load Balancer (offentlig eller intern). 

Den föredragna designen är att integrera en intern belastningsutjämnare med din Azure-brandväggen, eftersom det här är en mycket enklare design. Du kan använda en offentlig belastningsutjämnare om du redan har ett distribuerat och du vill att förvara den på plats. Men behöver du känna till asymmetrisk routning problem som kan dela funktioner med den offentliga belastningsutjämningsscenario.

Läs mer om Azure Load Balancer [vad är Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Offentlig belastningsutjämnare

Belastningsutjämnaren har distribuerats med en offentlig klientdels-IP-adress med en offentlig belastningsutjämnare.

### <a name="asymmetric-routing"></a>Asymmetrisk routning

Asymmetrisk routning är där ett paket tar en väg till målet och tar en annan sökväg när tillbaka till källan. Det här problemet inträffar när ett undernät har en standardväg ska i brandväggen privata IP-adressen och du använder en offentlig belastningsutjämnare. I det här fallet den inkommande trafiken för load balancer tas emot via dess offentliga IP-adress, men den returnera sökvägen går igenom i brandväggen privat IP-adress. Eftersom brandväggen inte är tillståndskänsliga utelämnar det returnerade paketet eftersom brandväggen inte är medveten om en upprättad session.

### <a name="fix-the-routing-issue"></a>Åtgärda problemet Routning

När du distribuerar en Azure-brandvägg i ett undernät är ett steg att skapa en standardväg för undernätet dirigera paket via i brandväggen privata IP-adressen finns på AzureFirewallSubnet. Mer information finns i [Självstudie: Distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](tutorial-firewall-deploy-portal.md#create-a-default-route).

När du introducera brandväggen i din belastningsutjämningsscenario vill Internet-trafiken att komma via din brandvägg offentlig IP-adress. Därifrån kan gäller brandväggen dess brandväggsregler och NAT paketen till din belastningsutjämnarens offentliga IP-adress. Det här är där problemet uppstår. Paket som tas emot på i brandväggen offentlig IP-adress, men återgå till brandväggen via privata IP-adress (med standardvägen).
Undvik problemet genom att skapa en ytterligare värdrutt för i brandväggen offentlig IP-adress. Paketen i brandväggen offentlig IP-adress ska dirigeras via Internet. Detta förhindrar tar standardväg till i brandväggen privat IP-adress.

![Asymmetrisk routning](media/integrate-lb/Firewall-LB-asymmetric.png)

Till exempel är följande vägar för en brandvägg i offentliga IP-adressen 13.86.122.41 och privata IP-adressen 10.3.1.4.

![Routningstabell](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Intern lastbalanserare

Belastningsutjämnaren har distribuerats med en privat klientdels-IP-adress med en intern belastningsutjämnare.

Det finns inga asymmetriska routningsproblemet med det här scenariot. Inkommande paket når i brandväggen offentlig IP-adress, hämta översätts till belastningsutjämnarens privata IP-adressen och återgår sedan till i brandväggen privata IP-adressen med hjälp av samma returnerade sökvägen.

Därför kan du distribuera det här scenariot som liknar den offentliga belastningsutjämningsscenario, men utan att behöva brandväggen offentliga IP-adressen värdrutt.

## <a name="additional-security"></a>Ytterligare säkerhet

Om du vill förbättra säkerheten för ditt scenario för Utjämning av nätverksbelastning, kan du använda nätverkssäkerhetsgrupper (NSG).

Du kan till exempel skapa en NSG på backend-undernät där de virtuella datorerna Utjämning av nätverksbelastning finns. Tillåt inkommande trafik från brandväggen IP-adress och port.

Mer information om Nätverkssäkerhetsgrupper finns i [säkerhetsgrupper](../virtual-network/security-overview.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuera och konfigurera en brandvägg för Azure](tutorial-firewall-deploy-portal.md).