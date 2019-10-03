---
title: Förstå dina avgifter för externa Azure-tjänster | Microsoft Docs
description: Lär dig mer om faktureringen av kostnaderna för externa tjänster, tidigare Marketplace, i Azure.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43986ce57b8d320beeae748d3b848cf9ef38744d
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718728"
---
# <a name="understand-your-azure-external-services-charges"></a>Förstå dina kostnader för externa Azure-tjänster
Externa tjänster publiceras av tredjepartsleverantörer av programvara på Azure Marketplace. Till exempel är SendGrid en extern tjänst som du kan köpa i Azure, men som inte publiceras av Microsoft. Vissa Microsoft-produkter säljs dock via Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Så debiteras externa tjänster

- Om du har ett [kundavtal med Microsoft](#check-access) debiteras dina tjänster från tredje part med resten av dina Azure-tjänster.
- Om du inte har ett Microsoft-kundavtal debiteras dina externa tjänster separat från dina Azure-tjänster.
- Varje extern tjänst har en specifik faktureringsmodell. Vissa tjänster debiteras med användningsbaserad betalning, medan andra har fasta månadsavgifter.
- Du kan inte använda månatliga kostnadsfria krediter för externa tjänster. Om du använder en Azure-prenumeration som ger rätt till [kostnadsfria krediter](https://azure.microsoft.com/pricing/spending-limits/) kan de inte användas för debiteringar från externa tjänster. När du etablerar en ny extern tjänst eller resurs visas en varning:

    ![Varning om inköp på Marketplace](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

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

## <a name="view-and-download-invoices"></a>Visa och ladda ned fakturor

Om du har ett [kundavtal med Microsoft](#check-access) finns dina avgifter från tredjepartsleverantörer på samma faktura som dina Azure-kostnader. Lär dig hur du [visar och laddar ned din Azure-faktura](billing-download-azure-invoice.md) från Azure-portalen för att se dina kostnader från tredjepartsleverantörer.

Om du inte har något Microsoft-kundavtal får du separata fakturor för avgifter från tredjepartsleverantörer. Du kan visa och ladda ned dina Azure Marketplace-fakturor från Azure-portalen genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering + fakturering**.
1. På den vänstra menyn väljer du **Fakturor**.
1. Klicka på fliken **Azure Marketplace och reservationer**.  ![Bild av fliken Azure Marketplace och reservationer](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. I listrutan för prenumerationer väljer du den prenumeration som innehåller de externa tjänster som du vill visa fakturor för.

## <a name="external-spending-for-ea-customers"></a>Externa utgifter för EA-kunder

EA-kunder kan visa kostnader för externa tjänster och ladda ned rapporter på EA-portalen. Information om hur du kommer igång finns i [Azure Marketplace för EA-kunder](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="manage-payment-for-external-services"></a>Hantera betalningar för externa tjänster

När du köper en extern tjänst väljer du en Azure-prenumeration för resursen. Betalningsmetoden för den valda Azure-prenumerationen blir betalningsmetoden för den externa tjänsten. Om du vill ändra betalningsmetoden för en extern tjänst måste du [ändra betalningsmetoden för Azure-prenumerationen](billing-how-to-change-credit-card.md) som är kopplad till den externa tjänsten. Du kan ta reda på vilken prenumeration som din order för externa tjänster är kopplad till genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Alla resurser** på den vänstra navigeringsmenyn.
     ![skärmbild av menyalternativet Alla resurser](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Sök efter din externa tjänst.
1. Leta efter namnet på prenumerationen i kolumnen **Prenumeration**.
    ![skärmbild av prenumerationsnamnet för resursen](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. Klicka på prenumerationens namn och [uppdatera den aktiva betalningsmetoden](billing-how-to-change-credit-card.md).

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

## <a name="cancel-an-external-service-order"></a>Avbryta en beställning av en extern tjänst
Om du vill avbryta en beställning av en externa tjänst tar du bort resursen på [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Alla resurser** på den vänstra navigeringsmenyn.
    ![Skärmbild av menyalternativet Alla resurser](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. Sök efter din externa tjänst.
1. Markera kryssrutan bredvid den resurs som du vill ta bort.
1. Välj **Ta bort** i kommandofältet.
    ![Skärmbild av knappen Ta bort](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. Skriv *Ja* på bekräftelsesidan.
    ![Ta bort resurs](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. Klicka på **Ta bort**.

## <a name="check-access"></a>Kontrollera åtkomst
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](../cost-management/quick-acm-cost-analysis.md)
