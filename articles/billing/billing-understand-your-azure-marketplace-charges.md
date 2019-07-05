---
title: Förstå din Azure externa tjänstavgifter | Microsoft Docs
description: Läs mer om fakturering av externa tjänster, tidigare känd som Marketplace, kostnader på Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490357"
---
# <a name="understand-your-azure-external-services-charges"></a>Förstå Azure externa tjänster-avgifter
Externa tjänster publiceras av programvara från tredje part-leverantörer på Azure marketplace. SendGrid är till exempel en extern tjänst som du kan köpa i Azure, men inte har publicerats av Microsoft. Vissa Microsoftprodukter som säljs via Azure marketplace för.

## <a name="how-external-services-are-billed"></a>Hur debiteras externa tjänster

- Om du har en [Microsoft kundavtal](#check-access), dina tjänster från tredje part faktureras med resten av dina Azure-tjänster.
- Om du inte har ett Microsoft-kundavtal, dina externa tjänster faktureras separat från dina Azure-tjänster.
- Alla externa tjänster har en annan faktureringsmodell. Medan andra har åtgärdat månatliga avgifter debiteras vissa tjänster på ett sätt som betalar per användning.
- Du kan inte använda månatliga, kostnadsfria krediter för externa tjänster. Om du använder en Azure-prenumeration som omfattar [kostnadsfria krediter](https://azure.microsoft.com/pricing/spending-limits/), de kan inte användas för kostnader från externa tjänster. När du etablerar en ny extern tjänst eller en resurs, visas en varning:

    ![Marketplace-köp varning](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>Visa och ladda ner fakturor

Om du har en [Microsoft kundavtal](#check-access), dina debiteringar från tredje part är på samma faktura som dina Azure-avgifter. Lär dig hur du [visa och hämta fakturan Azure](billing-download-azure-invoice.md) från Azure portal för att se en tredje parts-avgifter.

Om du inte har ett Microsoft-kundavtal kan ha separata fakturor för debitering från tredje part. Du kan visa och hämta dina Azure Marketplace-fakturor från Azure portal genom att följa dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter **Cost Management + fakturering**.
1. I den vänstra menyn väljer du **fakturor**.
1. Klicka på den **Azure Marketplace och -reservationer** fliken.  ![Bild av fliken för Azure marketplace och -reservationer](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. Välj den prenumeration som innehåller de externa tjänster som du vill visa fakturor för i prenumerationen listrutan.

## <a name="external-spending-for-ea-customers"></a>Externa utgifter för EA-kunder

EA-kunder kan se extern tjänsteutgifter och hämta rapporter i EA-portalen. Se [Azure Marketplace för EA-kunder](https://ea.azure.com/helpdocs/azureMarketplace) att komma igång.

## <a name="manage-payment-for-external-services"></a>Hantera betalning för externa tjänster

När du köper en extern tjänst måste välja du en Azure-prenumeration för resursen. Betalningsmetod för den valda Azure-prenumerationen blir betalningssätt för den externa tjänsten. Om du vill ändra betalningsmetod för en extern tjänst måste du [ändra betalningsmetod för Azure-prenumerationen](billing-how-to-change-credit-card.md) knutna till den externa tjänsten. Du kan ta reda på vilken prenumeration som beställningen extern tjänst som är kopplad till genom att följa dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Klicka på **alla resurser** i den vänstra navigeringsmenyn.
     ![Skärmbild av menyobjektet för alla resurser](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Sök efter din extern tjänst.
1. Leta efter namnet på prenumerationen i den **prenumeration** kolumn.
    ![Skärmbild av prenumerationens namn för resursen](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Klicka på prenumerationens namn och [uppdatera aktiva betalningsmetoden](billing-how-to-change-credit-card.md).

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>Avbryt en extern tjänst-order
Om du vill avbryta beställningen extern tjänst tar du bort resurs i den [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Klicka på **alla resurser** i den vänstra navigeringsmenyn.
    ![Skärmbild av menyobjektet för alla resurser](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Sök efter din extern tjänst.
1. Markera kryssrutan bredvid den resurs du vill ta bort.
1. Välj **ta bort** i kommandofältet.
    ![Skärmbild av knappen Ta bort](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Typ *”Ja”* bekräftelsebladet.
    ![Ta bort resurs](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Klicka på **Ta bort**.

## <a name="check-access"></a>Kontrollera åtkomst
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](../cost-management/quick-acm-cost-analysis.md)
