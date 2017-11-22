---
title: "Med hjälp av Azure DNS för privata domäner | Microsoft Docs"
description: "Översikt över privata DNS som är värd för tjänsten i Microsoft Azure."
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 95cf8ab2bd34e698e12452e062687219bad49eb6
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2017
---
# <a name="using-azure-dns-for-private-domains"></a>Med hjälp av Azure DNS för privata domäner
Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) ett tjänstnamn till dess IP-adress. Azure DNS är värdtjänsten för DNS-domäner är att tillhandahålla namnmatchning med hjälp av Microsoft Azure-infrastrukturen.  Förutom mot internet DNS-domäner stöder Azure DNS nu också privata DNS-domäner som en förhandsversion av funktionen.  
 
Azure DNS är en tillförlitlig, säker DNS-tjänst för att hantera och lösa domännamn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Privata DNS-zoner kan du använda dina egna anpassade domännamn i stället för Azure-tillhandahållna namnen finns idag.  Använda anpassade domännamn kan du anpassa din virtuella nätverksarkitekturen som bäst passar organisationens behov. Den innehåller namnmatchning för virtuella datorer inom ett virtuellt nätverk och mellan virtuella nätverk. Du kan dessutom konfigurera zoner namn med vyn Dela vågrätt - så att en privat och en offentlig DNS-zon med samma namn.

![Översikt över DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="benefits"></a>Fördelar

* **Tar bort behovet av anpassade DNS-lösningar.** Många kunder skapade tidigare, anpassade DNS-lösningar för att hantera DNS-zoner i det virtuella nätverket.  DNS-zonen hantering sköts nu med Azures inbyggda infrastrukturen, vilken tar bort för att skapa och hantera anpassade DNS-lösningar.

* **Använda alla vanliga DNS-posttyper.**  Azure DNS stöder A, AAAA, CNAME, MX, NS, PTR, SOA, SRV och TXT-poster.

* **Hantering av automatisk hostname-post.** Tillsammans med värd för din anpassade DNS-poster, underhåller Azure automatiskt hostname-poster för de virtuella datorerna i de angivna virtuella nätverk.  På så sätt kan du optimera de domännamn som du använder utan att skapa anpassade lösningar som DNS eller ändra program.

* **Värdnamnsmatchning mellan virtuella nätverk.** Till skillnad från Azure-tillhandahållna värdnamn, kan privata DNS-zoner delas mellan virtuella nätverk.  Den här funktionen gör det enklare för identifiering av cross-nätverket och tjänsten scenarier, till exempel virtuella nätverk peering.

* **Välbekanta verktyg och användarupplevelse.** Om du vill minska inlärningskurvan använder den här nya erbjudande redan etablerade Azure DNS-verktyg (PowerShell Resource Manager-mallar, REST-API).

* **Dela vågrätt DNS-stöd.** Azure DNS kan du skapa zoner med samma namn att matcha olika svar från inom ett virtuellt nätverk och från Internet.  Ett typiskt scenario för Dela vågrätt DNS är att tillhandahålla en särskild version av en tjänst för användning i ditt virtuella nätverk.


## <a name="pricing"></a>Prissättning

Privata DNS-zoner är gratis i hanterade förhandsversionen. Vid allmän tillgänglighet använder funktionen en användningsbaserad prismodellen liknar den befintliga Azure DNS erbjudande. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar ett privat DNS-zon](./private-dns-getstarted-powershell.md) i Azure DNS.

Lär dig mer om DNS-zoner och poster genom att besöka: [DNS-zoner och innehåller en översikt över](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

