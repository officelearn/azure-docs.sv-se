---
title: Förstå Azure CDN fakturering | Microsoft Docs
description: Här beskrivs hur Azure CDN fakturering fungerar.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: v-deasim
ms.openlocfilehash: 218c493c772dc8fd212efaf60a0599fa2e896411
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163520"
---
# <a name="understanding-azure-cdn-billing"></a>Förstå Azure CDN fakturering

Här beskrivs fakturering struktur för innehåll på av Azure innehåll innehållsleveransnätverk (CDN).

## <a name="what-is-a-billing-region"></a>Vad är en fakturering region?
En fakturering region är ett geografiskt område som används för att avgöra vilken hastighet debiteras för leverans av objekt från Azure CDN. De aktuella fakturering zonerna och regioner är följande:

- Zon 1: Nordamerika, Europa, Mellanöstern och Afrika

- Zon 2: Asien och stillahavsområdet (inklusive Japan)

- Zon 3: Sydamerika

- Zon 4: Australien och Nya Zeeland

- Zon 5: Indien

Information om punkt förekomst (POP) regioner finns [Azure CDN POP platser per region](https://docs.microsoft.com/azure/cdn/cdn-pop-locations). En POP finns i Mexiko i Nordamerika-regionen och därför ingår i zonen 1. 

Information om priser för Azure CDN finns [innehållsleveransnätverk priser](https://azure.microsoft.com/is-is/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hur beräknas leverans avgifter per region?
Azure CDN fakturering region baseras på platsen för källservern som skickas till slutanvändaren. Klienten målet (fysiska plats) anses inte fakturering region.

Till exempel om en användare finns i Mexiko skickar en begäran och den här begäran besvaras av en server som finns i USA POP bero på peering eller trafik, blir fakturering regionen USA.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Vad är en fakturerbar Azure CDN-transaktioner?
HTTP (S)-begäran som slutar på CDN är en fakturerbar händelse som innehåller alla svarstyper av: lyckade och misslyckade eller andra. Olika svar kan dock ge olika trafiktyper belopp. Till exempel *304 ändras inte* och andra endast svar skapa lite trafik eftersom de är en liten huvudsvar, på samma sätt felsvar (exempelvis *404 hittades inte*) har fakturerbar tid men innebära en liten kostnad på grund av små svar nyttolast.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Andra Azure kostnader som är associerade med Azure CDN Använd?
Med hjälp av Azure CDN också debiteras vissa användning på de tjänster som används som ursprung för din objekt. Dessa kostnader är vanligtvis en liten del av den sammanlagda kostnaden för CDN-användning.

Om du använder Azure Blob storage startpunkt för ditt innehåll avgifter du också i följande lagringsutrymme för cache färger:

- Faktiska GB används: faktiska lagring av källobjekt.

- Överföringar i GB: mängden data som överförs till fyller CDN cacheminnen.

- Transaktioner: som behövs för att fylla i cachen.

Läs mer om Azure Storage fakturering [förstå Azure Storage fakturering – bandbredd, transaktioner och kapacitet](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Om du använder *värdbaserade tjänsten leverans*, kommer du betalar avgifter enligt följande:

- Azure-beräkning tid: compute-instanser som fungerar som originalet.

- Azure-beräkning överföring: data som överförs från de compute-instanserna för att fylla Azure CDN-cacheminnen.

Om klienten använder byte-intervall begäranden (oavsett ursprung service), gäller följande:

- En *byte-intervall begäran* är en fakturerbar transaktion på CDN. När en klient skickar en begäran om byte-intervall, är den här begäran för en del (mellan) i objektet. CDN svarar med bara en partiell del av det innehåll som begärs. Den här partiellt svar är en fakturerbar transaktion och överföringsbeloppet är begränsad till storleken på intervallet svar (plus huvuden).

- När en begäran tas emot för endast en del av ett objekt (genom att ange en byte-intervall rubrik) kan CDN hämta hela objektet till sin cache. Därför även om fakturerbar transaktionen från CDN för ett partiellt svar, kan fakturerbar transaktionen från ursprunget innebära att maximal storlek för objektet.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Hur mycket överföring aktiviteten utförs för att stödja cacheminnet?
Varje gång en CDN POP behöver fylla sin cache skickar den en begäran till ursprunget för objektet cachelagras. Därför ursprung ådrar sig en fakturerbar transaktion på varje cache-miss. Antal cachemissar beror på ett antal faktorer:

- Hur Cacheable ställs innehållet är: om innehållet har hög TTL (time-to-live) / förfallodatum värden och är ofta nås så förblir den populära i cachen och det stora flertalet av belastningen hanteras av CDN. En typisk bra cache-träff kvoten är korrekt över 90%, vilket innebär att mindre än 10% av klientbegäranden måste gå tillbaka till ursprung, för en cache-miss eller ett objekt antingen uppdatera.

- Hur många noder måste du ladda objektet: varje gång som en nod läser in ett objekt från ursprung, har en fakturerbar transaktion. Därför resulterar globala innehåll (öppnas från flera noder) i fler fakturerbar transaktioner.

- TTL påverkan: ett högre TTL-värde för ett objekt innebär att den måste hämtas från ursprunget mindre ofta. Det innebär också klienter, till exempel webbläsare, kan cachelagra objektet längre, vilket kan minska transaktioner till CDN.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hur hanterar jag mina kostnader effektivt?
Ange den längsta TTL möjligt på ditt innehåll. 
