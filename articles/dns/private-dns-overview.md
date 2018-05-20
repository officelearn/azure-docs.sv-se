---
title: Med hjälp av Azure DNS för privata domäner | Microsoft Docs
description: Översikt över privata DNS som är värd för tjänsten i Microsoft Azure.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: 677fc66b66d6c17806a313f2fac3a15e8e1775ba
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="using-azure-dns-for-private-domains"></a>Med hjälp av Azure DNS för privata domäner
Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) ett tjänstnamn till dess IP-adress. Azure DNS är värdtjänsten för DNS-domäner är att tillhandahålla namnmatchning med hjälp av Microsoft Azure-infrastrukturen.  Förutom mot internet DNS-domäner stöder Azure DNS nu också privata DNS-domäner som en förhandsversion av funktionen.  
 
Azure DNS är en tillförlitlig, säker DNS-tjänst för att hantera och lösa domännamn i ett virtuellt nätverk utan att behöva lägga till en anpassad DNS-lösning. Privata DNS-zoner kan du använda dina egna anpassade domännamn i stället för Azure-tillhandahållna namnen finns idag.  Använda anpassade domännamn kan du anpassa din virtuella nätverksarkitekturen som bäst passar organisationens behov. Den innehåller namnmatchning för virtuella datorer inom ett virtuellt nätverk och mellan virtuella nätverk. Du kan dessutom konfigurera zoner namn med vyn Dela vågrätt - så att en privat och en offentlig DNS-zon med samma namn.

![Översikt över DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Fördelar

* **Tar bort behovet av anpassade DNS-lösningar.** Många kunder skapade tidigare, anpassade DNS-lösningar för att hantera DNS-zoner i det virtuella nätverket.  DNS-zonen hantering sköts nu med Azures inbyggda infrastrukturen, vilken tar bort för att skapa och hantera anpassade DNS-lösningar.

* **Använda alla vanliga DNS-posttyper.**  Azure DNS stöder A, AAAA, CNAME, MX, NS, PTR, SOA, SRV och TXT-poster.

* **Hantering av automatisk hostname-post.** Tillsammans med värd för din anpassade DNS-poster, underhåller Azure automatiskt hostname-poster för de virtuella datorerna i de angivna virtuella nätverk.  På så sätt kan du optimera de domännamn som du använder utan att skapa anpassade lösningar som DNS eller ändra program.

* **Värdnamnsmatchning mellan virtuella nätverk.** Till skillnad från Azure-tillhandahållna värdnamn, kan privata DNS-zoner delas mellan virtuella nätverk.  Den här funktionen gör det enklare för identifiering av cross-nätverket och tjänsten scenarier, till exempel virtuella nätverk peering.

* **Välbekanta verktyg och användarupplevelse.** Om du vill minska inlärningskurvan använder den här nya erbjudande redan etablerade Azure DNS-verktyg (PowerShell Resource Manager-mallar, REST-API).

* **Dela vågrätt DNS-stöd.** Azure DNS kan du skapa zoner med samma namn att matcha olika svar från inom ett virtuellt nätverk och från Internet.  Ett typiskt scenario för Dela vågrätt DNS är att tillhandahålla en särskild version av en tjänst för användning i ditt virtuella nätverk.

* **Tillgänglig i alla Azure-regioner.** Azure DNS privata zoner är tillgänglig i alla Azure-regioner i offentlig Azure-molnet. 


## <a name="capabilities"></a>Funktioner 
* Automatisk registrering av virtuella datorer från ett enda virtuellt nätverk länkade till en privat zon som ett virtuellt nätverk för registrering. De virtuella datorerna är registrerad (läggs till) att zonen privata som A-poster pekar på sina privata IP-adresser. Dessutom när en virtuell dator i en registrering av virtuellt nätverk hämtar bort Azure kommer också automatiskt ta bort motsvarande DNS-posten från länkade privata zonen. Observera att registrering virtuella nätverk som standard också fungera som upplösning virtuella nätverk i den DNS-matchning mot zonen fungerar från någon av de virtuella datorerna i det virtuella nätverket för registrering. 
* Vidarebefordra DNS-upplösning som stöds i virtuella nätverk som är länkade till den privata zonen som upplösning virtuella nätverk. Det finns ingen explicit beroende mellan virtuella nätverk i DNS-matchning som de virtuella nätverken att peerkoppla med varandra. Dock kunder kanske vill peer-virtuella nätverk för andra scenarier (t.ex: HTTP-trafik).
* Omvänd DNS-sökning stöds inom omfånget för virtuella nätverk. Omvänd DNS-sökning för en privat IP i det virtuella nätverket som tilldelats en privat zon returneras det FQDN som innehåller värden/postnamnet samt zonnamnet som suffix. 


## <a name="limitations"></a>Begränsningar
* 1 registrering virtuella nätverk per privata zon
* Upp till 10 upplösning virtuella nätverk per privata zon
* Ett givet virtuellt nätverk kan bara kopplas till en privat zon som ett virtuellt nätverk för registrering
* Ett givet virtuellt nätverk kan länkas till upp till 10 privata zoner som ett virtuellt nätverk upplösning
* Om ett virtuellt nätverk för registrering har angetts DNS-posterna för de virtuella datorerna från det virtuella nätverket som är registrerade i zonen för privat kommer inte att visas eller hämtas från Powershell/CLI/API: erna, men VM-poster registreras verkligen och löser har
* Omvänd DNS fungerar endast för privata IP-adressutrymme i det virtuella nätverket för registrering
* Omvänd DNS för en privat IP som inte är registrerat i zonen privat (t.ex: privat IP för en virtuell dator i ett virtuellt nätverk som är länkad som ett virtuellt nätverk lösning till en privat zon) returnerar ”internal.cloudapp.net” som DNS-suffix, men det här suffixet inte matchas.   
* Virtuella nätverk måste vara tom (dvs Inga VM-poster) när ursprungligen (dvs för första gången) länka till en privat zon som registreringen eller matchningen virtuella nätverk. Det virtuella nätverket kan dock sedan vara icke-tom för framtida länkning som ett registrerings- eller virtuellt nätverk till andra privata zoner. 
* För tillfället stöds villkorlig vidarebefordran inte, till exempel för att aktivera matchning mellan Azure och OnPrem nätverk. Dokumentation om hur kunder kan utnyttja det här scenariot via andra mekanismer finns [namnmatchning för virtuella datorer och Rollinstanser](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

Vi rekommenderar också att du också läsa mer om den [vanliga frågor och svar](./dns-faq.md#private-dns) för några vanliga frågor och svar om privat zoner i Azure DNS, inklusive specifika DNS-registrering och lösningar som du kan förvänta dig för vissa typer av åtgärder. 


## <a name="pricing"></a>Prissättning

Privata DNS-zoner är kostnadsfritt under förhandsversion. Vid allmän tillgänglighet använder funktionen en användningsbaserad prismodellen liknar den befintliga Azure DNS erbjudande. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar en zon för privat Azure DNS med den [PowerShell](./private-dns-getstarted-powershell.md) eller [CLI](./private-dns-getstarted-cli.md).

Läsa mer om några vanliga scenarier [privatzonsscenarier](./private-dns-scenarios.md) som kan genomföras med privata zoner i Azure DNS.

Läsa på den [vanliga frågor och svar](./dns-faq.md#private-dns) för några vanliga frågor och svar på privata zoner i Azure DNS inklusive specifikt beteende du kan förvänta dig för vissa typer av åtgärder. 

Lär dig mer om DNS-zoner och poster genom att besöka: [DNS-zoner och innehåller en översikt över](dns-zones-records.md).

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

