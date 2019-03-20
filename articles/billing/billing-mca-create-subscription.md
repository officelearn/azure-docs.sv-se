---
title: Skapa en ytterligare Azure-prenumeration för ditt faktureringskonto | Microsoft Docs
description: Lär dig hur du lägger till en ny Azure-prenumeration i Azure-portalen.
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 105b8481486c088a05e3acb95081d3ee55b55f52
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897120"
---
# <a name="create-an-additional-azure-subscription-for-microsoft-customer-agreement"></a>Skapa en ytterligare Azure-prenumeration för Microsoft kundavtal

Skapa ytterligare prenumerationer för ditt faktureringskonto att konfigurera separata miljöer för utveckling och testning, säkerhet eller för att isolera data av kompatibilitetsskäl.

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement). Om du vill skapa prenumerationer för andra fakturering konton finns i [skapa ytterligare en prenumeration på Azure-portalen](billing-create-subscription.md).

Om du vill skapa en prenumeration, måste du vara en **faktura avsnittet ägare**, **faktura avsnittet deltagare**, eller **Azure-prenumeration skapare**. Mer information finns i [prenumeration fakturering roller och uppgifter](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks). För att ge andra behörighet att skapa Azure-prenumerationer för kontot, se [ge andra användare behörighet att skapa Azure-prenumerationer](#give-others-permission-to-create-azure-subscriptions).

## <a name="create-a-subscription-in-the-azure-portal"></a>Skapa en prenumeration på Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök efter **prenumerationer**.

   ![Skärmbild som visar search i portalen för prenumerationer](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Välj **Lägg till**

4. Om du har åtkomst till flera konton för fakturering, väljer du faktureringskonto för ditt Microsoft-kundavtal.

   ![Skärmbild som visar skapa prenumeration](./media/billing-mca-create-subscription/billing-mca-create-azure-subscription.png)

5. Välj en profil för fakturering. Avgifterna för din prenumeration visas på fakturering profilens faktura och kommer att betalas till med hjälp av dess betalningsmetoder. Om du har åtkomst till endast en fakturering profil nedtonade valet.

6. Välj en faktura-avsnitt. Avgifterna för din prenumeration kommer att användas på det här avsnittet av fakturering profilens faktura. Om du har åtkomst till endast en faktura avsnitt nedtonade valet.

7. Välj en plan för prenumerationen. Välj **Microsoft Azure-prenumeration för DevTest**, om du planerar att använda den här prenumerationen för utveckling eller annan testningsarbetsbelastningar med **Microsoft Azure-prenumeration**. Om du har åtkomst till endast en plan nedtonade valet.

8. Ange ett namn för prenumerationen. Namnet hjälper dig att enkelt identifiera prenumerationen på Azure-portalen.

9. Välj **Skapa**.

## <a name="give-others-permission-to-create-azure-subscriptions"></a>Ge andra användare behörighet att skapa Azure-prenumerationer

Lägg till användare som skapare av Azure-prenumeration på en faktura avsnitt ge dem behörighet att skapa Azure-prenumerationer.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar search i portalen för prenumerationer](./media/billing-mca-create-subscription/billing-search-cost-management-billing.png)

3. Gå till avsnittet faktura. Beroende på din åtkomst kan du behöva välja en faktureringskonto eller en profil för fakturering. Fakturering konto eller profil, väljer du **faktura avsnitt** och sedan en faktura-avsnittet.

4. Välj **Access Management (IAM)** från den vänstra sidan.

5. Högst upp på sidan Välj **Lägg till**.

6. Välj **Azure-prenumeration skapare** för rollen.

   ![Skärmbild som visar tilldela roller för skapare av Azure-prenumeration till en användare](./media/billing-mca-create-subscription/billing-mca-add-azure-subscription-creator.png)

7. Ange e-postadressen för användaren som du vill ge åtkomst.

8. Välj **Spara**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Ge andra användare behörighet att skapa Azure-resurser med inbyggda roller](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)
- [Skapa en windows-dator](../virtual-machines/windows/quick-create-portal.md)
- [Skapa en linux-dator](../virtual-machines/linux/quick-create-portal.md)
- [Skapa hanteringsgrupper för resursorganisationen och hantering](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
