---
title: Felsökning av Azure-reservationstyp är inte tillgänglig
description: Den här artikeln hjälper dig att förstå och felsöka reserverade instanser som visas som ej tillgängliga i Azure-portalen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 8575d9d86d8e720122a295cf92fa571ef33d5b4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92798210"
---
# <a name="troubleshoot-reservation-type-not-available"></a>Felsökning av reservationstyp är inte tillgänglig

Den här artikeln hjälper dig att förstå och felsöka reserverade instanser som visas som ej tillgängliga i Azure-portalen.

## <a name="symptoms"></a>Symtom

1. Logga in på [Azure-portalen](https://portal.azure.com/) och gå till **Reservationer** .
2. Välj **+ Lägg till** och välj sedan en produkt.
3. Välj fliken **Alla produkter** .
4. I listan över produkter väljer du en. Något av följande meddelanden kan visas:
    - `Product unavailable for the selected subscription or region. Contact support.`  
        :::image type="content" source="./media/troubleshoot-product-not-available/product-unavailable-message.png" alt-text="Exempel på meddelande som visar att produkten inte är tillgänglig i den valda prenumerationen eller regionen" lightbox="./media/troubleshoot-product-not-available/product-unavailable-message.png" :::
    - `The selected subscription does not have enough core quota remaining to purchase this product. Request quota increase`  
        :::image type="content" source="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" alt-text="Exempel på meddelande som visar att produkten inte är tillgänglig i den valda prenumerationen eller regionen" lightbox="./media/troubleshoot-product-not-available/not-enough-core-quota-message.png" :::

## <a name="cause"></a>Orsak

Vissa reservationer är inte tillgängliga för köp eftersom:

- Alla produkter av reserverade instanser är inte tillgängliga för köp i varje region
- Din prenumeration har en kvotbegränsning

### <a name="cause-1"></a>Orsak 1

Alla produkter av reserverade instanser inte är tillgängliga för köp. Azure beslutar att tillåta eller inte tillåta vissa produkter baserat på kostnader som är kopplade till kundernas rabatt. I andra fall erbjuder Azure inte vissa produkter på grund av kapacitetsvillkoren i vissa datacenter. Dessutom kan vissa Azure-produktutvecklingsgrupper nu tillåta vissa produkter eftersom de vill dra tillbaka en äldre produkt.

I andra fall inför Azure kapacitetsbegränsningar för olika produkter baserat på efterfrågan på resurserna i vissa regioner. En sådan begränsning kan till exempel göras när efterfrågan av en viss VM-storlek upphör i ett datacenter. I det här exemplet kan inte Azure garantera kapaciteten för kunder som har köpt en reservation för den storleken i den regionen. Slutligen finns det vissa produkter som är unika av olika orsaker. Sådana produkter görs bara tillgängliga för en liten, vald uppsättning kunder.

Varför visas vissa reservationer som inte är tillgängliga för köp i Azure-portalen? Svaret är att användarna skapar supportbegäranden som anger att de inte kan hitta de produkter som de vill ha. Azure-reservationsutvecklingsteamet upptäckte att alla produkter med meddelandet `Product unavailable` får färre supportbegäranden om de visas.

### <a name="cause-2"></a>Orsak 2

Din prenumeration har en kvotbegränsning Prenumerationer har gränser för hur många processorkärnor de kan förbruka. Gränsen gäller för vissa produkter av reserverade instanser, främst virtuella datorer. Azure vill se till att personer som köper en reserverad instans kan använda den. Azure gör en enkel summarisk kontroll i Azure-portalen för att se till att du har tillräckligt med lediga kärnor i din prenumeration för att distribuera den virtuella datorn och kan dra fördel av reservationsköpet.

Du kan enkelt lägga till en viss produkt i din varukorg och köpa en reservation. Azure utvärderar det totala antalet processorkärnor som är tillgängliga för din prenumeration och kontrollerar om antalet är större än antalet kärnor för det valda objektet.

Azure kontrollerar inte kvoten för reserverade instanser med **delat** omfång. Förmånen för den reserverade instansen för det delade omfånget gäller för alla prenumerationer i registreringen. Azure kan inte avgöra om du har tillräckligt med lediga kärnor i alla dina prenumerationer för att distribuera resursen. Oavsett kvoten tillåter Azure alltid att du väljer en VM-storlek när det valda omfånget delas.

Dessutom gör Azure ingen kvotkontroll för **rekommenderade** köp. Rekommendationerna baseras på aktiv användning. Azure förutsätter att du har tillräckligt med kärnor för att köra en speciell VM-storlek eftersom du redan har genererat den användning som krävs för att skapa rekommendationen.

## <a name="solution"></a>Lösning

Använd någon av följande lösningar för ditt problem, beroende på vilket felmeddelande du fick.

### <a name="solution-1"></a>Lösning 1

Om du ser ett meddelande om att _produkten inte är tillgänglig_ väljer du länken **Kontakta supporten** i felmeddelandet för att begära att lägga till ett undantag för din prenumeration. Undantag beviljas inte alltid.

### <a name="solution-2"></a>Lösning 2

Om du ser ett meddelande om att det _inte finns tillräckligt stor kärnkvot_ kan du ändra omfånget till **Delad** . När du har köpt reservationen kan du ändra reservationsomfånget från **Delad** till **Enskild** .

Alternativt kan du välja länken **Begära kvotökning** i felmeddelandet för att begära ytterligare processorkärnkvot för din prenumeration.

## <a name="next-steps"></a>Nästa steg

- Mer information om alternativ för reservationsomfång finns i avsnittet om [reservationsomfång](prepare-buy-reservation.md#scope-reservations).