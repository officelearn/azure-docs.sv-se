---
title: Azure VMware-lösningar (AVS) – brand Väggs tabeller
description: Läs mer om brand Väggs tabeller och brand Väggs regler för AVS-privata moln.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 631ccb33e14586edce71752826f134c1e50819c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025052"
---
# <a name="firewall-tables-overview"></a>Översikt över brand Väggs tabeller

En brand Väggs tabell visar regler för att filtrera nätverks trafik till och från resurser i molnets privata moln. Du kan använda brand Väggs tabeller till ett VLAN/undernät. Reglerna styr nätverks trafiken mellan ett käll nätverk eller en IP-adress och ett mål nätverk eller en IP-adress.

## <a name="firewall-rules"></a>Brandväggsregler

I följande tabell beskrivs parametrarna i en brand Väggs regel.

| Egenskap | Information |
| ---------| --------|
| **Namn** | Ett namn som unikt identifierar brand Väggs regeln och dess syfte. |
| **Prioritet** | Ett tal mellan 100 och 4096, med 100 som högsta prioritet. Regler bearbetas i prioritetsordning. När trafiken påträffar en regel matchning stoppas regel bearbetningen. Därför bearbetas inte regler med lägre prioriteter som har samma attribut som regler med högre prioritet. Ta hand om att undvika motstridiga regler. |
| **Tillstånds spårning** | Spårning kan vara tillstånds lös (molnets privata moln, Internet eller VPN) eller tillstånds känslig (offentlig IP).  |
| **Protokoll** | Alternativen omfattar alla, TCP eller UDP. Om du behöver ICMP använder du valfri. |
| **Riktning** | Om regeln gäller för inkommande eller utgående trafik. |
| **Åtgärd** | Tillåt eller neka för den typ av trafik som definierats i regeln. |
| **Källa** | En IP-adress, CIDR-block (Classless Inter-Domain routing) (till exempel 10.0.0.0/24, till exempel) eller någon IP-adress.  Genom att ange ett intervall, ett service tag eller en program säkerhets grupp kan du skapa färre säkerhets regler. |
| **Källport** | Port som nätverks trafiken kommer från.  Du kan ange en enskild port eller ett port intervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler. |
| **Mål** | En IP-adress, CIDR-block (Classless Inter-Domain routing) (till exempel 10.0.0.0/24, till exempel) eller någon IP-adress.  Genom att ange ett intervall, ett service tag eller en program säkerhets grupp kan du skapa färre säkerhets regler.  |
| **Målport** | Port som nätverks trafiken flödar till.  Du kan ange en enskild port eller ett port intervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler.|

### <a name="stateless"></a>Tillståndslös

En tillstånds lös regel ser bara ut i enskilda paket och filtrerar dem utifrån regeln. Det kan krävas ytterligare regler för trafikflöde i omvänd riktning. Använd tillstånds lösa regler för trafik mellan följande punkter:

* Undernät för privata AVS-moln
* Lokalt undernät och ett privat moln under nät
* Internet trafik från molnets privata moln

### <a name="stateful"></a>Tillstånds känsliga

 En tillstånds känslig regel är medveten om de anslutningar som passerar genom den. En flödespost skapas för befintliga anslutningar. Kommunikation tillåts eller nekas baserat på flödespostens anslutningsstatus. Använd den här regel typen för offentliga IP-adresser för att filtrera trafik från Internet.

### <a name="default-rules"></a>Standardregler

Följande standard regler skapas i alla brand Väggs tabeller.

|Prioritet|Namn|Tillstånds spårning|Riktning|Trafik typ|Protokoll|Källa|Källport|Mål|Målport|Åtgärd|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Tillstånds känsliga|Utgående|Offentlig IP-eller Internet trafik|Alla|Alla|Alla|Alla|Alla|Tillåt|
|65001|deny-all-from-internet|Tillstånds känsliga|Inkommande|Offentlig IP-eller Internet trafik|Alla|Alla|Alla|Alla|Alla|Neka|
|65002|Tillåt-allt-till-intranät|Tillståndslös|Utgående|Intern eller VPN-trafik i AVS-moln|Alla|Alla|Alla|Alla|Alla|Tillåt|
|65003|Tillåt-allt-från-intranät|Tillståndslös|Inkommande|Intern eller VPN-trafik i AVS-moln|Alla|Alla|Alla|Alla|Alla|Tillåt|

## <a name="next-steps"></a>Nästa steg

* [Konfigurera brand Väggs tabeller och regler](firewall.md)
