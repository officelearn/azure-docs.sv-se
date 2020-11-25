---
title: Skapa ytterligare en Azure-prenumeration
description: Lär dig hur du lägger till en ny Azure-prenumeration i Azure-portalen. Se information om faktureringskontoformulär och visa fler tillgängliga resurser.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 27594ff4d7673038e9de38c5969a615be60aeadf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844701"
---
# <a name="create-an-additional-azure-subscription"></a>Skapa ytterligare en Azure-prenumeration

Du kan skapa ytterligare en prenumeration för faktureringskontot i ditt [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft-kundavtal](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) eller [Microsoft-partneravtal](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) i Azure-portalen. Du kanske vill ha ytterligare en prenumeration för att undvika att nå upp till prenumerationsgränser, om du vill skapa separata miljöer för säkerheten eller om du vill isolera data av efterlevnadsskäl.

Om du har ett MOSP-faktureringskonto (Microsoft Online Service Program) kan du skapa ytterligare prenumerationer i [Azure-portalen för registrering](https://account.azure.com/signup?offer=ms-azr-0003p).

Du kan läsa mer om faktureringskonton och se vilken typ av faktureringskonto du har i [Visa faktureringskonton i Azure-portalen](view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Behörigheter som krävs för att skapa Azure-prenumerationer

Du behöver följande behörigheter för att skapa prenumerationer:

|Faktureringskonto  |Behörighet  |
|---------|---------|
|Enterprise-avtal (EA) |  Rollen som kontoägare för Enterprise-avtalsregistreringen. Mer information finns i [Förstå administrativa roller för Azure Enterprise-avtal i Azure](understand-ea-roles.md).    |
|Microsoft-kundavtal (MCA) |  Rollen som ägare eller deltagare för fakturaavsnittet, faktureringsprofilen eller faktureringskontot. Eller rollen som Azure-prenumerationsförfattare för fakturaavsnittet.  Mer information finns i [Roller och uppgifter för prenumerationsfakturering](understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Microsoft-partneravtal (MPA) |   Rollen som global administratör eller administratörsrepresentant för CSP-partnerorganisationen. Läs mer i [Partner Center – Tilldela användarroller och behörigheter](/partner-center/permissions-overview).  Användaren måste logga in på partnerklientorganisationen för att skapa Azure-prenumerationer.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Skapa en prenumeration i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Prenumerationer**.

   ![Skärmbild som visar en sökning efter prenumerationer i portalen](./media/create-subscription/billing-search-subscription-portal.png)

1. Välj **Lägg till**.

   ![Skärm bild som visar knappen Lägg till i prenumerationsvyn](./media/create-subscription/subscription-add.png)

1. Om du har åtkomst till flera faktureringskonton väljer du faktureringskontot som du vill skapa prenumerationen för.

1. Fyll i formuläret och klicka på **Skapa**. I tabellerna nedan ser du formulärfälten för de olika typerna av faktureringskonton.

**Enterprise-avtal**

|Field  |Definition  |
|---------|---------|
|Namn     | Visningsnamnet som gör att du enkelt kan identifiera prenumerationen i Azure-portalen.  |
|Erbjudande     | Välj EA Dev/Test om du planerar att använda prenumerationen för utveckling eller testning. Annars väljer du Microsoft Azure Enterprise. DevTest-erbjudandet måste vara aktiverat för ditt registreringskonto om du ska kunna skapa EA Dev/Test-prenumerationer.|

**Microsoft-kundavtal**

|Field  |Definition  |
|---------|---------|
|Faktureringsprofil     | Avgifterna för din prenumeration faktureras till den valda faktureringsprofilen. Om du bara har åtkomst till en faktureringsprofil blir valet nedtonat.     |
|Fakturaavsnitt     | Avgifterna för din prenumeration visas i det här avsnittet på faktureringsprofilens faktura. Om du bara har åtkomst till ett enda fakturaavsnitt blir valet nedtonat.  |
|Planera     | Välj Microsoft Azure-plan för DevTest om du planerar att använda den här prenumerationen för utvecklings- eller testningsarbetsbelastningar. I annat fall väljer du Microsoft Azure-plan. Om det bara är ett prenumerationsavtal aktiverat för faktureringsprofilen är det här valet nedtonat.  |
|Namn     | Visningsnamnet som gör att du enkelt kan identifiera prenumerationen i Azure-portalen.  |

**Microsoft-partneravtal**

|Field  |Definition  |
|---------|---------|
|Kund    | Prenumerationen skapas för den kund du väljer. Om du bara har en kund är det här valet nedtonat.  |
|Återförsäljare    | Den återförsäljare som ska tillhandahålla tjänster till kunden. Det här är ett valfritt fält som endast gäller för indirekta leverantörer i CSP-modellen med två nivåer. |
|Namn     | Visningsnamnet som gör att du enkelt kan identifiera prenumerationen i Azure-portalen.  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Skapa ytterligare en Azure-prenumeration programmatiskt

Du kan också skapa ytterligare prenumerationer programmatiskt. Mer information finns i:

- [Skapa EA-prenumerationer programmatiskt med det senaste API:et](programmatically-create-subscription-enterprise-agreement.md)
- [Skapa MCA-prenumerationer programmatiskt med det senaste API:et](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Skapa MPA-prenumerationer programmatiskt med det senaste API:et](Programmatically-create-subscription-microsoft-customer-agreement.md)

## <a name="next-steps"></a>Nästa steg

- [Lägg till eller ändra Azure-prenumerationsadministratörer](add-change-subscription-administrator.md)
- [Flytta resurser till ny resursgrupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Skapa hanteringsgrupper för organisering och hantering av resurser](../../governance/management-groups/create-management-group-portal.md)
- [Avbryta prenumerationen på Azure](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).