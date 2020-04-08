---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813810"
---
| Resurs | Gräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regler|10,000. Alla regeltyper kombinerade.|
|Högsta DNAT-regler|298<br>Om en regels protokoll är konfigurerat för både TCP och UDP räknas det som två regler.|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|1 - 65535|
|Offentliga IP-adresser|100 maximalt (För närvarande läggs SNAT-portar till endast för de fem första offentliga IP-adresserna.)|
|IP-adresser för IP-grupper|50 IP-grupper eller mindre: maximalt 5000 enskilda IP-adresser per brandväggsinstans.<br>51 - 100 IP-grupper: 500 enskilda IP-adress vardera per brandväggsinstans.<br><br>Mer information finns i [IP-grupper (förhandsversion) i Azure-brandväggen](../articles/firewall/ip-groups.md#ip-address-limits)
|Routningstabell|Som standard har AzureFirewallSubnet en 0.0.0/0/0-väg med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet-anslutning. Om din AzureFirewallSubnet lär sig en standardväg till ditt lokala nätverk via BGP måste du åsidosätta det med en UDR 0.0.0/0 med **NextHopType-värdet** som **Internet** för att upprätthålla direkt Internet-anslutning. Som standard stöder Azure-brandväggen inte tvingad tunnel till ett lokalt nätverk.<br><br>Men om din konfiguration kräver tvångstunnelering till ett lokalt nätverk, kommer Microsoft att stödja det från fall till fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns tillåter vi din prenumeration och ser till att den brandvägg som Internet-anslutningen upprätthålls.|

<sup>1.</sup> Om du behöver öka dessa gränser kontaktar du Azure Support.
