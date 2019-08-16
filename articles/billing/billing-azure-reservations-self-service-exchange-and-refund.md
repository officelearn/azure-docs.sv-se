---
title: Självbetjänings utbyten och åter betalningar för Azure Reservations
description: Lär dig hur du kan utbyta eller återbetala Azure Reservations.
author: yashesvi
manager: yashesvi
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/05/2019
ms.author: banders
ms.openlocfilehash: 828bc3784a118a81adc4391b1bf222c00ee2025a
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543016"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Självbetjänings utbyten och åter betalningar för Azure Reservations

Azure Reservations tillhandahålla flexibilitet för att tillgodose dina behov av utveckling. Du kan byta en reservation mot en annan reservation av samma typ. Du kan också få återbetalning för en reservation, upp till 50 000 USD per år, om du inte längre behöver den.

Byte eller annullering av kapacitet via självbetjäning är inte tillgängligt för US Government-kunder med Enterprise-avtal. Andra typer av prenumerationer av amerikanska myndigheter inklusive betala per användning och CSP stöds.

Du måste ha ägar åtkomst till reservations ordningen för att kunna utbyta eller återbetala en befintlig reservation.

## <a name="exchange-an-existing-reserved-instance"></a>Exchange en befintlig reserverad instans

Du kan byta ut din reservation med tre snabb steg i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Välj de reservationer som du vill återbetala och klicka på **Exchange**.  
    ![Exempel bild som visar reservationer som ska returneras](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Välj den VM-produkt som du vill köpa och ange antal. Se till att den nya inköps summan är mer än den totala avkastningen. [Fastställ rätt storlek innan du köper](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Exempel bild som visar den virtuella dator produkten som ska köpas med ett utbyte](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Granska och Slutför transaktionen.  
    ![Exempel bild som visar den virtuella dator produkten som ska köpas med ett utbyte, och som slutför returen](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Om du vill återbetala en reservation går du till **reservations information** och klickar på **återbetala**.

## <a name="how-transactions-are-processed"></a>Hur transaktioner bearbetas

Först avbryter Microsoft den befintliga reservationen och debiterar det proportionella beloppet för den reservationen. Om det finns ett utbyte bearbetas det nya köpet. Microsoft bearbetar åter betalningar med någon av följande metoder, beroende på konto typ och betalnings metod:

### <a name="enterprise-agreement-customers"></a>Enterprise Agreement-kunder

Pengar läggs till i det monetära åtagandet för utbyte och åter betalning om det ursprungliga köpet gjordes med ett. Eventuella överanvänd fakturor sedan de ursprungliga inköpen har öppnats igen och omklassificeras för att se till att det ekonomiska åtagandet används. Om betalnings åtagandet som använder reservationen har köpts inte längre är aktiv, läggs krediten till i företagets aktuella affärs avtals villkor.

Om det ursprungliga köpet skedde som överanvändning, utfärdar Microsoft en kredit Nota.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Betala per användning-faktura betalningar och CSP-program

Den ursprungliga reservations inköps fakturan annulleras och en ny faktura skapas för åter betalningen. För utbyten visar den nya fakturan åter betalningen och det nya köpet. Bidrags beloppet justeras mot köpet. Om du bara återbetalar en reservation förblir det proportionella beloppet med Microsoft och det justeras mot ett framtida reservations köp.

### <a name="pay-as-you-go-credit-card-customers"></a>Betala per användning-kredit korts kunder

Den ursprungliga fakturan annulleras och en ny faktura skapas. Pengarna återbetalas till det kredit kort som användes för det ursprungliga köpet. Om du har ändrat kortet kontaktar du [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Avbrotts-, utbytes-och åter betalnings principer

Azure har följande principer för annulleringar, utbyten och åter betalningar.

**Exchange-principer**

- Du kan returnera flera befintliga reservationer för att köpa en ny reservation av samma typ. Du kan inte utbyta reservationer av en typ för en annan. Du kan till exempel inte returnera en VM-reservation för att köpa en SQL-reservation.
- Endast reservations ägare kan bearbeta ett utbyte. [Lär dig hur du lägger till eller ändrar användare som kan hantera en reservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Ett utbyte behandlas som en åter betalning och omköp – olika transaktioner skapas för annulleringen och det nya köpet. Den proportionella reservations mängden debiteras för de reservationer som du har handlat i. Du debiteras helt för det nya köpet. Den proportionella reservations mängden är det dagliga beräknade restvärdet för den reservation som returneras.
- Du kan byta ut eller återbetala reservationer även om det Enterprise-avtal som används för att köpa reservationen har upphört att gälla och förnyats som ett nytt avtal.
- Du kan ändra reservations egenskaper som storlek, region, kvantitet och period med ett utbyte.
- Den nya inköps summan måste vara lika med eller större än den returnerade mängden.
- Den nya reservationen som köpts som en del av Exchange har en ny term som börjar med tiden för Exchange.
- Det finns ingen påföljd eller de årliga gränserna för utbyten.

**Åter betalnings principer**
- Om du avbryter en reservation kan det finnas 12% tidig uppsägnings avgift.
- Den åter betalning du får för en annullering är det återstående proportionella saldot minus 12% tidigt uppsägnings avgifter. Om du vill avbryta går du till reservationen i Azure Portal och väljer **åter betalning**.
- Det totala bidrags beloppet får inte överstiga $50 000 USD i en rullande period på 12 månader.
- Åter betalningar beräknas utifrån det lägsta priset för antingen ditt inköps pris eller det aktuella priset för reservationen.
- Endast reservations ägare kan bearbeta en åter betalning. [Lär dig hur du lägger till eller ändrar användare som kan hantera en reservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft förbehåller sig rätten att debitera 12% straff för returer. Sanktionen debiteras inte för närvarande, men kommer att debiteras i framtiden.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Exchange non-Premium-lagring för Premium Storage

Du kan utbyta en reservation som köpts för en VM-storlek som inte stöder Premium Storage till en motsvarande VM-storlek. Till exempel en _F1_ för en _F1-enheter_. Om du vill göra Exchange-tjänsten går du till reservations information och klickar på **Exchange**. Exchange återställer inte den reserverade instansens period eller så skapar du en ny transaktion.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [hantera Azure reservations](billing-manage-reserved-vm-instance.md).
- Mer information om Azure Reservations finns i följande artiklar:
    - [Vad är Azure Reservations?](billing-save-compute-costs-reservations.md)
    - [Hantera reservationer i Azure](billing-manage-reserved-vm-instance.md)
    - [Förstå hur reservations rabatten tillämpas](billing-understand-vm-reservation-charges.md)
    - [Förstå reservations användningen för din prenumeration enligt principen betala per användning](billing-understand-reserved-instance-usage.md)
    - [Förstå reservations användningen för företags registreringen](billing-understand-reserved-instance-usage-ea.md)
    - [Windows-programkostnader som inte ingår i reservationer](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations i programmet Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)
