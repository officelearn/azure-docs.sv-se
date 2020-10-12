---
title: Spara kostnader med den reserverade instansen av Azure VMware-lösningen
description: Lär dig hur du köper en reserverad instans för Azure VMware-lösningen.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: a477b1730d70ec729a2be333b545b6faeb009998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91492493"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Spara kostnader med Azure VMware-lösningen

När du genomför en reserverad instans av Azure VMware-lösningen kan du spara pengar. Reservations rabatten tillämpas automatiskt på antalet Azure VMware-lösningar som körs och som matchar reservations omfånget och attributen. Du behöver inte tilldela en reservation till en dedikerad värd för att hämta rabatterna. En reserverad instans köps bara täcker beräknings delen av din användning och inkluderar kostnader för program varu licenser. Se [översikten över Azure VMware-lösningen](introduction.md).

## <a name="purchase-restriction-considerations"></a>Överväganden vid köp av begränsningar

Reserverade instanser är tillgängliga med vissa undantag.

-   **Moln**   – Reservationer är bara tillgängliga i de regioner som anges på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Otillräcklig kvot**   -En reservation som är begränsad till en enda/delad prenumeration måste ha värd kvoten tillgänglig i prenumerationen för den nya reserverade instansen. Du kan [skapa en begäran om kvot ökning](enable-azure-vmware-solution.md) för att lösa problemet.

-   **Erbjudande för erbjudandet**– du behöver ett [Azure-Enterprise-avtal (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements)   med Microsoft.

-   **Kapacitets begränsningar**   I sällsynta fall begränsar Azure köpet av nya reservationer för Azure VMware-lösningar värd-SKU: er, på grund av låg kapacitet i en region.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa en reserverad instans av en Azure VMware-lösning värd instans i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Betala för reservationen [fram eller med månads betalningar](../cost-management-billing/reservations/prepare-buy-reservation.md).

Dessa krav gäller för att köpa en reserverad dedikerad värd instans:

-   Du måste ha ägar rollen för minst en EA-prenumeration eller en prenumeration med en taxa enligt principen betala per användning.

-   För EA-prenumerationer måste alternativet **Lägg till reserverade instanser**   vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.

Så här köper du en instans:

1. Logga in på  [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster**   >  **reservationer**.

3. Välj **Lägg till**   för att köpa en ny reservation och välj sedan **Azure VMware-lösning**.

4. Fyll i obligatoriska fält. Kör Azure VMware-lösningar som matchar de attribut du väljer kvalificera för att hämta reservations rabatten. Det faktiska antalet Azure VMware-lösningar som erhåller rabatten beror på omfattning och kvantitet som väljs.

   Om du har ett EA-avtal kan du använda **alternativet Lägg till fler**   för att snabbt lägga till fler instanser. Alternativet är inte tillgängligt för andra prenumerations typer.

   | Field        |  Beskrivning |
   | ------------ | ------------ |
   | Prenumeration | Prenumerationen som används för att betala för reservationen. Reservationen debiteras via prenumerationens betalningsmetod. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft-kundavtal eller en enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras från saldot för det ekonomiska åtagandet om det finns ett sådant eller debiteras som överförbrukning. För en prenumeration med priser enligt principen betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen. |
   | Omfång        | Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer:<br><ul><li><b>Definitions område för enskild resurs grupp – tillämpar reservations rabatten på de matchande resurserna enbart i den valda resurs gruppen.</li><li><b>Enda prenumerations omfång – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.</li><li><b>Delat omfång – tillämpar reservations rabatten på matchande resurser i berättigade prenumerationer som finns i fakturerings kontexten. För EA-kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.</li></ul>       |
   | Region       | Den Azure-region som omfattas av reservationen.   |
   | Värd storlek    | AV36    |
   | Period         | Ett år eller tre år.  |
   | Kvantitet     | Antalet instanser som köps i reservationen. Antalet är antalet Azure VMware-lösningar som kan få fakturerings rabatt.    |

## <a name="usage-data-and-reservation-utilization"></a>Användnings data och reservations användning

Dina användningsdata har ett effektivt pris på noll för användning som täcks av reservationen. Du kan se vilken Azure VMware Solution-instans som har tagit emot reservations rabatten för varje reservation.

Mer information om hur reservations rabatter visas i användnings data och du är en EA-kund finns i [förstå Azure reservation-användning för företagets registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md). Om du har en enskild prenumeration kan du läsa mer i avsnittet om [hur du betalar per användning för din prenumeration enligt principen betala per användning](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändra en reservation efter köpet

Du kan göra följande typer av ändringar i en reservation efter köpet:

-   Uppdatera reservationsomfång

-   Flexibilitet för instans storlek (om tillämpligt)

-   Ägarskap

Du kan också dela upp en reservation i mindre segment eller slå samman reservationer. Ingen av ändringarna orsakar en ny kommersiell transaktion eller ändrar slut datumet för reservationen.

>[!NOTE]
>När du har köpt din reservation kommer du inte att kunna göra följande typer av ändringar direkt:
>
> - En befintlig reservations region
> - SKU
> - Kvantitet
> - Varaktighet
>
>Du kan dock *byta*ut   en reservation om du vill göra ändringar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Mer information finns i [självbetjänings utbyten och åter betalningar för Azure reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).
