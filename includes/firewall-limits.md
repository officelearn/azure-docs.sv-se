---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "68229189"
---
| Resource | Standardgräns |
| --- | --- |
| Data genom strömning |30 Gbit/s<sup>1</sup> |
|Regler|10 000, alla regel typer kombineras.|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|0 – 64000. Arbetet pågår för att minska den här begränsningen.|
|Offentliga IP-adresser|100 Max|
|Routningstabell|Som standard har AzureFirewallSubnet en router på 0.0.0.0/0 med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet anslutning. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP, måste du åsidosätta att med värdet 0.0.0.0/0 UDR med **NextHopType** -värdet inställt på **Internet** för att upprätthålla direkt Internet anslutning. Som standard stöder inte Azure-brandväggen Tvingad tunnel trafik till ett lokalt nätverk.<br><br>Men om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk, kommer Microsoft att stödja den på ett fall med hjälp av fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns kommer vi att tillåta din prenumeration och se till att den nödvändiga brand Väggs anslutningen för Internet underhålls.|

<sup>1</sup> Kontakta Azure-supporten om du behöver öka dessa gränser.
