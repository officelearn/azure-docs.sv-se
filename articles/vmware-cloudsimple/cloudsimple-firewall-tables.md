---
title: Azure VMware-lösning från CloudSimple - Brandväggstabeller
description: Läs mer om CloudSimple privata molnbrand brandväggstabeller och brandväggsregler.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025052"
---
# <a name="firewall-tables-overview"></a>Översikt över brandväggstabeller

I en brandväggstabell visas regler för att filtrera nätverkstrafik till och från privata molnresurser. Du kan använda brandväggstabeller på ett VLAN/-undernät. Reglerna styr nätverkstrafiken mellan ett källnätverk eller en IP-adress och ett målnätverk eller en IP-adress.

## <a name="firewall-rules"></a>Brandväggsregler

I följande tabell beskrivs parametrarna i en brandväggsregel.

| Egenskap | Information |
| ---------| --------|
| **Namn** | Ett namn som unikt identifierar brandväggsregeln och dess syfte. |
| **Prioritet** | Ett tal mellan 100 och 4096, med 100 som högsta prioritet. Regler bearbetas i prioritetsordning. När trafiken stöter på en regelmatchning stoppas regelbearbetningen. Därför bearbetas inte regler med lägre prioriteter som har samma attribut som regler med högre prioritet.  Var noga med att undvika motstridiga regler. |
| **Spårning av tillstånd** | Spårning kan vara tillståndslös (Privat moln, Internet eller VPN) eller tillståndskänslig (offentlig IP).  |
| **Protokollet** | Alternativen inkluderar Any, TCP eller UDP. Om du behöver ICMP, använd any. |
| **Riktning** | Om regeln gäller för inkommande eller utgående trafik. |
| **Åtgärd** | Tillåt eller neka för den typ av trafik som definieras i regeln. |
| **Källkod** | Ett IP-adress, klasslös cidr-block (inter-domain routing) (10.0.0.0/24, till exempel) eller Någon.  Genom att ange ett intervall, en tjänsttagg eller en programsäkerhetsgrupp kan du skapa färre säkerhetsregler. |
| **Källa Port** | Port från vilken nätverkstrafik kommer.  Du kan ange en enskild port eller ett enskilt portintervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler. |
| **Destination** | Ett IP-adress, klasslös cidr-block (inter-domain routing) (10.0.0.0/24, till exempel) eller Någon.  Genom att ange ett intervall, en tjänsttagg eller en programsäkerhetsgrupp kan du skapa färre säkerhetsregler.  |
| **Destinationsport** | Port som nätverkstrafiken flödar till.  Du kan ange en enskild port eller ett enskilt portintervall, till exempel 443 eller 8000-8080. Om du anger intervall behöver du inte skapa lika många säkerhetsregler.|

### <a name="stateless"></a>Tillståndslös

En tillståndslös regel tittar bara på enskilda paket och filtrerar dem baserat på regeln.  
Ytterligare regler kan krävas för trafikflödet i motsatt riktning.  Använd tillståndslösa regler för trafik mellan följande punkter:

* Undernät till privata moln
* Lokalt undernät och ett privat molnundernät
* Internettrafik från de privata molnen

### <a name="stateful"></a>Tillståndskänslig

 En tillståndskänslig regel är medveten om de anslutningar som passerar genom den. En flödespost skapas för befintliga anslutningar. Kommunikation tillåts eller nekas baserat på flödespostens anslutningsstatus.  Använd den här regeltypen för offentliga IP-adresser för att filtrera trafik från Internet.

### <a name="default-rules"></a>Standardregler

Följande standardregler skapas i varje brandväggstabell.

|Prioritet|Namn|Spårning av tillstånd|Riktning|Trafiktyp|Protokoll|Källa|Källport|Mål|Målport|Åtgärd|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|tillåt-allt-till-internet|Tillståndskänslig|Utgående|Offentlig IP- eller internettrafik|Alla|Alla|Alla|Alla|Alla|Tillåt|
|65001|neka-allt-från-internet|Tillståndskänslig|Inkommande|Offentlig IP- eller internettrafik|Alla|Alla|Alla|Alla|Alla|Neka|
|65002|tillåta-allt-till-intranät|Tillståndslös|Utgående|Privat molnintern eller VPN-trafik|Alla|Alla|Alla|Alla|Alla|Tillåt|
|65003|tillåta-allt-från-intranät|Tillståndslös|Inkommande|Privat molnintern eller VPN-trafik|Alla|Alla|Alla|Alla|Alla|Tillåt|

## <a name="next-steps"></a>Nästa steg

* [Konfigurera brandväggstabeller och -regler](firewall.md)
