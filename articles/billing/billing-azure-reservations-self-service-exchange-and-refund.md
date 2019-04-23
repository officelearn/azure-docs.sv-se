---
title: Självbetjäning utbyte och återbetalningar för Azure-reservationer | Microsoft Docs
description: Lär dig hur du kan byta eller återbetala Azure reservationer.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 54578746ea8029a760663edc456660f98358abc5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009318"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Självbetjäning utbyte och återbetalningar för Azure-reservationer

Azure reservationer ger flexibilitet för att möta dina växande behov. Du kan byta en reservation för en annan reservation av samma typ. Du kan också återbetala en reservation, upp till 50 000 USD per år, om du inte längre behöver den.

Självbetjäning kapaciteten för exchange och Avbryt är inte tillgängligt för US Government Enterprise Agreement-kunder. Andra typer av US Government prenumerationer, inklusive betala per användning och CSP stöds.

Du måste ha ägaråtkomst på Reservationsbeställning att byta ut eller ersätta en befintlig reservation.

## <a name="exchange-an-existing-reserved-instance"></a>Byta en befintlig reserverad instans

Du kan byta din reservation med tre enkla steg i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Välj reservationer som du vill återbetala och klicka på **Exchange**.  
    ![Exempelbild som visar reservationer att returnera](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. Välj VM-produkt som du vill köpa och anger du ett antal. Se till att det nya köp totala antalet är större än Returnerar summan. [Avgör om rätt storlek innan du köper](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Exempelbild som visar en VM-produkt för att köpa med en exchange](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. Granska och Slutför transaktionen.  
    ![Exempelbild som visar en VM-produkt att köpa med en exchange som du har slutfört avkastningen](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

Gå till att ersätta en reservation **reservationsinformation** och klicka på **återbetalning**.

## <a name="how-transactions-are-processed"></a>Hur transaktioner bearbetas

Först Microsoft avbryter den befintliga reservationen och återbetalningar proportionellt beloppet för den reservationen. Om det finns ett utbyte, bearbetas det nya köpet. Microsoft bearbetar återbetalningar med någon av följande metoder, beroende på vilken typ av konto och betalningsmetod:

### <a name="enterprise-agreement-customers"></a>Kunder med Enterprise-avtal

Pengar läggs till i Summa utbyten och återbetalningar om det ursprungliga köpet gjordes med någon. Inga överförbrukning fakturor eftersom de ursprungliga inköp på nytt och rerated för att se till att åtagandebelopp används. Om en summa i förskott termen med hjälp av reservationen köptes inte längre är aktiv har kredit lagts till en enterprise-avtal åtagandebelopp avtalsperiod.

Om det ursprungliga köpet gjordes som överförbrukning, skickar Microsoft en kreditnota.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Betala per användning faktura och CSP-programmet

Den ursprungliga reservation inköpsfakturan avbryts och sedan en ny faktura skapas för återbetalning. Den nya fakturan visar bidraget och ny för utbyten. Återbetalningsbeloppet justeras mot köpet. Om du bara återbetalas en reservation, proportionell beloppet förblir med Microsoft och den har justerats mot framtida reservationsköp.

### <a name="pay-as-you-go-credit-card-customers"></a>Betala per användning depositionsperiod

Den ursprungliga fakturan avbryts och en ny faktura skapas. Pengarna tillbaka till kreditkortet som användes för det ursprungliga köpet. Om du har ändrat ditt kort [supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="exchange-policies"></a>Exchange-principer

- Du kan returnera flera befintliga reservationer för att köpa en ny reservation av samma typ. Du kan inte byta reservationer av samma typ för en annan. Exempel: du kan inte returnera en VM-reservation om du vill köpa en reservation för SQL.
- Endast reservation ägare kan bearbeta ett utbyte. [Lär dig hur du lägga till eller ändra användare som kan hantera en reservation](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation).
- Ett utbyte behandlas som en återbetalning och återköp – olika transaktioner skapas för detta och det nya köpet. Proportionell reservation beloppet återbetalas för reservationer som du inbyte. Du debiteras fullständigt för det nya inköpet. Proportionell reservation beloppet är dagliga proportionell restvärde reservationen som returneras.
- Du kan byta ut eller återbetalning reservationer även om enterprise-avtal som används för att köpa reservationen har upphört att gälla och har förnyats som ett nytt avtal.
- Du kan ändra reservation egenskaper, till exempel storlek, region, kvantitet och period med ett utbyte.
- Den nya köp totalen ska vara lika med eller vara större än det returnerade.
- Den nya reservationen som köps som en del av exchange har en ny term från och med tidpunkten för exchange.
- Det finns inga särskilda avgifter eller årliga gränser för utbyten.

## <a name="refund-policies"></a>Återbetalningsprinciper för

- Din totala återbetalning kan inte överstiga 50 000 USD i 12 månaders rullande fönster.
- Endast reservation ägare kan bearbeta en återbetalning. [Lär dig hur du lägga till eller ändra användare som kan hantera en reservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft förbehåller sig rätten att debitera en 12% särskilda avgifter för alla returnerar även om den särskilda avgifter för närvarande inte debiteras.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Exchange-premium storage för premium storage

Du kan byta en reservation för en VM-storlek som inte stöder premium storage till en VM-storlek som har köpt. Till exempel en _F1_ för en _F1s_. Om du vill göra exchange, gå till information om Reservation och klicka **Exchange**. Exchange inte återställa att perioden för den reserverade instansen eller skapa en ny transaktion.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Läs hur du hanterar en reservation i [hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).
- Om du vill veta mer om Azure reservationer, finns i följande artiklar:
    - [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
    - [Hantera reservationer i Azure](billing-manage-reserved-vm-instance.md)
    - [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
    - [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
    - [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
    - [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
    - [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](/partner-center/azure-reservations)
