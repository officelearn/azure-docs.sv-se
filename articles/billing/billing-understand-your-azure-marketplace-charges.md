---
title: "Förstå Azure externa avgifterna | Microsoft Docs"
description: "Läs mer om fakturering för externa tjänster, tidigare känt som Marketplace, avgifter i Azure."
services: 
documentationcenter: 
author: adpick
manager: tonguyen
editor: 
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64bfd8581141001aa6c11ca17ec1af681054f490
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Förstå Azure faktureringen för externa serviceavgifter
Externa tjänster publiceras av tredje part programleverantörer i Azure marketplace. Till exempel är ClearDB och SendGrid externa tjänster som du kan köpa i Azure, men inte har publicerats av Microsoft.

När du etablerar en ny extern tjänst eller en resurs, visas en varning:

![Marketplace köpa varning](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Externa tjänster publiceras av företag som inte är Microsoft, men ibland också Microsoft-produkter kategoriseras som externa tjänster.
> 
> 

## <a name="how-external-services-are-billed"></a>Hur faktureras externa tjänster
- Externa tjänster faktureras separat. De behandlas som enskilda order i din Azure-prenumeration. Faktureringsperioden för varje tjänst anges när du köper tjänsten. Ska inte förväxlas med faktureringsperiod för prenumerationen som du har handlat. Du får också separata växlar och ditt kreditkort debiteras separat.
- Varje extern tjänst har en annan fakturering modell. Vissa tjänster debiteras i en betalning per användning sätt medan andra använder en månatlig baserat betalning modell. Du behöver ett kreditkort för externa Azure-tjänster, du kan inte köpa externa tjänster med faktura lön.
- Du kan inte använda ledigt månadskrediter för externa tjänster. Om du använder en Azure-prenumeration som innehåller [ledigt krediter](https://azure.microsoft.com/pricing/spending-limits/), de kan inte tillämpas på externa-tjänsten växlar. Använda ett kreditkort till externa tjänster.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Visa extern tjänsteutgifter och historik i Azure-portalen
Du kan visa en lista över de externa tjänster som finns på varje prenumeration inom den [Azure-portalen](https://portal.azure.com/): 

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som kontoadministratör.
2. På navmenyn väljer **prenumerationer**.
   
    ![Välj prenumerationer på navmenyn](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. I den **prenumerationer** bladet, Välj den prenumeration som du vill visa och välj sedan **externa tjänster**.
   
    ![Välj en prenumeration i bladet fakturering](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Du bör se alla dina externa tjänsten order, Utgivarnamn, tjänstnivå som du har köpt, namn du gav för resursen och aktuella statusen för. Välj en extern tjänst om du vill se tidigare växlar.
   
    ![Välj en extern tjänst](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Härifrån kan visa du tidigare faktura belopp inklusive moms fördelningen.
   
    ![Visa faktureringshistorik externa tjänster](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Visa extern tjänstens utgifter för kunder med Enterprise-avtal (EA)
EA kunder kan se externa tjänsteutgifter och hämta rapporter i EA-portalen. Se [Azure Marketplace för EA kunder](https://ea.azure.com/helpdocs/azureMarketplace) att komma igång.

## <a name="manage-payment-methods-for-external-service-orders"></a>Hantera betalningssätt för extern tjänst order
Uppdatera betalningsmetoden för extern tjänst order från den [Kontocenter](https://account.windowsazure.com/).

> [!NOTE]
> Om du har köpt din prenumeration med ett arbets- eller Skol-konto [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att göra ändringar i din betalningsmetod.
> 
> 

1. Logga in på den [Kontocenter](https://account.windowsazure.com/) och [navigera till den **marketplace** fliken](https://account.windowsazure.com/Store)
   
    ![Välj marketplace mitt konto](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Välj extern tjänsten som du vill hantera
   
    ![Välj extern tjänsten som du vill hantera](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Klicka på **Byt betalningsmetod** till höger på sidan. Den här länken kan du till en annan portal och hantera din betalningsmetod.
   
    ![Ordning sammanfattning](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Klicka på **redigera info** och följ instruktionerna för att uppdatera din betalningsinformation.
   
    ![Välj Redigera info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Avbryta en extern tjänst ordning
Om du vill avbryta din order extern tjänst kan du ta bort resursen i den [Azure-portalen](https://portal.azure.com).

![Ta bort resurs](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande har frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.

