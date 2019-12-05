---
title: Skapa ytterligare en Azure-prenumeration | Microsoft Docs
description: Lär dig hur du lägger till en ny Azure-prenumeration i Azure-portalen.
services: billing
documentationcenter: ''
author: amberb
manager: amberb
editor: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: banders
ms.openlocfilehash: bd561f616c9e120a38f1c6b125e349684f82942e
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665618"
---
# <a name="create-an-additional-azure-subscription"></a>Skapa ytterligare en Azure-prenumeration

Du kan skapa ytterligare en prenumeration för faktureringskontot i ditt [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft-kundavtal](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) eller [Microsoft-partneravtal](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) i Azure-portalen. Du kanske vill ha ytterligare en prenumeration för att undvika att nå upp till prenumerationsgränser, om du vill skapa separata miljöer för säkerheten eller om du vill isolera data av efterlevnadsskäl.

Om du har ett MOSP-faktureringskonto (Microsoft Online Service Program) kan du skapa ytterligare prenumerationer i [Azure-portalen för registrering](https://account.azure.com/signup?offer=ms-azr-0003p).

Du kan läsa mer om faktureringskonton och se vilken typ av faktureringskonto du har i [Visa faktureringskonton i Azure-portalen](billing-view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Behörigheter som krävs för att skapa Azure-prenumerationer

Du behöver följande behörigheter för att skapa prenumerationer:

|Faktureringskonto  |Behörighet  |
|---------|---------|
|Enterprise-avtal (EA) |  Rollen som kontoägare för Enterprise-avtalsregistreringen. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](billing-understand-ea-roles.md).    |
|Microsoft-kundavtal (MCA) |  Rollen som ägare eller deltagare för fakturaavsnittet, faktureringsprofilen eller faktureringskontot. Eller rollen som Azure-prenumerationsförfattare för fakturaavsnittet.  Mer information finns i [Roller och uppgifter för prenumerationsfakturering](billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Microsoft-partneravtal (MPA) |   Rollen som global administratör eller administratörsrepresentant för CSP-partnerorganisationen. Läs mer i [Partner Center – Tilldela användarroller och behörigheter](https://docs.microsoft.com/partner-center/permissions-overview).  Användaren måste logga in på partnerklientorganisationen för att skapa Azure-prenumerationer.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Skapa en prenumeration i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Prenumerationer**.

   ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/billing-create-subscription/billing-search-subscription-portal.png)

1. Välj **Lägg till**.

   ![Skärm bild som visar knappen Lägg till i prenumerationsvyn](./media/billing-create-subscription/subscription-add.png)

1. Om du har åtkomst till flera faktureringskonton väljer du faktureringskontot som du vill skapa prenumerationen för.

1. Fyll i formuläret och klicka på **Skapa**. I tabellerna nedan ser du formulärfälten för de olika typerna av faktureringskonton.

**Enterprise-avtal**

|Fält  |Definition  |
|---------|---------|
|Namn     | Visningsnamnet som gör att du enkelt kan identifiera prenumerationen i Azure-portalen.  |
|Erbjudande     | Välj EA Dev/Test om du planerar att använda prenumerationen för utveckling eller testning. Annars väljer du Microsoft Azure Enterprise. DevTest-erbjudandet måste vara aktiverat för ditt registreringskonto om du ska kunna skapa EA Dev/Test-prenumerationer.|

**Microsoft-kundavtal**

|Fält  |Definition  |
|---------|---------|
|Faktureringsprofil     | Avgifterna för din prenumeration faktureras till den valda faktureringsprofilen. Om du bara har åtkomst till en faktureringsprofil blir valet nedtonat.     |
|Fakturaavsnitt     | Avgifterna för din prenumeration visas i det här avsnittet på faktureringsprofilens faktura. Om du bara har åtkomst till ett enda fakturaavsnitt blir valet nedtonat.  |
|Planera     | Välj Microsoft Azure-plan för DevTest om du planerar att använda den här prenumerationen för utvecklings- eller testningsarbetsbelastningar. I annat fall väljer du Microsoft Azure-plan. Om det bara är ett prenumerationsavtal aktiverat för faktureringsprofilen är det här valet nedtonat.  |
|Namn     | Visningsnamnet som gör att du enkelt kan identifiera prenumerationen i Azure-portalen.  |

**Microsoft-partneravtal**

|Fält  |Definition  |
|---------|---------|
|Kund    | Prenumerationen skapas för den kund du väljer. Om du bara har en kund är det här valet nedtonat.  |
|Återförsäljare    | Den återförsäljare som ska tillhandahålla tjänster till kunden. Det här är ett valfritt fält som endast gäller för indirekta leverantörer i CSP-modellen med två nivåer. |
|Namn     | Visningsnamnet som gör att du enkelt kan identifiera prenumerationen i Azure-portalen.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Skapa ytterligare en Azure-prenumeration programmatiskt

Du kan också skapa ytterligare prenumerationer programmatiskt. Du kan läsa mer i [Skapa Azure-prenumerationer programmatiskt](../azure-resource-manager/programmatically-create-subscription.md).

## <a name="next-steps"></a>Nästa steg

- [Lägg till eller ändra Azure-prenumerationsadministratörer](billing-add-change-azure-subscription-administrator.md)
- [Flytta resurser till ny resursgrupp eller prenumeration](../azure-resource-manager/resource-group-move-resources.md?toc=/azure/billing/TOC.json)
- [Skapa hanteringsgrupper för organisering och hantering av resurser](../governance/management-groups/create.md?toc=/azure/billing/TOC.json)
- [Avbryta prenumerationen på Azure](billing-how-to-cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
