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
ms.openlocfilehash: 92c2e79910e40721a0ef62d44825bd1f3e19fc79
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548241"
---
| Resurs | Standardgräns |
| --- | --- |
| Data genom strömning |30 Gbit/s<sup>1</sup> |
|Regler|10 000. Alla regel typer kombineras.|
|Högsta antal DNAT-regler|299|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|0 – 64000. Arbetet pågår för att minska den här begränsningen.|
|Offentliga IP-adresser|100 maximalt (för närvarande har SNAT-portar endast lagts till för de första fem offentliga IP-adresserna.)|
|Routningstabell|Som standard har AzureFirewallSubnet en router på 0.0.0.0/0 med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet anslutning. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP, måste du åsidosätta att med värdet 0.0.0.0/0 UDR med **NextHopType** -värdet inställt på **Internet** för att upprätthålla direkt Internet anslutning. Som standard stöder inte Azure-brandväggen Tvingad tunnel trafik till ett lokalt nätverk.<br><br>Men om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk, kommer Microsoft att stödja den på ett fall med hjälp av fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns kommer vi att tillåta din prenumeration och se till att den nödvändiga brand Väggs anslutningen för Internet underhålls.|

<sup>1</sup> Kontakta Azure-supporten om du behöver öka dessa gränser.
