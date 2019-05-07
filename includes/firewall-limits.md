---
title: ta med fil
description: ta med fil
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150316"
---
| Resource | Standardgräns |
| --- | --- |
| Dataflöde |30 Gbit/s<sup>1</sup> |
|Regler|10 000, regeln alla typer kombineras.|
|Minimistorlek för AzureFirewallSubnet |/26|
|Portintervall i nätverk och regler|0-64,000. Arbete pågår till slappna av den här begränsningen.|
|Routningstabell|AzureFirewallSubnet har som standard en väg med 0.0.0.0/0 med NextHopType värdet **Internet**.<br><br>Azure-brandväggen måste ha direkt Internetanslutning. Om din AzureFirewallSubnet lär sig en standardväg till ditt lokala nätverk via BGP, måste du åsidosätta detta med en 0.0.0.0/0 UDR med den **NextHopType** värdet **Internet** att upprätthålla direkt Ansluten till Internet. Som standard stöder Azure brandvägg inte framtvingad tunneling till ett lokalt nätverk.<br><br>Men om din konfiguration kräver Tvingad tunneltrafik till ett lokalt nätverk, stöder Microsoft den på en fall till fall. Kontakta supporten, så att vi kan granska ditt ärende. Om godkända vi godkänna din prenumeration och se till att Internet-anslutning krävs brandväggen underhålls.|

<sup>1</sup>kontakta Azure-supporten om du behöver utöka gränserna.
