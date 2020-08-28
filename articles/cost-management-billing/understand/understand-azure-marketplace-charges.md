---
title: Förstå dina kostnader för externa Azure-tjänster
description: Lär dig mer om faktureringen av kostnaderna för externa tjänster, tidigare Marketplace, i Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8da85ec5781ff9575cf380092fea9e41d6af8786
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686130"
---
# <a name="understand-your-azure-external-services-charges"></a>Förstå dina kostnader för externa Azure-tjänster
Externa tjänster publiceras av tredjepartsleverantörer av programvara på Azure Marketplace. Till exempel är SendGrid en extern tjänst som du kan köpa i Azure, men som inte publiceras av Microsoft. Vissa Microsoft-produkter säljs dock via Azure Marketplace.

## <a name="how-external-services-are-billed"></a>Så debiteras externa tjänster

- Om du har ett Microsoft-kundavtal (MCA) eller ett Microsoft-partneravtal debiteras dina tjänster från tredje part med resten av dina Azure-tjänster. [Kontrollera din typ av faktureringskonto](#check-billing-account-type) för att se om du har åtkomst till ett MCA eller MPA.
- Om du inte har ett MCA eller MPA debiteras dina externa tjänster separat från dina Azure-tjänster. Du får två fakturor varje faktureringsperiod: en faktura för Azure-tjänster och en annan för Marketplace-köp.
- Varje extern tjänst har en specifik faktureringsmodell. Vissa tjänster debiteras med användningsbaserad betalning, medan andra har fasta månadsavgifter.
- Du kan inte använda månatliga kostnadsfria krediter för externa tjänster. Om du använder en Azure-prenumeration som ger rätt till [kostnadsfria krediter](https://azure.microsoft.com/pricing/spending-limits/) kan de inte användas för debiteringar från externa tjänster. När du etablerar en ny extern tjänst eller resurs visas en varning:

    ![Varning om inköp på Marketplace](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="external-spending-for-ea-customers"></a>Externa utgifter för EA-kunder

EA-kunder kan visa kostnader för externa tjänster och ladda ned rapporter på EA-portalen. Information om hur du kommer igång finns i [Azure Marketplace för EA-kunder](https://ea.azure.com/helpdocs/azureMarketplace).

## <a name="view-and-download-invoices-for-external-services"></a>Visa och ladda ned fakturor för externa tjänster

Om du har ett Microsoft-kundavtal (MCA) eller ett Microsoft-partneravtal debiteras dina tjänster från tredje part med resten av dina Azure-tjänster på en enda faktura. [Kontrollera din typ av faktureringskonto](#check-billing-account-type) för att se om du har åtkomst till ett MCA eller MPA. Om du har det kan du [visa och ladda ned fakturor från Azure-portalen](download-azure-invoice.md) för att se dina kostnader från tredjepartsleverantörer.

Om du inte har något MCA eller MPA får du separata fakturor för avgifter från tredjepartsleverantörer. 

Azure Marketplace-kostnader visas i din lokala valuta.

Du kan visa och ladda ned dina Azure Marketplace-fakturor från Azure-portalen genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering och fakturering**.
1. På den vänstra menyn väljer du **Fakturor**.
1. I listrutan för prenumerationer väljer du prenumerationen som är kopplad till dina Marketplace-tjänster.
1. Titta i kolumnen **Typ** i listan med fakturor. Om en faktura avser en Marketplace-tjänst är typen **Azure Marketplace och reservationer**. 

    ![skärmbild av Azure Marketplace-typ i fakturatabell](./media/understand-azure-marketplace-charges/marketplace-type-twd.png)

1. Om du vill filtrera efter typ och bara visa fakturor för Azure Marketplace och reservationer väljer du filtret **Typ**. Välj sedan **Azure Marketplace och reservationer** i listrutan.

    ![skärmbild där filtret Typ är valt och där Azure Marketplace och reservationer är valt i listrutan](./media/understand-azure-marketplace-charges/type-filter.png)

1. Välj nedladdningsikonen till höger för den faktura som du vill ladda ned.

    ![skärmbild som visar nedladdningsikonen som har valts för en faktura](./media/understand-azure-marketplace-charges/download-icon-marketplace.png)

1. Välj den blå **Ladda ned**-knappen under **Faktura**.

    ![skärmbild som visar knappen Ladda ned för en faktura i kontextfönstret](./media/understand-azure-marketplace-charges/invoice-download-marketplace.png)

## <a name="pay-for-external-services-in-the-azure-portal"></a>Betala för externa tjänster på Azure-portalen

Om du har ett Microsoft-kundavtal (MCA) eller ett Microsoft-partneravtal debiteras dina tjänster från tredje part med resten av dina Azure-tjänster. [Kontrollera din typ av faktureringskonto](#check-billing-account-type) för att se om du har åtkomst till ett MCA eller MPA. Om du gör det kan du betala för hela fakturan på Azure-portalen genom att följa stegen i [Betala din faktura för Microsoft Azure](pay-bill.md).

Om du inte har ett MCA eller MPA kan du betala dina Marketplace-fakturor på Azure-portalen genom att följa dessa steg:

1. Visa dina Marketplace-fakturor genom att följa stegen i föregående avsnitt, [Visa och ladda ned fakturor för externa tjänster](#view-and-download-invoices-for-external-services).
1. Välj den blå **Betala nu**-länken för den faktura du vill betala.

    ![skärmbild där länken Betala nu är vald i fakturatabellen](./media/understand-azure-marketplace-charges/pay-now-twd.png)

    >[!NOTE]
    > Länken **Betala nu** visas bara om fakturatypen är **Azure Marketplace och reservationer** och om fakturan är klar för betalning eller har ett passerat betalningsdatum.

1. Klicka på den blå länken för **Välj betalningsmetod** på den nya sidan.

    ![skärmbild där länken för att välja betalningsmetod är vald](./media/understand-azure-marketplace-charges/select-payment-method-pay-now-twd.png)

1. När du har valt en betalningsmetod klickar du på den blå **Betala nu**-knappen längst ned till vänster på sidan.
    ![skärmbild där knappen Betala nu är vald](./media/understand-azure-marketplace-charges/pay-now-button-twd.png)

## <a name="change-default-payment-for-external-services"></a>Ändra standardbetalning för externa tjänster

När du köper en extern tjänst väljer du en Azure-prenumeration för resursen. Betalningsmetoden för den valda Azure-prenumerationen blir betalningsmetoden för den externa tjänsten. Om du vill ändra betalningsmetoden för en extern tjänst måste du [ändra betalningsmetoden för Azure-prenumerationen](../manage/change-credit-card.md) som är kopplad till den externa tjänsten. Du kan ta reda på vilken prenumeration som din order för externa tjänster är kopplad till genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Alla resurser** på den vänstra navigeringsmenyn.
     ![skärmbild av menyalternativet Alla resurser](./media/understand-azure-marketplace-charges/all-resources.png)
1. Sök efter din externa tjänst.
1. Leta efter namnet på prenumerationen i kolumnen **Prenumeration**.
    ![skärmbild av prenumerationsnamnet för resursen](./media/understand-azure-marketplace-charges/sub-selected.png)
1. Klicka på prenumerationens namn och [uppdatera den aktiva betalningsmetoden](../manage/change-credit-card.md).

## <a name="cancel-an-external-service-order"></a>Avbryta en beställning av en extern tjänst

Om du vill avbryta en beställning av en externa tjänst tar du bort resursen på [Azure-portalen](https://portal.azure.com).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Alla resurser** på den vänstra navigeringsmenyn.
    ![Skärmbild av menyalternativet Alla resurser](./media/understand-azure-marketplace-charges/all-resources.png)
1. Sök efter din externa tjänst.
1. Markera kryssrutan bredvid den resurs som du vill ta bort.
1. Välj **Ta bort** i kommandofältet.
    ![Skärmbild av knappen Ta bort](./media/understand-azure-marketplace-charges/delete-button.png)
1. Skriv *Ja* på bekräftelsesidan.
    ![Ta bort resurs](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. Klicka på **Ta bort**.

## <a name="check-billing-account-type"></a>Kontrollera typen av faktureringskonto
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Börja analysera kostnader](../costs/quick-acm-cost-analysis.md)