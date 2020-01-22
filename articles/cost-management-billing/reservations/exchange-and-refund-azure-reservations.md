---
title: Byten och återbetalning för Azure-reservationer via självbetjäning
description: Lär dig hur du kan byta eller återbetala Azure-reservationer.
author: yashesvi
manager: yashesvi
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: ea545919436201524a1c77b27e9b187f3b1c3b64
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314053"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Byten och återbetalning för Azure-reservationer via självbetjäning

Azure-reservationer ger flexibilitet för att tillgodose dina föränderliga behov. Du kan byta en reservation mot en annan reservation av samma typ. Du kan också få återbetalning för en reservation, upp till 50 000 USD per år, om du inte längre behöver den. Max gränsen för exporten gäller för alla reservationer inom avtals området med Microsoft.

Byte eller annullering av kapacitet via självbetjäning är inte tillgängligt för US Government-kunder med Enterprise-avtal. Andra typer av US Government-prenumerationer, inklusive betala per användning och CSP stöds.

Du måste ha ägaråtkomst till reservationsordningen för att byta eller återbetala en befintlig reservation.

## <a name="exchange-an-existing-reserved-instance"></a>Byta en befintlig reserverad instans

Du kan byta din reservation med tre snabba steg i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

1. Välj de reservationer som du vill få återbetalda och klicka på **Byt**.  
    ![Exempelbild som visar reservationer som ska returneras](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)
2. Välj den VM-produkt som du vill köpa och ange antal. Se till att den nya inköpssumman är högre än retursumman. [Fastställ rätt storlek innan du köper](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy).  
    ![Exempelbild som visar den VM-produkt som ska köpas med ett byte](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)
3. Granska och slutför transaktionen.  
    ![Exempelbild som visar den VM-produkt som ska köpas med ett byte, slutförande av returen](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)

Om du vill få en reservation återbetald går du till **Reservationsinformation** och klickar på **Återbetalning**.

## <a name="how-transactions-are-processed"></a>Så bearbetas transaktioner

Först avbryter Microsoft den befintliga reservationen och återbetalar det proportionella beloppet för den reservationen. Om det finns ett byte bearbetas det nya köpet. Microsoft bearbetar återbetalningar med någon av följande metoder, beroende på din kontotyp och betalningsmetod:

### <a name="enterprise-agreement-customers"></a>Enterprise-avtalskunder

Pengar läggs till i det ekonomiska åtagandet för byten och återbetalningar om det ursprungliga köpet gjordes med ett sådant. Eventuella överförbrukningsfakturor sedan de ursprungliga inköpen öppnas på nytt och omklassificeras för att se till att det ekonomiska åtagandet används. Om den period för ekonomiskt åtagande som använder den köpta reservationen inte längre är aktiv läggs krediten till i din aktuella period för ekonomiskt åtagande med Enterprise-avtal. Krediten är giltig i 90 dagar från och med återbetalningsdagen. Oanvänd kredit upphör att gälla efter 90 dagar.

Om det ursprungliga köpet gjordes som överförbrukning utfärdar Microsoft en kreditnota.

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>Betala per användning-fakturabetalningar och CSP-program

Den ursprungliga fakturan för reservationsköp annulleras och en ny faktura skapas för återbetalningen. För byten visar den nya fakturan återbetalningen och det nya köpet. Återbetalningsbeloppet justeras mot köpet. Om du bara återbetalade en reservation stannar det proportionella beloppet hos Microsoft och justeras mot ett framtida reservationsköp.

### <a name="pay-as-you-go-credit-card-customers"></a>Kunder med Betala per användning-kreditkort

Den ursprungliga fakturan annulleras och en ny faktura skapas. Pengarna återbetalas till det kreditkort som användes för det ursprungliga köpet. Om du har ändrat kortet [kontaktar du supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="cancel-exchange-and-refund-policies"></a>Principer för att avbryta, byta och återbetala

Azure har följande principer för att avbryta, byta och återbetala.

**Principer för byte**

- Du kan returnera flera befintliga reservationer för att köpa en ny reservation av samma typ. Du kan inte byta reservationer av en typ mot en annan. Till exempel kan du inte returnera en VM-reservation för att köpa en SQL-reservation.
- Endast reservationsägare kan bearbeta ett byte. [Lär dig hur du lägger till eller ändrar användare som kan hantera en reservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Ett byte bearbetas som en återbetalning och ett omköp – olika transaktioner skapas för annulleringen och det nya köpet. Det proportionella reservationsbeloppet återbetalas för de reservationer som du byter ut. Du debiteras helt för det nya köpet. Det proportionella reservationsbeloppet är det dagliga proportionella restvärdet för den reservation som returneras.
- Du kan byta eller återbetala reservationer även om det Enterprise-avtal som användes för att köpa reservationen har upphört att gälla och förnyats som ett nytt avtal.
- Du kan ändra alla reservationsegenskaper såsom storlek, region, kvantitet och period med ett byte.
- Den nya inköpssumman måste vara lika med eller större än det returnerade beloppet.
- Den nya reservationen som köpts som en del av bytet har en ny period som börjar vid tiden för bytet.
- Det finns ingen tilläggsavgift eller årliga gränser för byten.

**Principer för återbetalning**
- Om du avbryter en reservation kan det medföra en avgift på 12 % för uppsägning i förtid.
- Den återbetalning som du får för en annullering består av det återstående proportionellt beräknade saldot minus avgiften på 12 % för uppsägning i förtid. Om du vill avbryta går du till reservationen i Azure-portalen och väljer **Återbetalning**.
- Det totala återbetalningsbeloppet kan inte överstiga 50 000 USD i en rullande period på 12 månader.
- Återbetalningar beräknas baserat på det lägsta priset – antingen ditt inköpspris eller det aktuella priset för reservationen.
- Endast reservationsägare kan bearbeta en återbetalning. [Lär dig hur du lägger till eller ändrar användare som kan hantera en reservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
- Microsoft förbehåller sig rätten att debitera en tilläggsavgift på 12 % för returer. För närvarande utgår inte tilläggsavgiften, men den kommer att debiteras i framtiden.

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Byta icke-Premium-lagring mot Premium-lagring

Du kan byta en reservation som köpts för en VM-storlek som inte stöder Premium-lagring till en motsvarande VM-storlek som gör det. Det kan till exempel gälla byte av _F1_ mot en _F1s_. Om du vill göra bytet går du till Information om reservation och klickar på **Byt**. Bytet återställer inte perioden för den reserverade instansens och skapar inte någon ny transaktion.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- Du kan läsa mer om Azure-reservationer i följande artiklar:
    - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
    - [Hantera reservationer i Azure](manage-reserved-vm-instance.md)
    - [Förstå hur reservationsrabatten tillämpas](../manage/understand-vm-reservation-charges.md)
    - [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
    - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
    - [Kostnader för Windows-programvara ingår inte i reservationer](reserved-instance-windows-software-costs.md)
    - [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](/partner-center/azure-reservations)
