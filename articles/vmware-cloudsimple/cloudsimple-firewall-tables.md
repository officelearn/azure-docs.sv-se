---
title: Brandväggen tabeller - VMware-lösning genom CloudSimple - Azure
description: Läs mer om CloudSimple optimeras brandväggen tabeller och brandväggsregler.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577352"
---
# <a name="firewall-tables-overview"></a>Översikt över brandväggen för tabeller

En brandvägg tabell med regler för att filtrera nätverkstrafik till och från resurser för privata moln. Du kan använda dem i ett virtuellt lokalt nätverk eller undernät. Reglerna styr sedan nätverkstrafik mellan en Källnätverk eller IP-adress och ett Målnätverk eller IP-adress.

## <a name="firewall-rules"></a>Brandväggsregler

I följande tabell beskrivs parametrarna i en brandväggsregel.

| Egenskap  | Information |
| ---------| --------|
| **Namn** | Ett namn som unikt identifierar brandväggsregeln och dess syfte. |
| **Prioritet** | Ett tal mellan 100 och 4096 med 100 högst prioritet. Regler bearbetas i prioritetsordning. När trafiken kommer över en matchning för regeln, stoppas regelbearbetningen. Därför kan bearbetas regler med lägre prioritet som har samma attribut som regler med högre prioritet inte.  Var noga med för att undvika motstridiga regler. |
| **Tillstånd spårning** | Spårning kan vara tillståndslösa (privat moln, Internet eller VPN) eller tillståndskänslig (offentlig IP-adress).  |
| **Protokoll** | Till exempel någon, TCP eller UDP. Om du vill använda ICMP, använder du någon. |
| **Riktning** | Om regeln gäller för inkommande eller utgående trafik. |
| **Åtgärd** | Tillåt eller neka för typ av trafik som definierats i regeln. |
| **Källa** | En IP-adress, classless Inter-Domain routing (CIDR) block (till exempel 10.0.0.0/24) eller någon.  Ange ett intervall, gör en tjänsttagg eller programsäkerhetsgrupp att du kan skapa lika många säkerhetsregler. |
| **Källport** | Port från vilken trafiken kommer ifrån.  Du kan ange en enskild port eller ett portintervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler. |
| **mål** | En IP-adress, classless Inter-Domain routing (CIDR) block (till exempel 10.0.0.0/24) eller någon.  Ange ett intervall, gör en tjänsttagg eller programsäkerhetsgrupp att du kan skapa lika många säkerhetsregler.  |
| **Målport** | Porten som nätverkstrafik flödar.  Du kan ange en enskild port eller ett portintervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler.|

### <a name="stateless"></a>Tillståndslös

En regel för tillståndslösa tittar bara på enskilda paket och filtrerar dem baserat på regeln.  
Ytterligare regler kan krävas för trafikflöde i omvänd riktning.  Använd tillståndslösa regler för trafik mellan följande punkter:

* Undernät för privata moln
* Lokala undernät och ett privat moln-undernät
* Internet-trafiken från privata moln

### <a name="stateful"></a>Tillståndskänslig

 En tillståndskänslig regel är medvetna om de anslutningar som passerar genom den. En flödespost skapas för befintliga anslutningar. Kommunikation tillåts eller nekas baserat på flödespostens anslutningsstatus.  Använd den här regeltypen för offentliga IP-adresser att filtrera trafik från Internet.

### <a name="default-rules"></a>Standardregler

Följande standardregler skapas för varje brandvägg-tabellen.

|Prioritet|Namn|Tillstånd spårning|Direction|Typ av nätverkstrafik|Protokoll|Källa|Källport|Mål|Målport|Åtgärd|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Tillståndskänslig|Utgående|Offentliga IP- eller internet-trafik|Alla|Alla|Alla|Alla|Alla|Tillåt|
|65001|deny-all-from-internet|Tillståndskänslig|Inkommande|Offentliga IP- eller internet-trafik|Alla|Alla|Alla|Alla|Alla|Neka|
|65002|allow-all-to-intranet|Tillståndslös|Utgående|Privat moln som är interna eller VPN-trafik|Alla|Alla|Alla|Alla|Alla|Tillåt|
|65003|allow-all-from-intranet|Tillståndslös|Inkommande|Privat moln som är interna eller VPN-trafik|Alla|Alla|Alla|Alla|Alla|Tillåt|

## <a name="next-steps"></a>Nästa steg

* [Konfigurera brandväggen tabeller och regler](https://docs.azure.cloudsimple.com/firewall/)