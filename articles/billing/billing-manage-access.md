---
title: Hantera åtkomst till Azure-fakturering | Microsoft Docs
description: ''
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
ms.author: cwatson
ms.openlocfilehash: 7a4e19ae5bf770949623f4cee7fa0d3033ccfa29
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582623"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Hantera åtkomst till faktureringsinformation för Azure

För de flesta prenumerationer kan du ge fakturering åtkomst till gruppmedlemmarna från **prenumerationer** i Azure-portalen. Om du är Azure-kund med ett Enterprise-avtal (EA-kund) och Enterprise-administratör, kan du ge behörigheter till avdelningen administratörer och kontoinnehavare i Enterprise portal.

## <a name="give-access-to-billing"></a>Ge åtkomst till fakturering

Alla utom EA-kunder kan bevilja åtkomst till faktureringsinformation genom att tilldela något av följande användarroller till medlemmar i din grupp:

- Kontoadministratör
- Tjänstadministratör
- Medadministratör
- Ägare
- Deltagare
- Läsare
- Faktureringsläsare

Om du vill tilldela roller, se [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

Dessa roller har åtkomst till faktureringsinformation i den [Azure-portalen](https://portal.azure.com/). Personer som har tilldelats rollerna kan också använda den [fakturerings-API: er](billing-usage-rate-card-overview.md) att programmässigt få fakturor och användningsinformation. Mer information finns i [roller i Azure RBAC](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Låt användare ladda ned fakturor

När du utvärdera Tilldela lämpliga roller till medlemmar i gruppen, kontoadministratör måste aktivera för att ladda ned fakturor i Azure-portalen. Fakturor som är äldre än December 2016 är tillgängliga att kontoadministratör.

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Som kontoadministratör, Välj din prenumeration från den [prenumerationsbladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

1. Välj **fakturor** och sedan **åtkomst till fakturor**.

    ![Skärmbild som visar hur du delegerar åtkomst till fakturor](./media/billing-manage-access/AA-optin.png)

1. Välj **på** och spara.

    ![Skärmbild som visar på-av att delegera åtkomst till faktura](./media/billing-manage-access/AA-optinAllow.png)

Kontoadministratören kan också konfigurera att fakturor ska skickas via e-post. Mer information finns i [Få fakturan via e-post](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Ge läsbehörighet till fakturering

Tilldela rollen Billing Reader till någon som behöver skrivskyddad åtkomst till faktureringsinformation för prenumerationen men inte möjligheten att hantera eller skapa Azure-tjänster. Den här rollen är lämplig för användare i en organisation som ansvarar för den finansiella och kostnad för Azure-prenumerationer.

Om du är en EA-kund, tilldela en ägare eller administratör för avdelning rollen Billing Reader till gruppmedlemmar. Men för den Billing Reader visa faktureringsinformation för avdelning eller konto, Enterprise-administratör måste aktivera **AO visa debiteringar** eller **DA visa debiteringar** principer i Enterprise portal.

Billing Reader-funktionen är en förhandsversion och har stöd inte för icke-globala moln.

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj prenumerationen på [prenumerationsbladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) på Azure-portalen.

1. Välj **åtkomstkontroll (IAM)**.
1. Välj **rolltilldelningar** att visa alla rolltilldelningar för den här prenumerationen.
1. Välj **Lägg till** > **Lägg till rolltilldelning**.
1. I den **rollen** listrutan väljer du **Billing Reader**.
1. I den **Välj** textrutan anger du namnet eller e-post för den användare som du vill lägga till.
1. Välj **Spara**.
1. Efter en liten stund tilldelas användaren rollen Billing Reader prenumerationsområde.
1. Billing Reader tar emot ett e-postmeddelande med en länk för att logga in.

    ![Skärmbild som visar vad Billing Reader kan se i Azure-portalen](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Tillåt avdelning administratör eller Kontoägare fakturering åtkomst

Enterprise-administratören kan tillåta avdelning administratörer och kontoinnehavare för att visa information om användning och kostnaderna knutna till avdelningar och konton som de hanterar.

1. Som företagsadministratör, logga in på den [EA-portalen](https://ea.azure.com/).
1. Välj **hantera**.
1. Under **registrering**, ändra den **DA visa debiteringar** till **aktiverad** för avdelning-administratören att visa användning och kostnader.
1. Ändra **AO visa debiteringar** till **aktiverad** för Kontoägaren för att visa användning och kostnader.


Mer information finns i [förstå Azure Enterprise-avtal administrativa roller i Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Konto-administratörer har åtkomst till Kontocenter

Kontoadministratören är juridiska ägare av prenumerationen. Som standard är den person som har registrerat sig för eller har köpt Azure-prenumerationen kontoadministratör, såvida inte den [äganderätten till prenumerationen har överförts](billing-subscription-transfer.md) till någon annan. Kontoadministratören kan skapa prenumerationer, avbryta prenumerationer, ändra faktureringsadress för en prenumeration och hantera åtkomstprinciper för prenumeration från den [Kontocenter](https://account.azure.com/Subscriptions).

## <a name="next-steps"></a>Nästa steg

- Användare i andra roller som ägare eller deltagare, når inte bara faktureringsinformation, men Azure-tjänster samt. För att hantera dessa roller, se [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).
- Mer information om roller finns i [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
