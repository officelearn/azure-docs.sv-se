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
ms.openlocfilehash: 0467359cd9d6a067e519a62532f00459bc5f68cb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805223"
---
| Resurs | Standardgräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regler|10 000, regeln alla typer kombineras.|
|Minimistorlek för AzureFirewallSubnet |/26|
|Portintervall i nätverk och regler|0-64,000. Arbete pågår till slappna av den här begränsningen.|
|Routningstabell|AzureFirewallSubnet har som standard en väg med 0.0.0.0/0 med NextHopType värdet **Internet**.<br><br>Om du aktiverar Tvingad tunneltrafik till lokalt via ExpressRoute eller VPN-Gateway, kan du behöva uttryckligen konfigurera en 0.0.0.0/0 användardefinierad väg (UDR) med NextHopType värde som Internet och associera den med din AzureFirewallSubnet. Detta åsidosätter eventuella standard-gateway BGP-meddelandet tillbaka till ditt lokala nätverk. Om din organisation kräver Tvingad tunneltrafik för Azure-brandvägg att dirigera standard gateway trafiken tillbaka via ditt lokala nätverk, kontakta supporten. Vi kan godkänna din prenumeration för att se till att nödvändiga brandväggen Internetanslutning upprätthålls.|

<sup>1</sup>kontakta Azure-supporten om du behöver utöka gränserna.
