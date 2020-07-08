---
title: inkludera fil
description: inkludera fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b317c2a43352a750d4700fad56d5f7d741b2cc7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805760"
---
| Resurs | Gräns |
| --- | --- |
| Data genom strömning |30 Gbit/s<sup>1</sup> |
|Regler|10 000. Alla regel typer kombineras.|
|Högsta antal DNAT-regler|298 för en enskild offentlig IP-adress.<br>Eventuella ytterligare offentliga IP-adresser bidrar till de tillgängliga SNAT-portarna, men minskar antalet tillgängliga DNAT-regler. Till exempel kan två offentliga IP-adresser tillåta 297 DNAT-regler. Om en regels protokoll har kon figurer ATS för både TCP och UDP räknas det som två regler.|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|1 - 65535|
|Offentliga IP-adresser|250 max. Alla offentliga IP-adresser kan användas i DNAT-regler och alla bidrar till tillgängliga SNAT-portar.|
|IP-adresser i IP-grupper|Högst 100 IP-grupper per brand vägg.<br>Högst 5000 enskilda IP-adresser eller IP-prefix per IP-grupp.<br><br>Mer information finns i [IP-grupper i Azure-brandväggen](../articles/firewall/ip-groups.md#ip-address-limits).
|Routningstabell|Som standard har AzureFirewallSubnet en router på 0.0.0.0/0 med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet anslutning. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP, måste du åsidosätta att med värdet 0.0.0.0/0 UDR med **NextHopType** -värdet inställt på **Internet** för att upprätthålla direkt Internet anslutning. Som standard stöder inte Azure-brandväggen Tvingad tunnel trafik till ett lokalt nätverk.<br><br>Men om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk, kommer Microsoft att stödja den på ett fall med hjälp av fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns kommer vi att tillåta din prenumeration och se till att den nödvändiga brand Väggs anslutningen för Internet underhålls.|

<sup>1</sup> Kontakta Azure-supporten om du behöver öka dessa gränser.
