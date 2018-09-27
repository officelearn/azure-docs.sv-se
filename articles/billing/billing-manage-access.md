---
title: Hantera åtkomst till Azure-fakturering med hjälp av roller | Microsoft Docs
description: ''
services: ''
documentationcenter: ''
author: vikramdesai01
manager: vikdesai
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: cwatson
ms.openlocfilehash: 623856f05eed44eca3752d56f047f9bb282bdc8e
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392099"
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Hantera åtkomst till faktureringsinformation för Azure med hjälp av rollbaserad åtkomstkontroll

Du kan ge åtkomst för faktureringsinformation för medlemmar i din grupp genom att tilldela något av följande användarroller i prenumerationen: kontoadministratör, tjänstadministratör, delad administratör, ägare, deltagare, läsare och fakturering läsare. De skulle ha åtkomst till faktureringsinformation i den [Azure-portalen](https://portal.azure.com/), och de kan använda den [fakturerings-API: er](billing-usage-rate-card-overview.md) att programmässigt få fakturor (en gång har valt-in) och användningsinformation. Mer information om vem som kan ge roller, och vilka roller kan göra vad, se [roller i Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="opt-in"></a> Ytterligare användare att komma åt fakturor

Kontoadministratören kan inaktivera i med hjälp av den [Azure-portalen](https://portal.azure.com/) Tillåt åtkomst till fakturor för andra användare och via API: et.

1. Som kontoadministratör, Välj din prenumeration från den [prenumerationsbladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

1. Välj **fakturor** och sedan **åtkomst till fakturor**.

    ![Skärmbild som visar hur du delegerar åtkomst till fakturor](./media/billing-manage-access/AA-optin.png)

1. Aktivera **på** åtkomst följt genom att spara ändringarna, så att användarna i prenumerationen omfattar roller för att ladda ned faktura.

    ![Skärmbild som visar på-av att delegera åtkomst till faktura](./media/billing-manage-access/AA-optinAllow.png)

Börjar tillåter tjänstadministratör, delad administratör, ägare, deltagare, läsare och Billing Reader på prenumerationen som ska hämta PDF-fakturor i Azure-portalen. Fakturor som är äldre än December 2016 är dock tillgängliga enbart till administratörskontot för tillfället.

Kontoadministratören kan också konfigurera om du vill att fakturor skickas via e-post. Mer information finns i [får fakturan i e-post](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Lägga till användare till rollen Läsare för fakturering

Rollen Billing Reader har skrivskyddad åtkomst till faktureringsinformation för prenumerationen på Azure-portalen och ingen åtkomst till tjänster, till exempel virtuella datorer och storage-konton. Tilldela rollen Billing Reader till någon som behöver åtkomst till faktureringsinformation för prenumerationen men inte möjlighet att hantera Azure-tjänster. Den här rollen är lämplig för användare i en organisation som bara utför finansiella och kostnad för Azure-prenumerationer.

1. Välj din prenumeration från den [prenumerationsbladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

1. Välj **åtkomstkontroll (IAM)** och klicka sedan på **Lägg till**.

    ![Skärmbild som visar IAM i prenumerationsbladet](./media/billing-manage-access/select-iam.PNG)

1. Välj **Billing Reader** i den **Välj en roll** sidan.

    ![Skärmbild som visar Billing Reader i popup-vy](./media/billing-manage-access/select-roles.PNG)

1. Skriv e-post för den användare du vill bjuda in och klicka sedan på **OK** att skicka inbjudan.

    ![Skärmbild som visar för att ange e-postadress om du vill bjuda in någon](./media/billing-manage-access/add-user.PNG)

1. Följ instruktionerna i inbjudan e-postmeddelandet för att logga in som en Billing Reader.

    ![Skärmbild som visar vad Billing Reader kan se i Azure-portalen](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> Billing Reader-funktionen är en förhandsversion och stöder ännu inte företagsprenumerationer (EA) eller icke-globala moln.

## <a name="adding-users-to-other-roles"></a>Lägga till användare i andra roller

Användare i andra roller som ägare eller deltagare, når inte bara faktureringsinformation, men Azure-tjänster samt. För att hantera dessa roller, se [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Vem som kan komma åt den [Kontocenter](https://account.windowsazure.com)?

Är bara kontoadministratören kan logga in på Azure kontocenter. Kontoadministratören är juridiska ägare av prenumerationen. Som standard är den person som har registrerat sig för eller har köpt Azure-prenumerationen kontoadministratör, såvida inte den [äganderätten till prenumerationen har överförts](billing-subscription-transfer.md) till någon annan. Kontoadministratören kan skapa prenumerationer, avbryta prenumerationer, ändra faktureringsadress för en prenumeration och hantera åtkomstprinciper för prenumerationen.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
