---
title: Visa gransknings rapport för Azure Resource roles i PIM – Azure AD | Microsoft Docs
description: Visa aktivitet och gransknings historik för Azure Resource roles i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2585457d22b773407c41bb6ea82c30a25fbfb88e
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847155"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Visa aktivitet och gransknings historik för Azures resurs roller i Privileged Identity Management

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM) kan du Visa aktivitet, aktiveringar och gransknings historik för Azure-resurser i din organisation. Detta omfattar prenumerationer, resurs grupper och till och med virtuella datorer. Alla resurser i Azure Portal som utnyttjar RBAC-funktionen (rollbaserad åtkomst kontroll) i Azure kan dra nytta av funktionerna för säkerhet och livs cykel hantering i Privileged Identity Management.

## <a name="view-activity-and-activations"></a>Visa aktivitet och aktiveringar

Om du vill se vilka åtgärder en viss användare har tagit i olika resurser kan du Visa den Azure Resource-aktivitet som är associerad med en viss aktiverings period.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill visa aktivitet och aktivering för.

1. Klicka på **roller** eller **medlemmar**.

1. Klicka på en användare.

    Du ser en grafisk vy över användarens åtgärder i Azure-resurser efter datum. Den visar även de senaste roll aktiveringarna under samma tids period.

    ![Användar information med sammanfattning av resurs aktivitet och roll aktiveringar](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Klicka på en speciell roll aktivering om du vill visa information och motsvarande Azure-resursinformation som inträffat när användaren var aktiv.

    ![Roll aktivering har valts och aktivitets information visas efter datum](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportera roll tilldelningar med underordnade

Du kan ha ett krav på efterlevnad där du måste ange en fullständig lista över roll tilldelningar till granskare. Med Privileged Identity Management kan du fråga roll tilldelningar för en speciell resurs, vilket innefattar roll tilldelningar för alla underordnade resurser. Tidigare var det svårt för administratörer att få en fullständig lista över roll tilldelningar för en prenumeration och de behövde exportera roll tilldelningar för varje enskild resurs. Med hjälp av Privileged Identity Management kan du fråga efter alla aktiva och berättigade roll tilldelningar i en prenumeration, inklusive roll tilldelningar för alla resurs grupper och resurser.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill exportera roll tilldelningar för, till exempel en prenumeration.

1. Klicka på **medlemmar**.

1. Klicka på **Exportera** för att öppna fönstret Exportera medlemskap.

    ![Exportera medlemskaps fönstret för att exportera alla medlemmar](media/azure-pim-resource-rbac/export-membership.png)

1. Klicka på **Exportera alla medlemmar** för att exportera alla roll tilldelningar i en CSV-fil.

    ![Exporterade roll tilldelningar i CSV-fil som visas i Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Visa resurs gransknings historik

Resurs granskning ger dig en översikt över alla roll aktiviteter för en resurs.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill visa gransknings historik för.

1. Klicka på **resurs granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Resurs gransknings lista med filter](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. För **gransknings typ**väljer du **Aktivera (tilldelad + aktive rad)** .

    ![Resurs gransknings lista som filtrerats efter aktivera gransknings typ](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Under **åtgärd**klickar du på **(aktivitet)** för en användare för att se användarens aktivitets information i Azure-resurser.

    ![Information om användar aktiviteter för en viss åtgärd](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Visa min granskning

I min granskning kan du Visa din personliga roll aktivitet.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill visa gransknings historik för.

1. Klicka på **min granskning**.

1. Filtrera historiken med ett fördefinierat datum eller anpassat intervall.

    ![Gransknings lista för den aktuella användaren](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resurs roller i Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Godkänn eller neka begär Anden för Azures resurs roller i Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Visa gransknings historik för Azure AD-roller i Privileged Identity Management](pim-how-to-use-audit-log.md)
