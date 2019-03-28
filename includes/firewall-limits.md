---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 368a7cf1d372d79f062affe9b206d070afe5c2f7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505797"
---
| Resurs | Standardgräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regler|10 000, regeln alla typer kombineras.|
|Minimistorlek för AzureFirewallSubnet |/26|
|Portintervall i nätverk och regler|0-64,000. Arbete pågår till slappna av den här begränsningen.|
|Routningstabell|AzureFirewallSubnet har som standard en väg med 0.0.0.0/0 med NextHopType värdet **Internet**.<br><br>Om du aktiverar Tvingad tunneltrafik till lokalt via ExpressRoute eller VPN-Gateway, kan du behöva uttryckligen konfigurera en 0.0.0.0/0 användardefinierad väg (UDR) med NextHopType värde som Internet och associera den med din AzureFirewallSubnet. Detta åsidosätter eventuella standard-gateway BGP-meddelandet tillbaka till ditt lokala nätverk. Om din organisation kräver Tvingad tunneltrafik för Azure-brandvägg att dirigera standard gateway trafiken tillbaka via ditt lokala nätverk, kontakta supporten. Vi kan godkänna din prenumeration för att se till att nödvändiga brandväggen Internetanslutning upprätthålls.|

<sup>1</sup>kontakta Azure-supporten om du behöver utöka gränserna.
