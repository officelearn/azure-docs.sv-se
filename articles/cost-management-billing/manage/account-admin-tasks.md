---
title: Kontoadministratörsuppgifter i Azure-portalen
description: Beskriver hur du utför betalningsåtgärder i Azure-portalen
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: ad39b9980562c345aff87f4e0771f265011886b3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683308"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Kontoadministratörsuppgifter i Azure-portalen

I den här artikeln beskrivs hur du utför följande uppgifter i Azure-portalen:
- Hantera prenumerationens betalningsmetoder
- Ta bort prenumerationens utgiftsgräns
- Lägga till krediter till din Azure i Open-prenumeration

Du måste vara kontoadministratör för att utföra dessa uppgifter.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Navigera till prenumerationens betalningsmetoder

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.

1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar en sökning efter kostnadshantering och fakturering ](./media/account-admin-tasks/search-bar.png)

1. I listan **Mina prenumerationer** väljer du den prenumeration som du vill lägga till kreditkortet till.

   ![Skärmbild som visar Mina prenumerationer-rutnätet i översikt](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Om du inte ser några av dina prenumerationer här, kan det bero på att du har ändrat prenumerationskatalogen vid något tillfälle. För dessa prenumerationer måste du ändra katalog till den ursprungliga katalogen (katalogen där du registrerade dig). Upprepa sedan steg 2.

1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar bladet betalningsmetoder markerat.](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Här kan du lägga till ett nytt kreditkort, ändra den aktiva betalningsmetoden, redigera kreditkortsuppgifter och ta bort kreditkort.

### <a name="change-active-payment-method"></a>Ändra aktiv betalningsmetod

Du kan ändra den aktiva betalningsmetoden genom att lägga till ett nytt kreditkort eller välja ett som redan har sparats. Om du vill ändra den aktiva betalningsmetoden till ett nytt kreditkort:

1. Välj ”+” i det övre vänstra hörnet om du vill lägga till ett kreditkort.

    ![Skärmbild som visar plustecken](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Ange kreditkortsinformation i formuläret till höger.

    ![Skärmbild som visar formulär för att lägga till kreditkort.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Om du vill göra det här kortet till din aktiva betalningsmetod markerar du kryssrutan intill **Gör det här till min aktiva betalningsmetod** ovanför formuläret. Det här kortet blir det aktiva betalningsmedlet för alla prenumerationer som använder samma kort som den valda prenumerationen.

    ![Skärmbild som visar kryssruta för att göra kort till aktiv betalningsmetod.](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Välj **Nästa**.

Om du vill ändra den aktiva betalningsmetoden till ett kreditkort som redan har sparats:

1. Markera kryssrutan intill det kort som du vill ange som aktiv betalningsmetod.

    ![Skärmbild som visar ruta markerad bredvid kreditkort](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klicka på **Ange som aktiv** i kommandofältet.

    ![Skärmbild som visar knappen Ange som aktiv](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Ange kreditkortsinformation

Om du vill redigera kreditkortsinformation som utgångsdatum eller adress klickar du på kreditkortet som du vill redigera. Ett kreditkortsformulär visas till höger.

![Skärmbild som visar valt kreditkort](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Uppdatera kreditkortsinformationen och klickar på**Spara**.

### <a name="remove-a-credit-card-from-the-account"></a>Ta bort ett kreditkort från kontot

1. Markera kryssrutan bredvid det kort som du vill ta bort.

    ![Skärmbild som visar ruta markerad bredvid kreditkort](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klicka på **Ta bort** i kommandofältet.

    ![Skärmbild som visar knappen Ta bort](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Om ditt kreditkort är den aktiva betalningsmetoden för någon av dina Microsoft-prenumerationer kan du inte ta bort det från ditt Azure-konto. Ändra den aktiva betalningsmetoden för alla prenumerationer som är kopplade till det här kreditkortet och försök igen.

### <a name="switch-to-invoice-payment"></a>Växla till fakturabetalning

Om du är berättigad till att betala med faktura (check/banköverföring) kan du byta din prenumeration till fakturabetalning (check/banköverföring) i Azure-portalen.

1. Välj **Betala per faktura** i kommandofältet.

    ![Skärmbild som visar bladet betalningsmetoder markerat.](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Ange adressen för fakturabetalningsmetoden.
1. Klicka på **Next**.

Om du vill godkänd att betala med faktura kan du [läsa om hur du betalar med faktura](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Redigera fakturabetalningsadress

Om du vill redigera adressen för din fakturabetalningsmetod klickar du på **Faktura** i listan över betalningsmetoder för din prenumeration. Adressformuläret öppnas till höger.

## <a name="remove-spending-limit"></a>Ta bort utgiftsgräns

Utgiftsgränsen i Azure gör att du inte kan spendera mer än ditt kreditbelopp. Du kan ta bort utgiftsgränsen när som helst så länge det finns en giltig betalningsmetod som är associerad med din Azure-prenumeration. För prenumerationstyper som har kredit under flera månader, som Visual Studio Enterprise och Visual Studio Professional, kan du välja att återaktivera utgiftsgränsen i början av nästa faktureringsperiod.

Utgiftsgränsen är inte tillgänglig för prenumerationer med åtagandeplaner eller prissättning enligt Betala per användning. Mer information finns i den [fullständiga listan med typer av Azure-prenumerationer och tillgänglighet för utgiftsgränsen](https://azure.microsoft.com/support/legal/offer-details/).

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar en sökning efter kostnadshantering och fakturering ](./media/account-admin-tasks/search-bar.png)

1. I listan **Mina prenumerationer** väljer du din Visual Studio Enterprise-prenumeration.

   ![Skärmbild som visar Mina prenumerationer-rutnätet i översikt](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Om du inte ser några av dina Visual Studio-prenumerationer här, kan det bero på att du har ändrat prenumerationskatalogen vid något tillfälle. För dessa prenumerationer måste du ändra katalog till den ursprungliga katalogen (katalogen där du registrerade dig). Upprepa sedan steg 2.

1. I prenumerationsöversikten klickar du på den orangefärgade banderollen för att ta bort utgiftsgränsen.

    ![Skärmbild som visar banderollen Ta bort utgiftsgräns](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Välj om du vill ta bort utgiftsgränsen på obestämd tid eller bara för den aktuella faktureringsperioden.

   ![Skärmbild som visar bladet Ta bort utgiftsgräns](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Klicka på **Välj betalningsmetod** för att välja en betalningsmetod för prenumerationen. Det blir den aktiva betalningsmetoden för din prenumeration.

1. Klicka på **Finish**.

## <a name="add-credits-to-azure-in-open-subscription"></a>Lägg till krediter till din Azure i Open-prenumeration

Om du har en prenumeration på Azure i Open-licensiering kan du lägga till krediter i din prenumeration i Azure-portalen genom att lösa in en produktnyckel eller köpa krediter med ett kreditkort.

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar en sökning efter kostnadshantering och fakturering ](./media/account-admin-tasks/search-bar.png)

1. I listan **Mina prenumerationer** väljer du din prenumeration på Azure i Open-licensiering.

    ![Skärmbild som visar Mina prenumerationer-rutnätet i översikt](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Om du inte ser din prenumeration här kan det bero på att du har ändrat katalog vid något tillfälle. Du måste ändra prenumerationskatalog till den ursprungliga katalogen (katalogen där du registrerade dig). Upprepa sedan steg 2.

1. Välj **Kredithistorik**.

    ![Skärmbild som visar kredithistorik](./media/account-admin-tasks/aio-credit-history-blade.png)

1. Välj ”+” i det övre vänstra hörnet om du vill lägga till mer kredit.

    ![Skärmbild som visar knappen Lägg till kredit](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Välj en betalningsmetod i listrutan. Du kan lägga till en produktnyckel eller köpa krediter med ett kreditkort.

    ![Skärmbild som visar listrutan Betalningsmetod på bladet Lägg till kredit](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Om du väljer produktnyckel:
    - Ange produktnyckeln
    - Klicka på **Verifiera**

1. Om du väljer kreditkort:
    - Klicka på **Välj betalningsmetod** för att lägga till ett kreditkort eller välja ett befintligt.
    - Ange hur mycket kredit du vill lägga till.

1. Klicka på **Verkställ**

## <a name="troubleshooting"></a>Felsökning
Du kan inte använda virtuella eller förbetalda kort. Om du får felmeddelanden när du lägger till eller uppdaterar ett giltigt kreditkort kan du prova att öppna webbläsaren i privat läge.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om att [analysera och förhindra oväntade kostnader i Azure-portalen](getting-started.md)
