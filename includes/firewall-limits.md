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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80813810"
---
| Resurs | Gräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regler|10 000. Alla regel typer kombineras.|
|Högsta antal DNAT-regler|298<br>Om en regels protokoll har kon figurer ATS för både TCP och UDP räknas det som två regler.|
|Minsta AzureFirewallSubnet-storlek |/26|
|Portintervall i nätverk och regler|1 - 65535|
|Offentliga IP-adresser|100 maximalt (för närvarande har SNAT-portar endast lagts till för de första fem offentliga IP-adresserna.)|
|IP-grupper IP-adresser|50 IP-grupper eller mindre: maximalt 5000 enskilda IP-adresser per brand Väggs instans.<br>51-100 IP-grupper: 500 enskilda IP-adresser varje per brand Väggs instans.<br><br>Mer information finns i [IP-grupper (för hands version) i Azure-brandväggen](../articles/firewall/ip-groups.md#ip-address-limits)
|Routningstabell|Som standard har AzureFirewallSubnet en router på 0.0.0.0/0 med NextHopType-värdet inställt på **Internet**.<br><br>Azure-brandväggen måste ha direkt Internet anslutning. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP, måste du åsidosätta att med värdet 0.0.0.0/0 UDR med **NextHopType** -värdet inställt på **Internet** för att upprätthålla direkt Internet anslutning. Som standard stöder inte Azure-brandväggen Tvingad tunnel trafik till ett lokalt nätverk.<br><br>Men om konfigurationen kräver Tvingad tunnel trafik till ett lokalt nätverk, kommer Microsoft att stödja den på ett fall med hjälp av fall. Kontakta supporten så att vi kan granska ditt ärende. Om det godkänns kommer vi att tillåta din prenumeration och se till att den nödvändiga brand Väggs anslutningen för Internet underhålls.|

<sup>1</sup> Kontakta Azure-supporten om du behöver öka dessa gränser.
