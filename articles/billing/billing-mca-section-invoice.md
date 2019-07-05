---
title: Skapa avsnitt på din faktura för att organisera dina kostnader – Azure
description: Lär dig att organisera dina kostnader med faktura avsnitt.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.topic: conceptual
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: eadaf34dc5bdd93af532362e8f8542de3f17f414
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490788"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Skapa avsnitt på din faktura för att organisera dina kostnader

Skapa avsnitt på din faktura för att organisera dina kostnader med en avdelning, utvecklingsmiljö, eller baserat på organisationens behov. Sedan ge andra användare behörighet att skapa Azure-prenumerationer som faktureras till avsnitt. Alla avgifter för användning och inköp för prenumerationerna som faktureras sedan till avsnittet. Du kan visa den totala kostnaden för avsnittet på din faktura, på Azure-portalen eller granska dem i Azure kostnadsanalys. Mer information finns i [Visa transaktioner med faktura avsnitt](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Skapa en faktura-avsnittet i Azure portal

Om du vill skapa en faktura-avsnittet, måste du vara en **fakturering profilägare** eller en **fakturering profildeltagare**. Mer information finns i [hantera faktura avsnitt för fakturering profil](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar Azure portal-sökning](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Välj **faktura avsnitt** från det vänstra fönstret. Beroende på din åtkomst, du kan behöva välja en profil för fakturering eller något faktureringskonto och välj sedan **faktura avsnitt**.

   ![Skärmbild som visar fakturan avsnittet lista](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. Välj **Lägg till** längst upp på sidan.

5. Ange ett namn för faktura-avsnittet och välj en profil för fakturering. I avsnittet visas på den här fakturering profilen faktura återger användningen av varje prenumeration och inköp som du har tilldelat till avsnittet. 

   ![Skärmbild som visar sidan för att skapa avsnittet faktura](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Välj **Skapa**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Skapa en ytterligare Azure-prenumeration för Microsoft kundavtal](billing-mca-create-subscription.md)
- [Ge andra användare behörighet att skapa Azure-prenumeration](billing-mca-create-subscription.md#give-others-permission)
- [Få faktureringsägarskapet för Azure-prenumerationer från användare i andra fakturering konton](billing-mca-request-billing-ownership.md)
