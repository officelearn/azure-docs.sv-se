---
title: Så här fungerar fakturering för Azure CDN | Microsoft Docs
description: Här beskrivs hur fungerar fakturering för Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782068"
---
# <a name="understanding-azure-cdn-billing"></a>Så här fungerar fakturering för Azure CDN

Här beskrivs fakturering strukturen för innehåll på med Azure CDN Content Delivery Network ().

## <a name="what-is-a-billing-region"></a>Vad är en Faktureringsregion?
En Faktureringsregion är ett geografiskt område som används för att avgöra vilket pris debiteras för leverans av objekt från Azure CDN. Aktuella fakturering zonerna och deras regioner är följande:

- Zon 1: Nordamerika, Europa, Mellanöstern och Afrika

- Zon 2: Asien och Stillahavsområdet (inklusive Japan)

- Zon 3: Sydamerika

- Zon 4: Australien och Nya Zeeland

- Zon 5: Indien

Information om point of presence (POP) regioner finns i [Azure CDN POP-platser efter region](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). Till exempel en POP i Mexiko är i regionen Nordamerika och ingår därför i zon 1. 

Information om priser för Azure CDN finns i [prissättningen för CDN](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hur beräknas leverans avgifter per region?
Azure CDN Faktureringsregion baseras på platsen för källservern som levererar innehåll till slutanvändaren. Faktureringsregion anses inte vara målet (fysiska plats) av klienten.

Till exempel om en användare finns i Mexiko skickar en begäran och den här begäran besvaras av en server i en USA-POP bero på peering eller trafik, debiteras Faktureringsregion USA.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Vad är en fakturerbar Azure CDN-transaktioner?
Varje HTTP (S)-begäran som avbryts vid CDN är en fakturerbar händelse som omfattar alla svarstyper av: lyckades, misslyckades eller annan. Dock generera olika svar olika trafik belopp. Till exempel *304 ändras inte* och andra endast svar generera lite trafik eftersom de är en liten huvudsvar, på samma sätt felsvar (till exempel *404 hittades inte*) är faktureringsbara men resultera i en liten kostnader på grund av mycket liten svarets nyttolast.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Vilka andra Azure-kostnader är associerade med Azure CDN-användning?
Använda Azure CDN även medför vissa kostnader för tjänsterna som används som ursprung för ditt objekt. Dessa kostnader är vanligtvis en liten del av den totala kostnaden för CDN-användning.

Om du använder Azure Blob storage som ursprung för ditt innehåll kan medföra du också på följande lagringskostnaderna för cache färger:

- Faktiska GB används: Det faktiska lagringsutrymmet för källobjekt.

- Överföringar i GB: Mängden data som överförs till Fyll CDN-cacheminnen.

- Transaktioner: Som behövs för att fylla i cachen.

Mer information om fakturering för Azure Storage finns i [förstå Azure Storage-fakturering – bandbredd, transaktioner och kapacitet](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Om du använder *finns serviceleverans*, debiteras du enligt följande:

- Azure-beräkningstid: De beräkningsinstanser som fungerar som ursprung.

- Azure-beräkning överföring: Data som överförs från de beräkningsinstanser för att fylla det Azure CDN-cacheminnet.

Om klienten använder byte-intervall begäranden (oavsett ursprung service), gäller följande:

- En *byte-intervall begäran* är en fakturerbar transaktion på CDN. När en klient skickar en begäran om byte-intervall, är den här begäran för en delmängd (intervall) av objektet. CDN svarar med bara en partiell del av det innehåll som begärs. Den här partiellt svar är en fakturerbar transaktion och det överföring är begränsad till storleken på intervallet svar (plus rubriker).

- När en begäran tas emot för endast en del av ett objekt (genom att ange en rubrik för byte-intervall), kan CDN hämta hela objektet till sin cache. Även om den faktureringsbara transaktionen från CDN avser ett partiellt svar, kan därför fakturerbara transaktionen från ursprunget omfatta i full storlek i objektet.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Hur mycket överföringsaktivitet utförs för att stödja cachen?
Varje gång en CDN POP behöver för att fylla sin cache, skickar den en begäran till ursprunget för objektet att cachelagras. Därför medför ursprunget en fakturerbar transaktion på varje cachemiss. Antalet cachemissar beror på ett antal faktorer:

- Hur komma innehållet är: Om innehållet har hög TTL (time-to-live) / giltighetstid standardvärden och är hanteras används ofta så förblir den populära i cacheminnet och det stora flertalet belastningen av CDN. Ett typiskt bra Cacheträff förhållande är väl över 90%, vilket innebär att mindre än 10% av klientbegäranden behöver gå tillbaka till ursprung, antingen för en cachemiss eller ett objekt uppdatera.

- Hur många noder behöver läsa in objektet: Varje gång en nod har lästs in ett objekt från ursprunget medför en fakturerbar transaktion. Därför kan resulterar fler globala innehåll (som öppnas från flera noder) i fler faktureringsbara transaktioner.

- TTL påverkan: En högre TTL-värde för ett objekt innebär att den måste hämtas från ursprunget mindre ofta. Det innebär också klienter, till exempel webbläsare, kan cachelagra objektet längre, vilket kan minska transaktionerna till CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hur hanterar jag mina kostnader så effektivt som möjligt?
Ange den längsta TTL möjligt på ditt innehåll. 
