---
title: MAU fakturerings modell för externa Azure AD-identiteter
description: Lär dig mer om Azure AD external-identiteter månads aktiva användare (MAU) fakturerings modell för gäst användar samarbete (B2B) i Azure AD. Lär dig hur du länkar din Azure AD-klient till en Azure-prenumeration.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12aa400d6ca44043d3d90e78a93ae49d97a927e8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270298"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Fakturerings modell för externa Azure AD-identiteter

Priser för Azure Active Directory (Azure AD) externa identiteter baseras på månatliga aktiva användare (MAU), vilket är antalet unika användare med autentiserings aktivitet inom en kalender månad. Den här fakturerings modellen gäller både B2B (Azure AD gäst användar samarbete) och [Azure AD B2C klienter](https://docs.microsoft.com/azure/active-directory-b2c/billing). MAU-faktureringen hjälper dig att minska kostnaderna genom att erbjuda en kostnads fri nivå och flexibel, förutsägbar prissättning. I den här artikeln får du lära dig om hur du MAU fakturering och länkar dina Azure AD-klienter till en prenumeration.

> [!IMPORTANT]
> Den här artikeln innehåller ingen pris information. Den senaste informationen om fakturering och prissättning för användning finns [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>Vad behöver jag göra?

Din Azure AD-klient måste vara kopplad till en Azure-prenumeration för att kunna dra nytta av MAU-fakturering.

|Om din klient organisation är:  |Du måste:  |
|---------|---------|
| En Azure AD-klient är redan länkad till en prenumeration     | Gör ingenting. När du använder externa identiteter för att samar beta med gäst användare debiteras du automatiskt med hjälp av MAU-modellen.        |
| En Azure AD-klient har ännu inte länkats till en prenumeration     | [Länka din Azure AD-klient till en prenumeration](#link-your-azure-ad-tenant-to-a-subscription) för att aktivera Mau fakturering.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>Om månatliga aktiva användare (MAU) fakturering

I din Azure AD-klient debiteras användningen av gäst användar samarbete baserat på antalet unika gäst användare med autentisering under en kalender månad. Den här modellen ersätter förhållandet mellan 1:5 och fakturerings modell, som tillåter upp till fem gäst användare för varje Azure AD Premium licens i din klient organisation. När klienten är länkad till en prenumeration och du använder externa identiteter för att samar beta med gäst användare debiteras du automatiskt med hjälp av den MAU fakturerings modellen.
  
Den pris nivå som gäller för gäst användarna baseras på den högsta pris nivå som tilldelats din Azure AD-klient. Om den högsta pris nivån i din klient organisation är Azure AD Premium P1, gäller till exempel pris nivån Premium P1 även för gäst användarna. Om den högsta prissättningen är Azure AD Free uppmanas du att uppgradera till en Premium pris nivå när du försöker använda Premium funktioner för gäst användare.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Länka din Azure AD-klient till en prenumeration

En Azure AD-klient måste vara länkad till en Azure-prenumeration för korrekt fakturering och åtkomst till funktioner.

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett Azure-konto som har tilldelats minst [deltagar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) rollen i prenumerationen eller en resurs grupp i prenumerationen.

2. Välj den katalog som innehåller din prenumeration: Välj ikonen **katalog + prenumeration** i Azure Portal-verktygsfältet och välj sedan den katalog som innehåller din prenumeration.

    ![Välj ikonen katalog + prenumeration](media/external-identities-pricing/portal-mau-pick-directory.png)

3. Under **Azure-tjänster**väljer du **Azure Active Directory**.

4. På den vänstra menyn väljer du **externa identiteter**.

5. Under **prenumerationer**väljer du **länkade prenumerationer**.

6. I listan klient väljer du kryss rutan bredvid klienten och väljer sedan **länk prenumeration**.

    ![Välj klienten och länka en prenumeration](media/external-identities-pricing/linked-subscriptions.png)

7. I fönstret länka en prenumeration väljer du en **prenumeration** och en **resurs grupp**. Välj sedan **Använd**.

    ![Välj en prenumeration och resurs grupp](media/external-identities-pricing/link-subscription-resource.png)

När du har slutfört de här stegen faktureras din Azure-prenumeration baserat på din Azure Direct-eller Enterprise-avtal information, om tillämpligt.

## <a name="next-steps"></a>Nästa steg

Den senaste pris informationen finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).
