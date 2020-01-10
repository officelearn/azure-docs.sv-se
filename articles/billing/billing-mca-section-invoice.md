---
title: Skapa fakturaavsnitt för att ordna kostnader – Azure
description: Lär dig att organisera dina kostnader med fakturaavsnitt.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ff8b2da353d623cd9f05c8d0b0317587d7093ce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75389289"
---
# <a name="create-sections-on-your-invoice-to-organize-your-costs"></a>Skapa avsnitt på din faktura för att organisera dina kostnader

Skapa avsnitt på din faktura för att organisera dina kostnader efter avdelning, utvecklingsmiljö eller organisationens behov. Ge sedan andra behörighet att skapa Azure-prenumerationer som faktureras till avsnittet. Eventuella användningskostnader och inköp för prenumerationerna faktureras sedan till avsnittet. Du kan visa de totala avgifterna för avsnittet på din faktura i Azure-portalen eller granska dem i Azure-kostnadsanalysen. Mer information finns i [visa transaktioner efter fakturaavsnitt](billing-mca-understand-your-bill.md#view-transactions-by-invoice-sections).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="create-an-invoice-section-in-the-azure-portal"></a>Skapa ett fakturaavsnitt i Azure-portalen

För att kunna skapa ett fakturaavsnitt behöver du vara **faktureringsprofilsägare** eller **faktureringsprofilsdeltagare**. Mer information finns i [hantera fakturaavsnitt för faktureringsprofil](billing-understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning i Azure-portalen](./media/billing-mca-section-invoice/billing-search-cost-management-billing.png)

3. Välj **Fakturaavsnitt** i den vänstra rutan. Beroende på din åtkomst kan du behöva välja en faktureringsprofil eller ett faktureringskonto och sedan välja **Fakturaavsnitt**.

   ![Skärmbild som visar lista över fakturaavsnitt](./media/billing-mca-section-invoice/mca-select-invoice-sections.png)

4. Välj **Lägg till** längst upp på sidan.

5. Ange ett namn för fakturaavsnittet och välj en faktureringsprofil. Avsnittet visas på den här faktureringsprofilens faktura och anger användningen för varje prenumeration och inköp som du har tilldelat till avsnittet.

   ![Skärmbild som visar sidan för skapande av fakturaavsnitt](./media/billing-mca-section-invoice/mca-create-invoice-section.png)

6. Välj **Skapa**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Skapa ytterligare en Azure-prenumeration för Microsoft-kundavtal](billing-mca-create-subscription.md)
- [Hantera faktureringsroller i Azure-portalen](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Få faktureringsägarskap för Azure-prenumerationer från användare i andra faktureringskonton](billing-mca-request-billing-ownership.md)
