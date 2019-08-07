---
title: Brand Väggs tabeller – VMware-lösning av CloudSimple – Azure
description: Lär dig mer om CloudSimple privata moln brand Väggs tabeller och brand Väggs regler.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d25aa9252f061cee7f4cffdca42f00d84f719a3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812656"
---
# <a name="firewall-tables-overview"></a>Översikt över brand Väggs tabeller

En brand Väggs tabell visar regler för att filtrera nätverks trafik till och från privata moln resurser. Du kan tillämpa dem på ett VLAN eller undernät. Reglerna styr sedan nätverks trafiken mellan ett käll nätverk eller en IP-adress och ett mål nätverk eller en IP-adress.

## <a name="firewall-rules"></a>Brandväggsregler

I följande tabell beskrivs parametrarna i en brand Väggs regel.

| Egenskap | Information |
| ---------| --------|
| **Namn** | Ett namn som unikt identifierar brand Väggs regeln och dess syfte. |
| **prioritet** | Ett tal mellan 100 och 4096, med 100 som högsta prioritet. Regler bearbetas i prioritetsordning. När trafiken kommer över en regel matchning stoppar regel bearbetningen. Därför bearbetas inte alla regler som finns med lägre prioriteter som har samma attribut som regler med högre prioritet.  Ta hand om att undvika motstridiga regler. |
| **Tillstånds spårning** | Spårning kan vara tillstånds lös (privat moln, Internet eller VPN) eller tillstånds känslig (offentlig IP).  |
| **Protokoll** | Alternativen omfattar alla, TCP eller UDP. Om du behöver ICMP använder du valfri. |
| **Riktning** | Om regeln gäller för inkommande eller utgående trafik. |
| **Åtgärd** | Tillåt eller neka för den typ av trafik som definierats i regeln. |
| **Källa** | En IP-adress, CIDR-block (Classless Inter-Domain routing) (endast 10.0.0.0/24, till exempel) eller någon.  Genom att ange ett intervall, ett service tag eller en program säkerhets grupp kan du skapa färre säkerhets regler. |
| **Källport** | Port som nätverks trafiken kommer från.  Du kan ange en enskild port eller ett port intervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler. |
| **Mål** | En IP-adress, CIDR-block (Classless Inter-Domain routing) (endast 10.0.0.0/24, till exempel) eller någon.  Genom att ange ett intervall, ett service tag eller en program säkerhets grupp kan du skapa färre säkerhets regler.  |
| **Målport** | Port som nätverks trafiken flödar till.  Du kan ange en enskild port eller ett port intervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler.|

### <a name="stateless"></a>Tillståndslös

En tillstånds lös regel ser bara ut i enskilda paket och filtrerar dem utifrån regeln.  
Det kan krävas ytterligare regler för trafikflöde i omvänd riktning.  Använd tillstånds lösa regler för trafik mellan följande punkter:

* Undernät för privata moln
* Lokalt undernät och ett privat moln under nät
* Internet trafik från privata moln

### <a name="stateful"></a>Tillstånds känsliga

 En tillstånds känslig regel är medveten om de anslutningar som passerar genom den. En flödespost skapas för befintliga anslutningar. Kommunikation tillåts eller nekas baserat på flödespostens anslutningsstatus.  Använd den här regel typen för offentliga IP-adresser för att filtrera trafik från Internet.

### <a name="default-rules"></a>Standardregler

Följande standard regler skapas i alla brand Väggs tabeller.

|Priority|Namn|Tillstånds spårning|Direction|Trafik typ|Protocol|Source|Källport|Mål|Målport|Action|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Tillstånds känsliga|Utgående|Offentlig IP-eller Internet trafik|Alla|Any|Any|Any|Any|Allow|
|65001|deny-all-from-internet|Tillstånds känsliga|Inkommande|Offentlig IP-eller Internet trafik|Alla|Any|Any|Any|Any|Neka|
|65002|Tillåt-allt-till-intranät|Tillståndslös|Utgående|Intern eller VPN-trafik i privat moln|Alla|Any|Any|Any|Any|Allow|
|65003|Tillåt-allt-från-intranät|Tillståndslös|Inkommande|Intern eller VPN-trafik i privat moln|Alla|Any|Any|Any|Any|Allow|

## <a name="next-steps"></a>Nästa steg

* [Konfigurera brand Väggs tabeller och regler](https://docs.azure.cloudsimple.com/firewall/)