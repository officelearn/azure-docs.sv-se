---
title: "Hantera åtkomst till Azure fakturering med roller | Microsoft Docs"
description: 
services: 
documentationcenter: 
author: vikramdesai01
manager: vikdesai
editor: 
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: vikdesai
ms.openlocfilehash: c70904097f139bc2178feed83f1cf1274f3c738d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="manage-access-to-billing-information-for-azure-using-role-based-access-control"></a>Hantera åtkomst till faktureringsinformation för Azure med hjälp av rollbaserad åtkomstkontroll

Du kan ge åtkomst för Azure faktureringsinformation för medlemmar i gruppen genom att tilldela någon av följande användarroller till din prenumeration: kontoadministratör, administratör, medadministratör, ägare, deltagare, läsare och fakturering läsare. De skulle ha tillgång till faktureringsinformationen i den [Azure-portalen](https://portal.azure.com/), och de kan använda den [fakturerings-API: er](billing-usage-rate-card-overview.md) få programmässigt fakturor (en gång deltar i) och användningsinformation. Mer information om vem som kan ge roller, och vilka roller kan göra vad, se [roller i Azure RBAC](../active-directory/role-based-access-built-in-roles.md).

## <a name="opt-in"></a>Ytterligare användare att komma åt fakturor

Kontoadministratören måste välja med hjälp av den [Azure-portalen](https://portal.azure.com/) Tillåt åtkomst till fakturor för andra användare och via API.

1. Som tjänstadministratör, väljer din prenumeration från den [prenumerationer bladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

1. Välj **fakturor** och sedan **åtkomst till fakturor**.

    ![Skärmbild som visar hur du delegerar åtkomst till fakturor](./media/billing-manage-access/AA-optin.png)

1. Aktivera **på** åtkomst följt av sparar ändringarna, så att användarna i prenumerationen omfång roller för att ladda ned faktura.

    ![Skärmbild som visar på av till delegera åtkomst till faktura](./media/billing-manage-access/AA-optinAllow.png)

Börjar tillåter administratör, medadministratör, ägare, deltagare, läsare och fakturering läsare i prenumeration för att hämta PDF fakturor i Azure-portalen. Fakturor som är äldre än December 2016 finns dock att kontoadministratör för tillfället.

Kontoadministratören kan också konfigurera om du vill att fakturor skickas via e-post. Läs mer i [hämta fakturan i e-post](billing-download-azure-invoice-daily-usage-date.md).

## <a name="adding-users-to-the-billing-reader-role"></a>Lägga till användare i rollen Läsare fakturering

Rollen fakturering läsare har skrivskyddad åtkomst till faktureringsinformation för prenumerationen i Azure-portalen och ingen åtkomst till tjänster som virtuella datorer och storage-konton. Tilldela rollen fakturering läsare någon som behöver åtkomst till faktureringsinformation för prenumerationen men inte möjligheten att hantera Azure-tjänster. Den här rollen är lämplig för användare i en organisation som endast utför ekonomi- och hantering för Azure-prenumerationer.

1. Välj din prenumeration från den [prenumerationer bladet](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

1. Välj **åtkomstkontroll (IAM)** och klicka sedan på **Lägg till**.

    ![Skärmbild som visar IAM i prenumerationsbladet](./media/billing-manage-access/select-iam.PNG)

1. Välj **fakturering Reader** i den **Välj en roll** sidan.

    ![Skärmbild som visar fakturering läsare i popup-vy](./media/billing-manage-access/select-roles.PNG)

1. Skriv e-post för den användare du vill bjuda in och klicka sedan på **OK** att skicka inbjudan.

    ![Skärmbild som visar för att ange e-postadress för att bjuda in någon](./media/billing-manage-access/add-user.PNG)

1. Följ anvisningarna i e-postmeddelandet inbjudan att logga in som en läsare för fakturering.

    ![Skärmbild som visar vad läsaren fakturering kan se i Azure-portalen](./media/billing-manage-access/billing-reader-view.png)

> [!NOTE]
> Fakturering Reader-funktionen är i förhandsvisning och ännu stöder inte företagsprenumerationer (EA) eller icke-globala moln.

## <a name="adding-users-to-other-roles"></a>Lägga till användare i andra roller

Användare i andra roller, till exempel ägare eller deltagare, kan komma åt inte bara faktureringsinformation, men Azure-tjänster. Om du vill hantera dessa roller finns [lägga till eller ändra Azure-administratörsroller som hanterar prenumerationen eller tjänster](billing-add-change-azure-subscription-administrator.md).

## <a name="who-can-access-the-account-centerhttpsaccountwindowsazurecom"></a>Vem som kan komma åt den [Kontocenter](https://account.windowsazure.com)?

Enbart administratörskontot kan logga in till mitt konto. Kontoadministratören är juridiska ägaren av prenumerationen. Den person som registrerade sig för eller har köpt Azure-prenumerationen är som standard kontoadministratör, såvida inte den [prenumeration ägarskap överfördes](billing-subscription-transfer.md) till någon annan. Kontoadministratören kan skapa prenumerationer, avbryta prenumerationer, ändra faktureringsadress för en prenumeration och hantera åtkomstregler för prenumerationen.

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
