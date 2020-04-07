---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/03/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4972a80ec7c481ac7bc8860c005c8576ce37a090
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758654"
---
| Resurs | Gräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regler|10,000. Alla regeltyper kombinerade.|
|Högsta DNAT-regler|298<br>Om en regels protokoll är konfigurerat för både TCP och UDP räknas det som två regler.|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|0-64,000. Arbete pågår för att slappna av denna begränsning.|
|Offentliga IP-adresser|100 maximalt (För närvarande läggs SNAT-portar till endast för de fem första offentliga IP-adresserna.)|
|IP-adresser för IP-grupper|50 IP-grupper eller mindre: maximalt 5000 enskilda IP-adresser per brandväggsinstans.<br>51 - 100 IP-grupper: 500 enskilda IP-adress vardera per brandväggsinstans.<br><br>Mer information finns i [IP-grupper (förhandsversion) i Azure-brandväggen](../articles/firewall/ip-groups.md#ip-address-limits)
|Routningstabell|Som standard har AzureFirewallSubnet en 0.0.0/0/0-väg med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet-anslutning. Om din AzureFirewallSubnet lär sig en standardväg till ditt lokala nätverk via BGP måste du åsidosätta det med en UDR 0.0.0/0 med **NextHopType-värdet** som **Internet** för att upprätthålla direkt Internet-anslutning. Som standard stöder Azure-brandväggen inte tvingad tunnel till ett lokalt nätverk.<br><br>Men om din konfiguration kräver tvångstunnelering till ett lokalt nätverk, kommer Microsoft att stödja det från fall till fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns tillåter vi din prenumeration och ser till att den brandvägg som Internet-anslutningen upprätthålls.|

<sup>1.</sup> Om du behöver öka dessa gränser kontaktar du Azure Support.
