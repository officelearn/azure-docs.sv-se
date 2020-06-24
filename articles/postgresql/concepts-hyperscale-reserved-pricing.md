---
title: Reserverade beräknings priser – Azure Database for PostgreSQL-Scale Scale (citus)
description: Förskotts beräkning för Azure Database for PostgreSQL-storskalig (citus) Compute-resurser med reserverad kapacitet
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85218069"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Förskotts beräkning för Azure Database for PostgreSQL-storskalig (citus) Compute-resurser med reserverad kapacitet

Azure Database for PostgreSQL – storskalig (citus) hjälper dig nu att spara pengar genom att betala för beräknings resurser jämfört med priser enligt principen betala per användning. Med citus-reserverad kapacitet, gör du ett överplacerat åtagande på citus-servergruppen () för en eller tre års period för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa citus-reserverad kapacitet måste du ange Azure-regionen, reservations perioden och fakturerings frekvensen.

> [!IMPORTANT]
> Den här sidan är till för reserverad kapacitet för Azure Database for PostgreSQL – storskalig (citus). Se [den här sidan](/azure/postgresql/concept-reserved-pricing) om du vill ha information om reserverad kapacitet för Azure Database for PostgreSQL – en server.

Du behöver inte tilldela reservation till vissa citus-Server grupper. En citus-servergrupp (redan körs) eller som nyligen distribueras får automatiskt fördelarna med reserverad prissättning. Genom att köpa en reservation är du förbetald för beräknings kostnaderna under en period på en eller tre år. När du köper en reservation debiteras inte längre de citus-beräknings avgifter som matchar reservations attributen enligt priserna för betala per användning. En reservation omfattar inte program vara, nätverk eller lagrings kostnader som är associerade med citus-Server grupperna. I slutet av reservations perioden upphör fakturerings förmånen och citus-Server grupperna debiteras enligt priset för betala per användning. Reservationer förnyas inte. Information om priser finns i det [reserverade kapacitets erbjudandet Azure Database for PostgreSQL – citus)](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

Du kan köpa storskalig (citus) reserverad kapacitet i [Azure Portal](https://portal.azure.com/). Betala för reservationen [i förväg eller via månadsbetalningar](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations). Så här köper du den reserverade kapaciteten:

* Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
* För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen.
* För ett CSP-program (Cloud Solution Provider) kan endast administratörs agenter eller försäljnings agenter köpa citus-reserverad kapacitet.

Mer information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp finns i [förstå Azure reservation-användning för din företags registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) och [förstå Azure reservation-användning för din prenumeration enligt principen betala per](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)användning.

## <a name="determine-the-right-server-group-size-before-purchase"></a>Fastställ rätt storlek på Server gruppen innan köpet

Storleken på reservationen bör baseras på den totala mängden beräkning som används av den befintliga eller närmast distribuerade koordinatorn och arbetsnoder i citus-servergrupper inom en angiven region.

Anta till exempel att du kör en citus-Server grupp med 16 vCore-koordinatorer och tre vCore Worker-noder. Dessutom antar vi att du planerar att distribuera under nästa månad till en extra storskalig (citus)-Server grupp med en 32 vCore-koordinator och två 4 vCore Worker-noder. Låt oss anta att du behöver dessa resurser i minst ett år.

I så fall bör du köpa ett års reservation för

* Totalt 16 virtuella kärnor + 32 virtuella kärnor = 48 virtuella kärnor för koordinator-noder
* Totalt tre noder x 8 virtuella kärnor + 2 noder x 4 virtuella kärnor = 24 + 8 = 32 virtuella kärnor för arbetsnoder

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Köp Azure Database for PostgreSQL reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj **Lägg till** och sedan i fönstret köp reservationer väljer du **Azure Database for PostgreSQL** för att köpa en ny reservation för dina postgresql-databaser.
4. Välj storskalig (citus) Compute-typ till köp och klicka på knappen **Välj** .
5. Granska kvantiteten för vald beräknings typ på fliken **produkter** .
4. Gå vidare till fliken **köp + granska** för att slutföra köpet.

I följande tabell beskrivs obligatoriska fält.

| Fält        | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Prenumeration | Prenumerationen som används för att betala för den Azure Database for PostgreSQL reserverade kapacitets reservationen. Betalnings metoden för prenumerationen debiteras mot startkostnader för den Azure Database for PostgreSQL reserverade kapacitets reservationen. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller ett enskilt avtal med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). För en företags prenumeration dras avgifterna från registreringen av betalnings åtagandet eller debiteras som överanvändning. För en enskild prenumeration med priset betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen.                                                                                  |
| Omfång        | VCore-reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer: <br><br> **Delat** används reservations rabatten för VCore på citus-Server grupper som körs i alla prenumerationer i din fakturerings kontext. För företags kunder är det delade omfånget registreringen och innehåller alla prenumerationer i registreringen.  För Betala per användning-kunder gäller den delade omfattningen samtliga Betala per användning-prenumerationer som kontoadministratören har skapat. <br><br> **Enskild prenumeration** tillämpas reservations rabatten för VCore på citus-Server grupper i den här prenumerationen. <br><br> **En enda resurs grupp,** reservations rabatten tillämpas på citus-Server grupper i den valda prenumerationen och den valda resurs gruppen i den prenumerationen. |
| Region       | Den Azure-region som omfattas av reservationen Azure Database for PostgreSQL – storskalig (citus) reserverad kapacitet.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Term         | Ett år eller tre år.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Kvantitet     | Mängden data bearbetnings resurser som köps i citus (reserverad kapacitet reservation). I synnerhet antalet koordinatorer eller arbetsnodens virtuella kärnor i den valda Azure-regionen som reserveras och som kommer att få fakturerings rabatten. Om du t. ex. kör (eller planerar att köra) citus-Server grupper med den totala beräknings kapaciteten för 64 koordinator Node virtuella kärnor och 32 Worker Node virtuella kärnor i regionen USA, östra, anger du kvantitet som 64 och 32 för koordinator-och arbetsnoder för att maximera förmånen för alla servrar.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilitet för vCore-storlek

vCore storleks flexibilitet hjälper dig att skala upp-eller ned-koordinatorn och arbetsnoder inom en region, utan att förlora den reserverade kapacitets förmånen.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Reservations rabatten för vCore tillämpas automatiskt på antalet citus-Server grupper som matchar Azure Database for PostgreSQL reserverade kapacitets omfånget och attributen. Du kan uppdatera omfånget för Azure Database för PostgreSQL – citus (Restore Restore Capacity) genom att Azure Portal, PowerShell, CLI eller genom API: et.

Du kan läsa mer om Azure-reservationer i följande artiklar:

* [Vad är Azure-reservationer?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Hantera Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Förstå reservationsrabatter i Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Förstå reservationsanvändning för din Betala per användning-prenumeration](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Förstå reservationsanvändning för din Enterprise-registrering](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
