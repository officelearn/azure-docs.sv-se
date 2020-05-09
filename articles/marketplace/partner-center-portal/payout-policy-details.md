---
title: Information om utbetalnings princip – Azure Marketplace
description: Information om utbetalnings principer, inklusive scheman och återkoppling.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6aa40d0914237a28c7bbd32b15bf0d8d02140192
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783557"
---
# <a name="payout-policy-details"></a>Information om utbetalningsprincip

I den här artikeln beskrivs Microsofts utbetalnings process, utbetalnings schema, var du hittar status för en utbetalning och en återställnings princip.

## <a name="where-to-find-upcoming-payouts"></a>Var du hittar kommande utbetalningar

I Partner Center väljer du **utbetalning** längst upp till höger i portalen:

![Visar utbetalnings ikonen längst upp till höger i Partner Center-portalen.](./media/payout-overview.png)

> [!TIP]
> Alla konto roller har inte åtkomst till utbetalnings information. Mer information finns i [roller och behörigheter för att få åtkomst till utbetalnings rapporten](./payout-summary.md).

## <a name="payment-schedules"></a>Betalnings scheman

I följande avsnitt beskrivs vår utbetalnings process.

### <a name="enterprise-agreement-transactions-after-may-1-2020"></a>Enterprise-avtal transaktioner efter den 1 maj 2020

#### <a name="update-to-our-commercial-marketplace-publisher-payout-model"></a>Uppdatera till vår utbetalnings modell för den kommersiella Marketplace-utgivaren

Från och med den 1 maj 2020 uppdaterar vi vår utlösare som är relaterad till produkter som köpts på Azure Marketplace eller AppSource av kunder med en Microsoft-Enterprise-avtal. När en kund köper en produkt från Azure Marketplace eller AppSource med sina befintliga Microsoft-Enterprise-avtal för transaktioner efter den 1 maj 2020 kommer vi att börja utfärda inbetalningar i nästa utbetalnings cykel 30 dagar efter kund faktura. Transaktioner där en kund använder ett kredit kort har inte ändrats och kommer att fortsätta att ha en 30-dagars företags period innan utbetalning. Tabellerna visar information om utbetalnings schema.

> [!NOTE]
> Se [processen för kund icke-betalning](#process-for-customer-non-payment) nedan för de åtgärder som vi vidtar om kunden inte betalar, men vi redan har utfärdat en utbetalning till dig.

| Händelse  | Date  | Partner synlighet: rapporten utbetalning för partner Center  |  Partner synlighet: Partner Center Analytics\* |
| --- | --- | --- | --- |
| Transaktion eller månad för användning | 8/1/2020 – 8/31/2020 | E.t. | **Användnings rapport**: ny förbrukning som visas (uppdateras var fjärde timme)<br>**Order rapport**: ej tillämpligt |
| Slut period (månad) | 8/31/2020 | E.t. | **Användnings rapport**: slut på månads förbrukning som visas<br>**Order rapport**: ej tillämpligt |
| Order genererad | 9/3/2020 – 9/7/2020 | E.t. | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Beräkna utbetalning | 9/4/2020 – 9/10/2020 | Markerat som **obearbetat** i transaktions historik på instrument panelen för utbetalning | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Månatlig utbetalning | 10/5/2020 | Markerat som **kommande** i transaktions historik på instrument panelen för utbetalningar | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Utbetalnings datum | 10/15/2020 | Markerat som **skickat** i transaktions historik och i avsnittet betalningar på instrument panelen för utbetalning | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Kund faktura insamlad | 12/1/2020 | Markerat som **skickat** i transaktions historik och i avsnittet betalningar på instrument panelen för utbetalning | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva  |
|  |  |  |  |

\*Användnings-och order rapporter är tillgängliga i avsnittet analysera i Partner Center.

### <a name="customers-who-pay-using-credit-card-or-invoice"></a>Kunder som betalar med kredit kort eller faktura

Alla inköp med ett kredit kort eller en månads faktura har en 30-dagars period för att säkerställa att penning beloppen rensas och det inte finns några åter betalningar eller misstänkt bedrägerier.

| Händelse  | Date  | Partner synlighet: rapporten utbetalning för partner Center  |  Partner synlighet: Partner Center Analytics\*  |
| --- | --- | --- | --- |
| Transaktion eller månad för användning | 8/1/2019 - 8/31/2019 | E.t. | **Användnings rapport**: ny förbrukning som visas (uppdateras var fjärde timme)<br>**Order rapport**: ej tillämpligt |
| Slut period (månad) | 8/31/2019 | E.t. | **Användnings rapport**: slut på månads förbrukning som visas<br>**Order rapport**: ej tillämpligt |
| Order genererad | 9/3/2019 – 9/7/2019 | E.t. | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Kund faktura insamlad | 9/7/2019 – 9/10/2019 | E.t. | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Beräkna utbetalning | 9/8/2019 -9/12/2019 | Markerat som **obearbetat** i transaktions historiken på instrument panelen för utbetalning | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Månatlig utbetalning | 11/5/2019\* | Markerat som **kommande** i transaktions historiken för utbetalnings instrument panelen | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Utbetalnings datum | 11/15/2019 | Markerat som **skickat** i transaktions historiken och i avsnittet betalningar på instrument panelen för utbetalning | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
|  |  |  |  |

\*Användnings-och order rapporter är tillgängliga i avsnittet analysera i Partner Center.

### <a name="enterprise-agreement-transactions-prior-to-may-1-2020"></a>Enterprise-avtal transaktioner före den 1 maj 2020

Alla köp som inträffar före detta datum bearbetas och betalas enligt schemat nedan efter att Microsoft har samlat in betalning från kunder och bearbetat Marketplace-avgiften.

| Händelse  | Date  | Partner synlighet: rapporten utbetalning för partner Center  |  Partner synlighet: Partner Center Analytics\*  |
| --- | --- | --- | --- |
| Transaktion eller månad för användning | 8/1/2019 – 8/31/2019 | E.t. | **Användnings rapport**: ny förbrukning som visas (uppdateras var fjärde timme)<br>**Order rapport**: ej tillämpligt |
| Slut period (månad) | 8/31/2019 | E.t. | **Användnings rapport**: slut på månads förbrukning som visas<br>**Order rapport**: ej tillämpligt |
| Order genererad | 9/3/2019 – 9/7/2019 | E.t. | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Kund faktura insamlad | 12/1/2019 | E.t. | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Beräkna utbetalning | 12/5/2019 – 12/7/2019 | Markerat som **obearbetat** i transaktions historiken på instrument panelen för utbetalning | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Månatlig utbetalning | 1/5/2019 | Markerat som **kommande** i transaktions historiken för utbetalnings instrument panelen | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
| Utbetalnings datum | 1/15/2019 | Markerat som **skickat** i transaktions historik och i avsnittet betalningar på instrument panelen för utbetalning | **Användnings rapport**: förbrukning som visas med Ordernr/OrderLineItemID<br>**Order rapport**: kund order visas som aktiva |
|  |  |  |  |

\*Användnings-och order rapporter är tillgängliga i avsnittet analysera i Partner Center.

## <a name="process-for-customer-non-payment"></a>Process för ej betalnings kund

I sällsynta fall kan Microsoft inte samla in betalningar från kunder för sina kommersiella marknads marknads inköp. När en kund inte betalar Microsoft enligt deras fakturerings schema påbörjar vi samlings processen. Den här processen tar cirka fyra månader och inbegriper beständig kommunikation från Microsoft. Om betalningen inte tas emot i slutet av den här processen, skriver Microsoft ut medlen som ej insamlade.

Efter utbetalnings processen som har framställts här kan Microsoft redan betala ut pengar till utgivare (du) som slutligen inte kan samlas in. Därför har vi en process för att stämma av dessa belopp. För att se till att du har en varning om att din (redan inlevererad) betalning kan stämmas av får du ett meddelande när en kund befinner sig i Inkasso processen och köp kan förmodligen skrivas av.

Microsoft kommer att koppla tillbaka eventuella utbetalningar som redan betalats till dig med någon av följande metoder: (1) Microsoft kan subtrahera de obetalda beloppen från framtida utbetalningar. om till exempel $1 000 i utbetalningar betraktas som ej kan samlas in och skrivs av, kommer dina framtida utbetalningar att bli kvar tills $1 000 återställs eller (2) Microsoft kan begära en åter betalning eller faktura utgivare för alla insamlade belopp.

Följande är ett exempel schema:

| Händelse | Ungefärligt datum | Partner synlighet |
| --- | --- | --- |
| Exempel på utbetalnings datum | 10/15/2020 | Markerat som **skickat** i transaktions historik och i avsnittet betalningar i instrument panelen för utbetalning |
| <font color="red">Om kunden inte betalar Microsoft</font> | 12/2/2020 – 12/5/2020 | Ingen ändring, samma som ovan |
| Kunden får ett e-postmeddelande om betalning för första sent | 12/6/2020 | Inga |
| Kunden får regelbundna e-postmeddelanden med ökande angelägenhets grad | 12/7/2020 – 1/31/2020 | Inga |
| Utgivaren har meddelats att skriva av är troligt vis | 1/7/2020 | E-postmeddelande som skickas till utgivaren om att deras kunder ännu inte har skickat någon betalning. Transaktions-ID och dollar belopp ingår. |
| Kund mottar uppsägnings meddelande | 2/1/2020 | Inga |
| Samlings processens slut/medel skrivs av | 2/15/2020 | E-postmeddelande som skickas till utgivare som fonder har skrivits av. Transaktions-ID och dollar belopp ingår. |
| Utbetalningen dras av | 3/1/2020 | I Publisher visas en negativ transaktion i Partner Center utbetalnings instruktion |
| Utbetalningen nekas | 3/15/2020 | Framtida utbetalningar visas i utbetalnings instruktionen för partner Center. Utgivare får ingen betalning förrän saldot inte längre är negativt.  |
|||

## <a name="next-step"></a>Nästa steg

- [Skatteinformation](./tax-details-paid-transactions.md)
