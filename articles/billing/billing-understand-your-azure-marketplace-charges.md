---
title: Förstå din Azure externa tjänstavgifter | Microsoft Docs
description: Läs mer om fakturering av externa tjänster, tidigare känd som Marketplace, kostnader på Azure.
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44e1930ec4d82593580bfc3a7c3e19439436752e
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904343"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Förstå din Azure-fakturering för externa tjänstavgifter
Externa tjänster publiceras av tredje part programvaruleverantörer på Azure marketplace. SendGrid är till exempel en externa tjänster som du kan köpa i Azure, men inte har publicerats av Microsoft.

När du etablerar en ny extern tjänst eller en resurs, visas en varning:

![Marketplace-köp varning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Externa tjänster som publiceras av företag som inte är Microsoft, men ibland även Microsoft-produkter kategoriseras som externa tjänster.
> 
> 

## <a name="how-external-services-are-billed"></a>Hur debiteras externa tjänster
- Externa tjänster faktureras separat. De behandlas som enskilda order i Azure-prenumerationen. Faktureringsperioden för varje tjänst har angetts när du har köpt tjänsten. Ska inte förväxlas med faktureringsperiod för prenumerationen som du har köpt prenumerationen. Du får också separat fakturor och ditt kreditkort debiteras separat.
- Alla externa tjänster har en annan faktureringsmodell. Vissa tjänster faktureras på ett sätt som betalar per användning, medan andra använder en månatlig baserat betalning-modell. Du behöver ett kreditkort för Azure externa tjänster kan du köpa inte externa tjänster med betala med faktura.
- Du kan inte använda månatliga, kostnadsfria krediter för externa tjänster. Om du använder en Azure-prenumeration som omfattar [kostnadsfria krediter](https://azure.microsoft.com/pricing/spending-limits/), de kan inte användas för extern tjänst fakturor. Använda ett kreditkort för att köpa externa tjänster.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Visa extern tjänsteutgifter och historik i Azure portal
Du kan visa en lista över externa tjänster som finns på varje prenumeration inom de [Azure-portalen](https://portal.azure.com/): 

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som kontoadministratör.
2. I navmenyn väljer **prenumerationer**.
   
    ![Välj prenumerationer i navmenyn](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. I den **prenumerationer** bladet, väljer du den prenumeration som du vill visa och välj sedan **externa tjänster**.
   
    ![Välj en prenumeration på bladet fakturering](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Du bör se var och en av dina extern tjänst order, Utgivarnamn, tjänstnivå som du har köpt, namn du gav resursen och aktuell beställningsstatus. Välj en extern tjänst om du vill se tidigare fakturor.
   
    ![Välj en extern tjänst](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Härifrån kan visa du tidigare faktura belopp som inkluderar en skatt analys på detaljnivå.
   
    ![Visa externa tjänster faktureringshistorik](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Visa extern tjänst utgifter för kunder med Enterprise Agreement (EA)
EA-kunder kan se extern tjänsteutgifter och hämta rapporter i EA-portalen. Se [Azure Marketplace för EA-kunder](https://ea.azure.com/helpdocs/azureMarketplace) att komma igång.

## <a name="manage-payment-methods-for-external-service-orders"></a>Hantera betalningsmetoder för extern tjänst order
Uppdatera betalningsmetoder för extern tjänst order från den [Kontocenter](https://account.windowsazure.com/).

> [!NOTE]
> Om du har köpt din prenumeration med ett konto för arbetet eller skolan, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att göra ändringar i din betalningsmetod.
> 
> 

1. Logga in på den [Kontocenter](https://account.windowsazure.com/) och [navigerar du till den **marketplace** fliken](https://account.windowsazure.com/Store)
   
    ![Välj marketplace i kontocenter](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Välj den externa tjänsten som du vill hantera
   
    ![Välj den externa tjänsten som du vill hantera](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Klicka på **ändra betalningsmetod** till höger på sidan. Den här länken tar dig till en annan portal för att hantera din betalningsmetod.
   
    ![Ordersammanfattning](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Klicka på **redigera info** och följ instruktionerna för att uppdatera din betalningsinformation.
   
    ![Välj Redigera information](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Avbryt en extern tjänst-order
Om du vill avbryta beställningen extern tjänst tar du bort resurs i den [Azure-portalen](https://portal.azure.com).

![Ta bort resurs](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

