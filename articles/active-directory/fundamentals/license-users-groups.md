---
title: Tilldela eller ta bort licenser – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tilldelar eller ta bort Azure Active Directory-licenser från användare eller grupper.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869713967a6a89611ab2ea9ca7b7516f090a5f92
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034257"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Tilldela eller ta bort licenser i Azure Active Directorys portalen

Många Azure Active Directory (Azure AD)-tjänster kräver att du licensierar var och en av dina användare eller grupper (och associerade medlemmar) för den tjänsten. Endast användare med aktiva licenser kommer att kunna komma åt och använda de licensierade Azure AD-tjänster som är sanna.

## <a name="available-license-plans"></a>Tillgängliga licens planer

Det finns flera tillgängliga licens planer för Azure AD-tjänsten, inklusive:

- Azure AD Kostnadsfri

- Azure AD Premium P1

- Azure AD Premium P2

För detaljerad information om varje licens plan och tillhör ande licens information, se [vilken licens du behöver?](https://azure.microsoft.com/pricing/details/active-directory/).

Inte alla Microsoft-tjänster är tillgängliga på alla platser. Innan en licens kan tilldelas till en grupp, måste du ange den **användningsplats** för alla medlemmar. Du kan ange ett värde den **Azure Active Directory &gt; användare &gt; profil &gt; inställningar** område i Azure AD. Alla användare vars användnings plats inte har angetts ärver platsen för Azure AD-organisationen.

## <a name="view-license-plans-and-plan-details"></a>Visa licens planer och information om plan

Du kan visa dina tillgängliga Service planer, inklusive de enskilda licenserna, kontrol lera väntande förfallo datum och Visa antalet tillgängliga tilldelningar.

### <a name="to-find-your-service-plan-and-plan-details"></a>Hitta din service plan och planera information

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett licens administratörs konto i din Azure AD-organisation.

1. Välj **Azure Active Directory**, och välj sedan **licenser**.

    ![Sidan licenser, med antal köpta tjänster och tilldelade licenser](media/license-users-groups/license-details-blade.png)

1. Välj den **inköpta** länken för att visa sidan **produkter** och för att se de **tilldelade**, **tillgängliga**och **sista giltighets** siffrorna för dina licens planer.

    ![Sidan tjänster med service licens planer och tillhör ande licens information](media/license-users-groups/license-products-blade-with-products.png)

1. Välj ett schema namn om du vill visa dess licensierade användare och grupper.

## <a name="assign-licenses-to-users-or-groups"></a>Tilldela licenser till användare eller grupper

Se till att vem som helst att behöva använda en licensierad Azure AD-tjänsten har rätt licens. Du kan lägga till licens rättigheterna till användare eller till en hel grupp.

### <a name="to-assign-a-license-to-a-user"></a>Tilldela en licens till en användare

1. På sidan **produkter** väljer du namnet på den licens plan som du vill tilldela användaren.

    ![Sidan tjänster med en markerad service licens plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. På översikts sidan licens plan väljer du **tilldela**.

    ![Sidan tjänster med markerat alternativet tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. På den **tilldela** väljer **användare och grupper**, och sök sedan efter och välj den användare som du tilldelar licensen.

    ![Tilldela licens sidan med markerade Sök och välj alternativ](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Välj **tilldelningsalternativ**, kontrollera att du har rätt licens alternativen har aktiverats och välj sedan **OK**.

    ![Licens alternativ sidan med alla alternativ som är tillgängliga i licens planen](media/license-users-groups/license-option-blade-assignments.png)

    Den **tilldela licens** sidan uppdateringar att visa att en användare har valts och att tilldelningarna har konfigurerats.

    > [!NOTE]
    > Inte alla Microsoft-tjänster är tillgängliga på alla platser. Innan en användare kan tilldelas en licens, måste du ange den **användningsplats**. Du kan ange ett värde den **Azure Active Directory &gt; användare &gt; profil &gt; inställningar** område i Azure AD. Alla användare vars användnings plats inte har angetts ärver platsen för Azure AD-organisationen.

1. Välj **Tilldela**.

    Användaren har lagts till i listan över licensierade användare och har åtkomst till den medföljande Azure AD-tjänster.

### <a name="to-assign-a-license-to-a-group"></a>Tilldela en licens till en grupp

1. På sidan **produkter** väljer du namnet på den licens plan som du vill tilldela användaren.

    ![Bladet produkter med en markerad produkt licens plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. På den **Azure Active Directory Premium Plan 2** väljer **tilldela**.

    ![Sidan produkter med markerade alternativet för att tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. På den **tilldela** väljer **användare och grupper**, och sök sedan efter och välj den grupp som du tilldelar licensen.

    ![Tilldela licens sidan med markerade Sök och välj alternativ](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Välj **tilldelningsalternativ**, kontrollera att du har rätt licens alternativen har aktiverats och välj sedan **OK**.

    ![Licens alternativ sidan med alla alternativ som är tillgängliga i licens planen](media/license-users-groups/license-option-blade-group-assignments.png)

    Den **tilldela licens** sidan uppdateringar att visa att en användare har valts och att tilldelningarna har konfigurerats.

1. Välj **Tilldela**.

    Gruppen har lagts till i listan över licensierade grupper och alla medlemmar har åtkomst till den medföljande Azure AD-tjänster.

## <a name="remove-a-license"></a>Ta bort en licens

Du kan ta bort en licens från en användares Azure AD-användare på sidan grupp översikt för en grupp tilldelning eller genom att starta från sidan för Azure AD- **licenser** och se användare och grupper för en licens.

### <a name="to-remove-a-license-from-a-user"></a>Så här tar du bort en licens från en användare

1. På sidan **licensierade användare** för service planen väljer du den användare som inte längre ska ha licensen. Till exempel _Alain Charon_.

1. Välj **ta bort licens**.

    ![Licensierade användare sida med ta bort licens alternativet är markerat](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Det går inte att ta bort licenser som en användare ärver från en grupp direkt. Du behöver ta bort användaren från gruppen som de ärver licensen.

### <a name="to-remove-a-license-from-a-group"></a>Ta bort en licens från en grupp

1. Välj den grupp som inte längre ska ha licensen på sidan **licensierade grupper** för licens planen.

1. Välj **ta bort licens**.

    ![Licensierade grupper sida med ta bort licens alternativet är markerat](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

## <a name="next-steps"></a>Nästa steg

När du har tilldelat dina licenser, kan du utföra följande processer:

- [Identifiera och lösa licensproblem för tilldelning](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Lägg till licensierade användare till en grupp för licensiering](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenarier, begränsningar och kända problem med hjälp av grupper för att hantera licensiering i Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)
