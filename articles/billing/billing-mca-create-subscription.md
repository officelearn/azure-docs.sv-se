---
title: Skapa en ytterligare Azure-prenumeration för ditt faktureringskonto
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
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 558410d980d261780f7287d1e27ed704b356fc2b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490918"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Skapa en ytterligare Azure-prenumeration för Microsoft kundavtal

Skapa ytterligare prenumerationer för ditt faktureringskonto att konfigurera separata miljöer för utveckling och testning, säkerhet eller för att isolera data av kompatibilitetsskäl.

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access). Om du vill skapa prenumerationer för andra typer av fakturering konton finns i [skapa ytterligare en prenumeration på Azure-portalen](billing-create-subscription.md).

Om du vill skapa en prenumeration, måste du vara en **faktura avsnittet ägare**, **faktura avsnittet deltagare**, eller **Azure-prenumeration skapare**. Mer information finns i [prenumeration fakturering roller och uppgifter](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). För att ge andra behörighet att skapa Azure-prenumerationer för kontot, se [ge andra användare behörighet att skapa Azure-prenumerationer](#give-others-permission).

## <a name="create-a-subscription"></a>Skapa en prenumeration

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **prenumerationer**.

   ![Skärmbild som visar search i portalen för prenumerationer](./media/billing-mca-create-subscription/billing-search-subscriptions.png)

3. Välj **Lägg till**

4. Om du har åtkomst till flera konton för fakturering, väljer du faktureringskonto för ditt Microsoft-kundavtal.

   ![Skärmbild som visar skapa prenumeration](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Välj en profil för fakturering. Avgifterna för din prenumeration kommer att debiteras för den valda profilen för fakturering. Om du har åtkomst till endast en fakturering profil nedtonade valet.

6. Välj en faktura-avsnitt. Avgifterna för din prenumeration kommer att debiteras för det här avsnittet av fakturering profilens faktura. Om du har åtkomst till endast en faktura avsnitt nedtonade valet.

7. Välj en plan för prenumerationen. Välj **Microsoft Azure-prenumeration för DevTest**, om du planerar att använda den här prenumerationen för utveckling eller annan testningsarbetsbelastningar med **Microsoft Azure-prenumeration**. Om du har åtkomst till endast en plan nedtonade valet.

8. Ange ett namn för prenumerationen. Namnet hjälper dig att enkelt identifiera prenumerationen på Azure-portalen.

9. Välj **Skapa**.

## <a name="give-others-permission"></a>Ge andra användare behörighet

Lägg till användare som skapare av Azure-prenumeration på en faktura avsnitt ge dem behörighet att skapa Azure-prenumerationer.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **Cost Management + fakturering**.

   ![Skärmbild som visar search i portalen för prenumerationer](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Gå till fakturaavsnittet. Beroende på din åtkomst kan du behöva välja en faktureringskonto eller en profil för fakturering. Fakturering konto eller profil, väljer du **faktura avsnitt** och sedan en faktura-avsnittet i listan. Prenumerationer som skapas av användarna kommer att debiteras för det här avsnittet för fakturan.
   
   ![Skärmbild som visar att välja faktura avsnitt](./media/billing-mca-create-subscription/mca-select-invoice-sections.png)        

4. Välj **Access Management (IAM)** från den vänstra sidan.

5. Välj **Lägg till** längst upp på sidan.

6. Välj **Azure-prenumeration skapare** för rollen.

7. Ange e-postadressen för användaren som du vill ge åtkomst.

8. Välj **Spara**.

## <a name="check-access"></a>Kontrollera åtkomst
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Ge andra användare behörighet att skapa Azure-resurser med inbyggda roller](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Skapa en windows-dator](../virtual-machines/windows/quick-create-portal.md)
- [Skapa en linux-dator](../virtual-machines/linux/quick-create-portal.md)
- [Skapa hanteringsgrupper för resursorganisationen och hantering](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
