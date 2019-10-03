---
title: Skapa ytterligare en Azure-prenumeration för ditt faktureringskonto
description: Lär dig hur du lägger till en ny Azure-prenumeration i Azure-portalen.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e1fafafe5f67d1775ca80a1f7c2aff4dec9e0bc4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709576"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Skapa ytterligare en Azure-prenumeration för Microsoft-kundavtal

Skapa ytterligare prenumerationer för ditt faktureringskonto för att konfigurera separata miljöer för utveckling och testning, säkerhet eller isolering av data av säkerhetsskäl.

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access). Om du vill skapa prenumerationer för andra typer av faktureringskonton kan du läsa [Skapa ytterligare en prenumeration i Azure-portalen](billing-create-subscription.md).

Om du vill skapa en prenumeration måste du vara **fakturaavsnittsägare**, **fakturaavsnittsdeltagare** eller **Azure-prenumerationsskapare**. Mer information finns i [Roller och uppgifter för prenumerationsfakturering](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). Om du vill ge andra behörighet att skapa Azure-prenumerationer för ditt faktureringskonto kan du läsa [Ge andra behörighet att skapa Azure-prenumerationer](#give-others-permission).

## <a name="create-a-subscription"></a>Skapa en prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Prenumerationer**.

   ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Välj **Lägg till**

4. Om du har åtkomst till flera faktureringskonton väljer du faktureringskontot för ditt Microsoft-kundavtal.

   ![Skärmbild som visar sidan Skapa prenumeration](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Välj en faktureringsprofil. Avgifterna för din prenumeration faktureras till den valda faktureringsprofilen. Om du bara har åtkomst till en faktureringsprofil blir valet nedtonat.

6. Välj ett fakturaavsnitt. Avgifterna för din prenumeration faktureras till det här avsnittet på faktureringsprofilens faktura. Om du bara har åtkomst till ett enda fakturaavsnitt blir valet nedtonat.

7. Välj en plan för prenumerationen. Välj **Microsoft Azure-plan för DevTest** om du planerar att använda den här prenumerationen för utvecklings- eller testningsarbetsbelastningar. I annat fall använder du **Microsoft Azure-plan**. Om du bara har åtkomst till en enda plan blir valet nedtonat.

8. Ange ett namn för prenumerationen. Namnet hjälper dig att enkelt identifiera prenumerationen i Azure-portalen.

9. Välj **Skapa**.

## <a name="give-others-permission"></a>Ge andra behörighet

Lägg till användare som Azure-prenumerationsskapare i ett fakturaavsnitt för att ge dem behörighet att skapa Azure-prenumerationer.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

   ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Gå till fakturaavsnittet. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil. Från faktureringskontot eller profilen väljer du **Fakturaavsnitt** och sedan ett fakturaavsnitt i listan. Alla prenumerationer som skapas av användarna faktureras till det här fakturaavsnittet.
   
   ![Skärmbild som visar val av fakturaavsnitt](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Välj **Åtkomsthantering (IAM)** längst upp till vänster.

5. Välj **Lägg till** längst upp på sidan.

6. Välj **Azure-prenumerationsskapare** för rollen.

7. Ange e-postadressen för den användare som du vill ge åtkomst till.

8. Välj **Spara**.

## <a name="check-access"></a>Kontrollera åtkomst
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Ge andra behörighet att skapa Azure-resurser med hjälp av inbyggda roller](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Skapa en virtuell Windows-dator](../virtual-machines/windows/quick-create-portal.md)
- [Skapa en virtuell Linux-dator](../virtual-machines/linux/quick-create-portal.md)
- [Skapa hanteringsgrupper för organisering och hantering av resurser](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
