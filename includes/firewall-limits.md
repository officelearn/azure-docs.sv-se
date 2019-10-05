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
ms.openlocfilehash: d4797232a51739238a88576a1fdd95bc62d6afaa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975350"
---
| Resource | Standardgräns |
| --- | --- |
| Data genom strömning |30 Gbit/s<sup>1</sup> |
|Regler|10 000, alla regel typer kombineras.|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|0 – 64000. Arbetet pågår för att minska den här begränsningen.|
|Offentliga IP-adresser|100 maximalt (för närvarande har SNAT-portar endast lagts till för de första fem offentliga IP-adresserna.)|
|Routningstabell|Som standard har AzureFirewallSubnet en router på 0.0.0.0/0 med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet anslutning. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP, måste du åsidosätta att med värdet 0.0.0.0/0 UDR med **NextHopType** -värdet inställt på **Internet** för att upprätthålla direkt Internet anslutning. Som standard stöder inte Azure-brandväggen Tvingad tunnel trafik till ett lokalt nätverk.<br><br>Men om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk, kommer Microsoft att stödja den på ett fall med hjälp av fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns kommer vi att tillåta din prenumeration och se till att den nödvändiga brand Väggs anslutningen för Internet underhålls.|

<sup>1</sup> Kontakta Azure-supporten om du behöver öka dessa gränser.
