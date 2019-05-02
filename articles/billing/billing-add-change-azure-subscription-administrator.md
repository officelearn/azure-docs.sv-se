---
title: Lägga till eller ändra Azure-prenumerationsadministratörer | Microsoft Docs
description: Beskriver hur du lägger till eller ändrar en administratör för Azure-prenumeration med hjälp av rollbaserad åtkomstkontroll (RBAC).
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 000315a2d751a05d3e401ee1bb9f593c6e321194
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922907"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Lägga till eller ändra Azure-prenumerationsadministratörer

Du måste ha rätt administratörsroll för att hantera åtkomst till Azure-resurser. Azure har ett auktoriseringssystem som kallas rollbaserad åtkomstkontroll (RBAC) med flera inbyggda roller som du kan välja bland. Du kan tilldela dessa roller med olika omfång, till exempel hanteringsgruppen, prenumeration eller resursgrupp.

Microsoft rekommenderar att du hanterar åtkomst till resurser med hjälp av RBAC. Men om du fortfarande använder den klassiska distributionsmodellen och hantera klassiska resurser med hjälp av [Azure Service Management PowerShell-modulen](https://docs.microsoft.com/powershell/module/servicemanagement/azure), måste du använda en klassisk administratör. 

> [!TIP]
> Om du bara använder Azure-portalen för att hantera klassiska resurser, behöver du inte använda den klassiska administratören.

Mer information finns i [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md) och [Azure klassiska prenumerationsadministratörer](../role-based-access-control/classic-administrators.md).

Den här artikeln beskrivs hur lägga till eller ändra administratörsrollen för en användare med RBAC prenumerationsområde.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare som administratör för en prenumeration

Om du vill göra en administratör för en Azure-prenumeration för en användare, tilldela dem den [ägare](../role-based-access-control/built-in-roles.md#owner) roll (en RBAC-roll) prenumerationsområde. Ägarrollen ger användaren fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att delegera åtkomst till andra. De här stegen är desamma som alla andra rolltilldelning.

1. I Azure-portalen öppnar [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Klicka på den prenumeration där du vill bevilja åtkomst.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på den **rolltilldelningar** fliken för att visa alla rolltilldelningar för den här prenumerationen.

    ![Skärmbild som visar rolltilldelningar](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** att öppna den **Lägg till rolltilldelning** fönstret.

    Om du inte har behörighet att tilldela roller, inaktiveras alternativet.

1. I den **rollen** listrutan, väljer den **ägare** roll.

1. I den **Välj** väljer du en användare. Om du inte ser användare i listan kan du skriva i den **Välj** om du vill söka i katalogen för visningsnamn och e-postadresser.

    ![Skärmbild som visar ägarrollen valt](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Klicka på **spara** du tilldela rollen.

    Efter en liten stund tilldelas användaren ägarrollen prenumerationsområde.

## <a name="next-steps"></a>Nästa steg

* [Vad är rollbaserad åtkomstkontroll (RBAC)?](../role-based-access-control/overview.md)
* [Förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Anvisningar: Associera eller lägga till en Azure-prenumeration till Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Behörigheter för administratörsrollen i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
