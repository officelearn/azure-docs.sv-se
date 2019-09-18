---
title: Lägga till eller ändra administratörer för en Azure-prenumeration | Microsoft Docs
description: Här beskrivs hur du lägger till eller ändrar administratörer för en Azure-prenumeration med hjälp av rollbaserad åtkomstkontroll (RBAC).
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "64922907"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Lägg till eller ändra administratörer för en Azure-prenumeration

Du måste ha rätt administratörsroll för att hantera åtkomst till Azure-resurser. Azure har ett behörighetssystem som kallas rollbaserad åtkomstkontroll (RBAC) där du kan välja mellan flera olika inbyggda roller. Du kan tilldela dessa roller för olika omfång, som en hanteringsgrupp, en prenumeration eller en resursgrupp.

Microsoft rekommenderar att du hanterar åtkomsten till resurser med RBAC. Om du fortfarande använder den klassiska distributionsmodellen och hanterar de klassiska resurserna med hjälp av [Azure Service Management Powershell-modulen](https://docs.microsoft.com/powershell/module/servicemanagement/azure) måste du använda en klassisk administratör. 

> [!TIP]
> Om du bara använder Azure-portalen till att hantera de klassiska resurserna behöver du inte använda någon klassisk administratör.

Mer information finns i [Azure Resource Manager jämfört med klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md) och [Administratörer för klassiska Azure-prenumerationer](../role-based-access-control/classic-administrators.md).

I den här artikeln beskrivs hur du lägger till eller ändrar administratörsroller för användare med RBAC i ett prenumerationsomfång.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare som administratör för en prenumeration

Om du vill göra en användare till administratör för en Azure-prenumeration tilldelar du rollen [Ägare](../role-based-access-control/built-in-roles.md#owner) (en RBAC-roll) i prenumerationsomfånget. Rollen ägare ger fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att ge åtkomst till andra. De här stegen är desamma som för andra rolltilldelningar.

1. Öppna [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure-portalen.

1. Klicka på prenumerationen du vill ge åtkomst till.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

    ![Skärmbild som visar rolltilldelningar](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönstret **Lägg till rolltilldelning**.

    Om du inte har behörighet att tilldela roller är det här alternativet inaktiverat.

1. I listrutan **Roll** väljer du rollen **Ägare**.

1. Välj en användare i listan **Välj**. Om du inte ser användaren i listan kan du ange visningsnamn och e-postadresser i rutan **Välj** om du vill söka i katalogen.

    ![Skärmbild där rollen Ägare är vald](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Klicka på **Spara** för att tilldela rollen.

    Efter en stund tilldelas rollen Ägare till användaren i prenumerationsomfånget.

## <a name="next-steps"></a>Nästa steg

* [Vad är rollbaserad åtkomstkontroll (RBAC)?](../role-based-access-control/overview.md)
* [Förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Anvisningar: Associera eller lägga till en Azure-prenumeration till Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Behörigheter för administratörsrollen i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.
