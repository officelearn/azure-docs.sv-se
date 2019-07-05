---
title: Hantera åtkomst till Azure-fakturering | Microsoft Docs
description: Lär dig mer om att ge åtkomst till din faktureringsinformation för medlemmar i ditt team.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 6726c876d0895f9488aa2ae5c225a6b2ac19e69f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491082"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Hantera åtkomst till faktureringsinformation för Azure

Du kan ange andra åtkomst till faktureringsinformation för ditt konto i Azure-portalen. Typ av fakturering roller och instruktionerna för att ge åtkomst till faktureringsinformation variera beroende på typ av ditt faktureringskonto. För att avgöra vilken typ av ditt faktureringskonto, se [Kontrollera vilken typ av ditt faktureringskonto](#check-the-type-of-your-billing-account).

Artikeln gäller för kunder med konton för Microsoft Online Service-programmet. Om du är Azure-kund med ett Enterprise Agreement (EA) och Enterprise-administratör, kan du ge behörigheter till avdelningen administratörer och kontoinnehavare i Enterprise portal. Mer information finns i [förstå Azure Enterprise-avtal administrativa roller i Azure](billing-understand-ea-roles.md). Om du är en Microsoft kundavtal kund visas [förstå Microsoft kundavtal administrativa roller i Azure](billing-understand-mca-roles.md). 

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Kontoadministratörer för Microsoft Online Service-programmet konton

En kontoadministratör är ensam ägare för ett faktureringskonto för Microsoft Online Service-Program. Rollen tilldelas till en person som registrerat sig för Azure. Kontoadministratörer har behörighet att utföra olika fakturering uppgifter som att skapa prenumerationer, visa fakturor eller ändra faktureringen för en prenumeration.

## <a name="give-others-access-to-view-billing-information"></a>Ge andra användare behörighet att visa faktureringsinformation

Kontoadministratören kan bevilja andra åtkomst till faktureringsinformation genom att tilldela någon av följande roller för en prenumeration i sitt konto.

- Tjänstadministratör
- Medadministratör
- Ägare
- Deltagare
- Läsare
- Faktureringsläsare

Dessa roller har åtkomst till faktureringsinformation i den [Azure-portalen](https://portal.azure.com/). Personer som tilldelas dessa roller kan också använda den [fakturerings-API: er](billing-usage-rate-card-overview.md) att programmässigt få fakturor och användningsinformation.

Om du vill tilldela roller, se [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

** Om du är en EA-kund kan kan Kontoägare tilldela rollen ovan för andra användare av sina team. Men för dessa användare kan visa faktureringsinformation, Enterprise-administratör måste aktivera AO Visa avgifter i Enterprise portal.


### <a name="opt-in"></a> Låt användare ladda ned fakturor

När en kontoadministratör har tilldelat lämpliga roller till andra användare, måste de aktiverar åtkomst till ladda ner fakturor i Azure-portalen. Fakturor som är äldre än December 2016 är tillgängliga att kontoadministratör.

1. Logga in på den [Azure-portalen](https://portal.azure.com/), som kontot Administratör

1. Sök på **Cost Management + fakturering**.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-manage-access/billing-search-cost-management-billing.png)
 
1. Välj **prenumerationer** från det vänstra fönstret. Beroende på din åtkomst, du kan behöva väljer du en fakturering omfattning och välj sedan **prenumerationer**.
 
    ![Skärmbild som visar att välja prenumerationer](./media/billing-manage-access/billing-select-subscriptions.png)

1. Välj **fakturor** och sedan **Fakturaåtkomsten**.

    ![Skärmbild som visar hur du delegerar åtkomst till fakturor](./media/billing-manage-access/AA-optin.png)

1. Välj **på** och spara.

    ![Skärmbild som visar på-av att delegera åtkomst till faktura](./media/billing-manage-access/AA-optinAllow.png)

Kontoadministratören kan också konfigurera att fakturor ska skickas via e-post. Mer information finns i [Få fakturan via e-post](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Ge läsbehörighet till fakturering

Tilldela rollen Billing Reader till någon som behöver skrivskyddad åtkomst till faktureringsinformation för prenumerationen men inte möjligheten att hantera eller skapa Azure-tjänster. Den här rollen är lämplig för användare i en organisation som ansvarar för den finansiella och kostnad för Azure-prenumerationer.

Billing Reader-funktionen är en förhandsversion och har stöd inte för icke-globala moln.

1. Logga in på den [Azure-portalen](https://portal.azure.com/), som kontot Administratör

1. Sök på **Cost Management + fakturering**.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-manage-access/billing-search-cost-management-billing.png)

1. Välj **prenumerationer** från det vänstra fönstret. Beroende på din åtkomst, du kan behöva väljer du en fakturering omfattning och välj sedan **prenumerationer**.
 
    ![Skärmbild som visar att välja prenumerationer](./media/billing-manage-access/billing-select-subscriptions.png)

1. Välj **åtkomstkontroll (IAM)** .
1. Välj **Lägg till** högst upp på sidan.

    ![Skärmbild som visar att klicka på Lägg till rolltilldelning](./media/billing-manage-access/billing-click-add-role-assignment.png)

1. I den **rollen** listrutan väljer du **Billing Reader**.
1. I den **Välj** textrutan anger du namnet eller e-post för den användare som du vill lägga till.
1. Välj användaren.
1. Välj **Spara**.
    ![Skärmbild som visar att klicka på Lägg till rolltilldelning](./media/billing-manage-access/billing-save-role-assignment.png)

1. Efter en liten stund tilldelas användaren rollen Billing Reader för prenumerationen.

** Om du är en EA-kund, tilldela en ägare eller administratör för avdelning rollen Billing Reader till gruppmedlemmar. Men för den Billing Reader visa faktureringsinformation för avdelning eller konto, Enterprise-administratör måste aktivera **AO visa debiteringar** eller **DA visa debiteringar** principer i Enterprise portal.

## <a name="check-the-type-of-your-billing-account"></a>Kontrollera typ av ditt faktureringskonto
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Nästa steg

- Användare i andra roller som ägare eller deltagare, når inte bara faktureringsinformation, men Azure-tjänster samt. För att hantera dessa roller, se [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).
- Mer information om roller finns i [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
