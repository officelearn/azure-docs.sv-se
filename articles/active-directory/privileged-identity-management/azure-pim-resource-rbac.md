---
title: Visa granskningsrapport för Azure-resursroller i PIM (Privileged Identity Management) – Azure AD | Microsoft-dokument
description: Visa aktivitets- och granskningshistorik för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329646"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Visa aktivitets- och granskningshistorik för Azure-resursroller i Privilegierad identitetshantering

Med Azure Active Directory (Azure AD) Privilegierad identitetshantering (PIM) kan du visa aktivitet, aktiveringar och granskningshistorik för Azure-resursroller inom organisationen. Detta inkluderar prenumerationer, resursgrupper och till och med virtuella datorer. Alla resurser i Azure-portalen som utnyttjar Azure-rollbaserade åtkomstkontrollfunktioner kan dra nytta av säkerhets- och livscykelhanteringsfunktionerna i Privilegierad identitetshantering.

> [!NOTE]
> Om din organisation har lagt ut hanteringsfunktioner på entreprenad till en tjänsteleverantör som använder [Azure-delegerad resurshantering](../../lighthouse/concepts/azure-delegated-resource-management.md)visas inte rolltilldelningar som auktoriserats av den tjänsteleverantören här.

## <a name="view-activity-and-activations"></a>Visa aktivitet och aktiveringar

Om du vill se vilka åtgärder en viss användare har vidtagit i olika resurser kan du visa azure-resursaktiviteten som är associerad med en viss aktiveringsperiod.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill visa aktivitet och aktiveringar för.

1. Välj **Roller** eller **Medlemmar**.

1. Välj en användare.

    Du ser en sammanfattning av användarens åtgärder i Azure-resurser efter datum. Den visar också de senaste rollaktiveringarna under samma tidsperiod.

    ![Användarinformation med sammanfattning av resursaktivitet och rollaktiveringar](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Välj en specifik rollaktivering för att se information och motsvarande Azure-resursaktivitet som inträffade när användaren var aktiv.

    [![Rollaktivering markerad och aktivitetsinformation](media/azure-pim-resource-rbac/export-membership.png "Rollaktivering markerad och aktivitetsinformation")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Exportera rolluppgifter med underordnade

Du kan ha ett efterlevnadskrav där du måste tillhandahålla en fullständig lista över rolltilldelningar till revisorer. Med privilegierad identitetshantering kan du fråga rolltilldelningar på en viss resurs, vilket inkluderar rolltilldelningar för alla underordnade resurser. Tidigare var det svårt för administratörer att få en fullständig lista över rolltilldelningar för en prenumeration och de var tvungna att exportera rolltilldelningar för varje specifik resurs. Med privilegierad identitetshantering kan du fråga efter alla aktiva och kvalificerade rolltilldelningar i en prenumeration, inklusive rolltilldelningar för alla resursgrupper och resurser.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill exportera rolltilldelningar för, till exempel en prenumeration.

1. Välj **medlemmar**.

1. Välj **Exportera** om du vill öppna fönstret Exportera medlemskap.

    [![Exportera medlemsfönstret för att exportera alla medlemmar](media/azure-pim-resource-rbac/export-membership.png "Exportera medlemskapssida för att exportera alla medlemmar")](media/azure-pim-resource-rbac/export-membership.png)

1. Välj **Exportera alla medlemmar** om du vill exportera alla rolltilldelningar i en CSV-fil.

    ![Exporterade rolltilldelningar i CSV-fil som visas i Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Visa historik för resursgranskning

Resursgranskning ger dig en vy över all rollaktivitet för en resurs.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill visa granskningshistorik för.

1. Välj **Resursgranskning**.

1. Filtrera historiken med hjälp av ett fördefinierat datum eller anpassat intervall.

    [![Lista över resursgranskning med filter](media/azure-pim-resource-rbac/rbac-resource-audit.png "Lista över resursgranskning med filter")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. För **granskningstyp**väljer du **Aktivera (Tilldelad + Aktiverad)**.

    [![Resursgranskningslista filtrerad efter Aktivera granskningstyp](media/azure-pim-resource-rbac/rbac-audit-activity.png "Lista över resursgranskning filtrerad efter Aktivera")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![Resursgranskningslista som filtreras efter Aktivera granskningstyp](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Under **Åtgärd**klickar du **på (aktivitet)** för att en användare ska kunna se användarens aktivitetsinformation i Azure-resurser.

    ![Information om användaraktivitet för en viss åtgärd](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Visa min granskning

Med min granskning kan du visa din personliga rollaktivitet.

1. Öppna **Azure AD-privilegierad identitetshantering**.

1. Välj **Azure-resurser**.

1. Välj den resurs som du vill visa granskningshistorik för.

1. Välj **Min granskning**.

1. Filtrera historiken med hjälp av ett fördefinierat datum eller anpassat intervall.

    [![Granskningslista för den aktuella användaren](media/azure-pim-resource-rbac/my-audit-time.png "Granskningslista för den aktuella användaren")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Åtkomst till granskningshistorik kräver antingen en global administratör eller en privilegierad rolladministratörsroll.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Få orsak, godkännare och biljettnummer för godkännandehändelser

1. Logga in på [Azure-portalen](https://aad.portal.azure.com) med administratörsbehörigheter för privilegierad roll och öppna Azure AD.
1. Välj **Granskningsloggar**.
1. Använd **filtret Tjänst** om du bara vill visa granskningshändelser för tjänsten Privilegierad identitetshantering. På sidan **Granskningsloggar** kan du:

    - Se orsaken till en granskningshändelse i kolumnen **Statusorsak.**
    - Se godkännaren i kolumnen **Initierad av (aktör)** för händelsen "Lägg till medlem i godkänd rollbegäran".

    [![Filtrera granskningsloggen för PIM-tjänsten](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrera granskningsloggen för PIM-tjänsten")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Välj en granskningslogghändelse om du vill visa biljettnumret på fliken **Aktivitet** i **informationsfönstret.**
  
    [![Kontrollera biljettnumret för granskningshändelsen](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Kontrollera biljettnumret för revisionshändelsen")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Du kan visa beställaren (den person som aktiverar rollen) på fliken **Mål** i **informationsfönstret** för en granskningshändelse. Det finns tre måltyper för Azure-resursroller:

    - Rollen (**Typ** = Roll)
    - Beställaren (**Typ** = Övrigt)
    - Godkännaren (**Typ** = Användare)

    [![Kontrollera måltypen](media/azure-pim-resource-rbac/audit-event-target-type.png "Kontrollera måltypen")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Vanligtvis är logghändelsen omedelbart ovanför godkännandehändelsen en händelse för "Lägg till medlem i roll **slutförd" där den initierade av (aktören)** är beställaren. I de flesta fall behöver du inte hitta beställaren i godkännandebegäran från ett granskningsperspektiv.

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-assign-roles.md)
- [Godkänna eller neka begäranden om Azure-resursroller i Privilegierad identitetshantering](pim-resource-roles-approval-workflow.md)
- [Visa granskningshistorik för Azure AD-roller i privilegierad identitetshantering](pim-how-to-use-audit-log.md)
