---
title: Spara kostnader med den reserverade instansen av Azure VMware-lösningen
description: Lär dig hur du köper en reserverad instans för Azure VMware-lösningen.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: bac2497c637a301c7ce8cbc44fc6945c3ef43b06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370686"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Spara kostnader med Azure VMware-lösningen

När du genomför en reserverad instans av [Azure VMware-lösningen](introduction.md)sparar du pengar. Reservations rabatten tillämpas automatiskt på de Azure VMware-lösningar som körs och som matchar reservations omfånget och attributen. Du behöver inte tilldela en reservation till en dedikerad värd för att hämta rabatterna. Ett inköp med reserverad instans täcker bara beräknings delen av din användning och innehåller kostnader för program varu licenser. 


## <a name="purchase-restriction-considerations"></a>Överväganden vid köp av begränsningar

Reserverade instanser är tillgängliga med vissa undantag.

-   **Moln** – reservationer är bara tillgängliga i de regioner som anges på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Otillräcklig kvot** – en reservation som är begränsad till en enda/delad prenumeration måste ha värd kvoten tillgänglig i prenumerationen för den nya reserverade instansen. Du kan [skapa en begäran om kvot ökning](enable-azure-vmware-solution.md) för att lösa problemet.

-   **Erbjudande för erbjudandet**– du behöver ett [Azure-Enterprise-avtal (EA)](../cost-management-billing/manage/ea-portal-agreements.md) med Microsoft.

-   **Kapacitets begränsningar** – i sällsynta fall begränsar Azure inköpet av nya reservationer för Azure VMware-lösningen värd-SKU: er på grund av låg kapacitet i en region.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa en reserverad instans av en Azure VMware-lösning värd instans i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Betala för reservationen [i förväg eller via månadsbetalningar](../cost-management-billing/reservations/prepare-buy-reservation.md).

Dessa krav gäller för att köpa en reserverad dedikerad värd instans:

-   Du måste ha ägar rollen för minst en EA-prenumeration eller en prenumeration med en taxa enligt principen betala per användning.

-   För EA-prenumerationer måste alternativet **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.

Så här köper du en instans:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Alla tjänster** > **Reservationer**.

3. Välj **Lägg till** för att köpa en ny reservation och välj sedan **Azure VMware-lösning**.

4. Ange de obligatoriska fälten. Genom att köra Azure VMware-lösningar som matchar attributen väljer du kvalificera för att hämta reservations rabatten. Det faktiska antalet Azure VMware-lösningar som erhåller rabatten beroende på vald omfattning och kvantitet.

   Om du har ett EA-avtal kan du använda **alternativet Lägg till fler** för att snabbt lägga till fler instanser. Alternativet är inte tillgängligt för andra prenumerations typer.

   | Fält        |  Beskrivning |
   | ------------ | ------------ |
   | Prenumeration | Prenumerationen som används för att betala för reservationen. Reservationen debiteras via prenumerationens betalningsmetod. Prenumerations typen måste vara ett Enterprise-avtal (erbjudande nummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft-kundavtal eller en enskild prenumeration med priser enligt principen betala per användning (erbjudande nummer: MS-AZR-0003P eller MS-AZR-0023P). Avgifterna dras från saldot för det ekonomiska åtagandet om det finns ett sådant eller debiteras som överförbrukning. För en prenumeration med priser enligt principen betala per användning debiteras avgifterna till kredit kortet eller faktura betalnings metoden för prenumerationen. |
   | Omfång        | Reservationens omfång kan omfatta en prenumeration eller flera prenumerationer (delad omfattning). Om du väljer:<br><ul><li><b>Definitions område för enskild resurs grupp – tillämpar reservations rabatten på de matchande resurserna enbart i den valda resurs gruppen.</li><li><b>Enda prenumerations omfång – tillämpar reservations rabatten på de matchande resurserna i den valda prenumerationen.</li><li><b>Delat omfång – tillämpar reservations rabatten på matchande resurser i berättigade prenumerationer som finns i fakturerings kontexten. För EA-kunder är fakturerings kontexten registreringen. För enskilda prenumerationer med betalning per användning är faktureringsomfånget alla berättigade prenumerationer som kontoadministratören har skapat.</li></ul>       |
   | Region       | Den Azure-region som omfattas av reservationen.   |
   | Värd storlek    | AV36    |
   | Period         | Ett år eller tre år.  |
   | Kvantitet     | Antalet instanser som köps i reservationen. Antalet är antalet Azure VMware-lösningar som kan få fakturerings rabatt.    |

## <a name="usage-data-and-reservation-usage"></a>Användnings data och reservations användning

Din användning som erhåller en reservations rabatt har ett effektivt pris på noll. Du kan se vilken Azure VMware Solution-instans som har tagit emot reservations rabatten för varje reservation.

Mer information om hur reservations rabatter visas i användnings data:

- För EA-kunder, se [förstå Azure reservation-användning för företagets registrering](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- För enskilda prenumerationer, se [förstå Azure reservation-användning för din prenumeration enligt principen betala per](../cost-management-billing/reservations/understand-reserved-instance-usage.md) användning

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
>Du kan dock *byta* ut en reservation om du vill göra ändringar.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).