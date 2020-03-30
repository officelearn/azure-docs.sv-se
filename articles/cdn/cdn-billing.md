---
title: Förstå Azure CDN-fakturering | Microsoft-dokument
description: I vanliga frågor och svar beskrivs hur Azure CDN-fakturering fungerar.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: magattus
ms.openlocfilehash: e2827a11f4ec2a5c0467c3699cd9990aaf7ae97a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73495482"
---
# <a name="understanding-azure-cdn-billing"></a>Så här fungerar fakturering för Azure CDN

I den här vanliga frågor och svar beskrivs faktureringsstrukturen för innehåll som finns i Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Vad är en faktureringsregion?
En faktureringsregion är ett geografiskt område som används för att avgöra vilken hastighet som debiteras för leverans av objekt från Azure CDN. De aktuella faktureringszonerna och deras regioner är följande:

- Zon 1: Nordamerika, Europa, Mellanöstern och Afrika

- Zon 2: Asien och Stillahavsområdet (inklusive Japan)

- Zon 3: Sydamerika

- Zon 4: Australien och Nya Zeeland

- Zon 5: Indien

Information om POP-regioner (point-of-presence) finns i [Azure CDN POP-platser efter region](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Till exempel är en POP i Mexiko i Nordamerika och ingår därför i zon 1. 

Information om Azure CDN-priser finns i [priser för Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hur beräknas leveranskostnaderna efter region?
Faktureringsregionen för Azure CDN baseras på platsen för källservern som levererar innehållet till slutanvändaren. Kundens mål (fysiska plats) betraktas inte som faktureringsregionen.

Om en användare som finns i Mexiko till exempel utfärdar en begäran och den här begäran servas av en server som finns i en AMERIKANSK POP på grund av peering- eller trafikförhållanden, kommer faktureringsregionen att vara USA.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Vad är en fakturerbar Azure CDN-transaktion?
Alla HTTP(S)-begäranden som avslutas vid CDN är en fakturerbar händelse som innehåller alla svarstyper: lyckades, misslyckas eller andra. Olika svar kan dock generera olika trafikbelopp. Till exempel *genererar 304 Inte ändrade* och andra sidhuvudsvar lite trafik eftersom de är ett litet sidhuvudsvar. På samma sätt är felsvar (till exempel *404 hittades inte)* fakturerbara men medför en liten kostnad på grund av den lilla svarsnyttolasten.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Vilka andra Azure-kostnader är associerade med Azure CDN-användning?
Om du använder Azure CDN debiteras också vissa användningsavgifter för de tjänster som används som ursprung för dina objekt. Dessa kostnader är vanligtvis en liten del av den totala cdn-kostnaden.

Om du använder Azure Blob-lagring som ursprung för ditt innehåll ådrar du dig även följande lagringsavgifter för cachefyllningar:

- Faktisk GB som används: Den faktiska lagringen av källobjekten.

- Transaktioner: Vid behov för att fylla cacheminnet.

- Överföringar i GB: Mängden data som överförs för att fylla CDN-cacheminnena.

> [!NOTE]
> Från och med oktober 2019, Om du använder Azure CDN från Microsoft, är kostnaden för dataöverföring från Origins som finns i Azure till CDN PoPs kostnadsfritt. Azure CDN från Verizon och Azure CDN från Akamai omfattas av de priser som beskrivs nedan.

Mer information om Azure Storage-fakturering finns i [Förstå Azure Storage Billing – Bandbredd, Transaktioner och Kapacitet](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Om du använder *värdserviceleverans*debiteras du enligt följande:

- Azure-beräkningstid: Beräkningsinstanserna som fungerar som ursprung.

- Azure-beräkningsöverföring: Dataöverföringar från beräkningsinstanserna för att fylla Azure CDN-cacheminnen.

Om din klient använder begäranden om byteintervall (oavsett ursprungstjänst) gäller följande överväganden:

- En *begäran om byteintervall* är en fakturerbar transaktion på CDN. När en klient utfärdar en begäran om byteintervall gäller den här begäran för en delmängd (intervall) av objektet. CDN svarar med endast en del av innehållet som begärs. Det här partiella svaret är en fakturerbar transaktion och överföringsbeloppet är begränsat till storleken på intervallsvaret (plus rubriker).

- När en begäran endast anländer för en del av ett objekt (genom att ange ett byte-intervallhuvud) kan CDN hämta hela objektet i cacheminnet. Även om den fakturerbara transaktionen från CDN är för ett partiellt svar, kan den fakturerbara transaktionen från ursprunget omfatta objektets fulla storlek.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Hur mycket överföringsaktivitet inträffar för att stödja cachen?
Varje gång en CDN POP behöver fylla sin cache, gör den en begäran till ursprunget för objektet som cachelagras. Som ett resultat, ursprunget medför en fakturerbar transaktion på varje cache miss. Antalet cachemissar beror på ett antal faktorer:

- Hur cacheable innehållet är: Om innehållet har höga TTL (time-to-live) / utgångsdatum värden och nås ofta så att det förblir populärt i cache, då den stora majoriteten av belastningen hanteras av CDN. Ett typiskt bra cache-hit-förhållande är långt över 90%, vilket innebär att mindre än 10% av klientbegärandena måste återgå till ursprung, antingen för en cache miss eller objektuppdatering.

- Hur många noder behöver läsa in objektet: Varje gång en nod läser in ett objekt från ursprunget, ådrar den sig en fakturerbar transaktion. Därför resulterar mer globalt innehåll (som nås från fler noder) i mer fakturerbara transaktioner.

- TTL inflytande: En högre TTL för ett objekt innebär att det måste hämtas från ursprunget mindre ofta. Det innebär också att klienter, till exempel webbläsare, kan cachelagra objektet längre, vilket kan minska transaktionerna till CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Vilka ursprungstjänster är berättigade till kostnadsfri dataöverföring med Azure CDN från Microsoft? 
Om du använder någon av följande Azure-tjänster som ditt CDN-ursprung debiteras du inte från dataöverföring från Origin till CDN-poPs. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Lastbalanserare
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure App-tjänst
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure-datautforskaren
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL-databas
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hur hanterar jag mina kostnader mest effektivt?
Ställ in den längsta TTL möjligt på ditt innehåll. 
