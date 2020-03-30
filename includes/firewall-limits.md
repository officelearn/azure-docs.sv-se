---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0df38533afe97f010d1050c3ee2a4a69a54d4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334586"
---
| Resurs | Gräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regler|10,000. Alla regeltyper kombinerade.|
|Högsta DNAT-regler|299|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|0-64,000. Arbete pågår för att slappna av denna begränsning.|
|Offentliga IP-adresser|100 maximalt (För närvarande läggs SNAT-portar till endast för de fem första offentliga IP-adresserna.)|
|Routningstabell|Som standard har AzureFirewallSubnet en 0.0.0/0/0-väg med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet-anslutning. Om din AzureFirewallSubnet lär sig en standardväg till ditt lokala nätverk via BGP måste du åsidosätta det med en UDR 0.0.0/0 med **NextHopType-värdet** som **Internet** för att upprätthålla direkt Internet-anslutning. Som standard stöder Azure-brandväggen inte tvingad tunnel till ett lokalt nätverk.<br><br>Men om din konfiguration kräver tvångstunnelering till ett lokalt nätverk, kommer Microsoft att stödja det från fall till fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns tillåter vi din prenumeration och ser till att den brandvägg som Internet-anslutningen upprätthålls.|

<sup>1.</sup> Om du behöver öka dessa gränser kontaktar du Azure Support.
