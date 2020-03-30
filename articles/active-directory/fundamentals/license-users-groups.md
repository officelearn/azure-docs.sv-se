---
title: Tilldela eller ta bort licenser - Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du tilldelar eller tar bort Azure Active Directory-licenser från dina användare eller grupper.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128831"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Tilldela eller ta bort licenser i Azure Active Directory-portalen

Många Azure Active Directory-tjänster (Azure AD) kräver att du licensierar var och en av dina användare eller grupper (och associerade medlemmar) för den tjänsten. Endast användare med aktiva licenser kan komma åt och använda de licensierade Azure AD-tjänster som det är sant för. Licenser tillämpas per klient och överförs inte till andra klienter. 

## <a name="available-license-plans"></a>Tillgängliga licensplaner

Det finns flera licensplaner tillgängliga för Azure AD-tjänsten, inklusive:

- Azure AD Kostnadsfri

- Azure AD Premium P1

- Azure AD Premium P2

Specifik information om varje licensplan och tillhörande licensieringsinformation finns i [Vilken licens behöver jag?](https://azure.microsoft.com/pricing/details/active-directory/)

Alla Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en grupp måste du ange **användningsplatsen** för alla medlemmar. Du kan ange det här värdet i området **Azure Active Directory &gt; Users &gt; Profile &gt; Settings** i Azure AD. Alla användare vars användningsplats inte har angetts ärver platsen för Azure AD-organisationen.

## <a name="view-license-plans-and-plan-details"></a>Visa licensplaner och planera information

Du kan visa tillgängliga serviceplaner, inklusive de enskilda licenserna, kontrollera väntande förfallodatum och visa antalet tillgängliga tilldelningar.

### <a name="to-find-your-service-plan-and-plan-details"></a>Så här hittar du serviceplanen och planerar information

1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett licensadministratörskonto i din Azure AD-organisation.

1. Välj **Azure Active Directory**och välj sedan **Licenser**.

    ![Sidan Licenser, med antal köpta tjänster och tilldelade licenser](media/license-users-groups/license-details-blade.png)

1. Välj länken **Köpt** om du vill visa sidan **Produkter** och se numren **Tilldelad,** **Tillgänglig**och **Förfallogående** för dina licensplaner.

    ![tjänster sida, med servicelicens planer och tillhörande licensinformation](media/license-users-groups/license-products-blade-with-products.png)

1. Välj ett plannamn om du vill visa dess licensierade användare och grupper.

## <a name="assign-licenses-to-users-or-groups"></a>Tilldela licenser till användare eller grupper

Se till att alla som behöver använda en licensierad Azure AD-tjänst har rätt licens. Du kan lägga till licensrättigheterna för användare eller i en hel grupp.

### <a name="to-assign-a-license-to-a-user"></a>Så här tilldelar du en licens till en användare

1. På sidan **Produkter** väljer du namnet på den licensplan som du vill tilldela användaren.

    ![tjänstsidan, med markerad tjänstlicensplan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. På översiktssidan för licensplanen väljer du **Tilldela**.

    ![sidan tjänster, med markerat alternativ tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. På sidan **Tilldela** väljer du **Användare och grupper**och söker sedan efter och väljer den användare som du tilldelar licensen.

    ![Tilldela licenssida, med markerade sök- och markeringsalternativ](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Välj **Tilldelningsalternativ,** se till att du har aktiverat lämpliga licensalternativ och välj sedan **OK**.

    ![Sidan Licensalternativ, med alla alternativ som är tillgängliga i licensplanen](media/license-users-groups/license-option-blade-assignments.png)

    Sidan **Tilldela licensuppdateringar** för att visa att en användare är markerad och att tilldelningarna är konfigurerade.

    > [!NOTE]
    > Alla Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste du ange **användningsplatsen**. Du kan ange det här värdet i området **Azure Active Directory &gt; Users &gt; Profile &gt; Settings** i Azure AD. Alla användare vars användningsplats inte har angetts ärver platsen för Azure AD-organisationen.

1. Välj **Tilldela**.

    Användaren läggs till i listan över licensierade användare och har åtkomst till de inkluderade Azure AD-tjänsterna.
    > [!NOTE]
    > Licenser kan också tilldelas direkt till en användare från användarens **licenssida.** Om en användare har en licens som tilldelats via ett gruppmedlemskap och du vill tilldela samma licens direkt till användaren, kan den endast göras från sidan **Produkter** som nämns i steg 1.

### <a name="to-assign-a-license-to-a-group"></a>Så här tilldelar du en licens till en grupp

1. På sidan **Produkter** väljer du namnet på den licensplan som du vill tilldela användaren.

    ![Produktblad, med markerad produktlicensplan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. På sidan **Azure Active Directory Premium Plan 2** väljer du **Tilldela**.

    ![Sidan Produkter, med markerat alternativ tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. På sidan **Tilldela** väljer du **Användare och grupper**och söker sedan efter och väljer den grupp som du tilldelar licensen.

    ![Tilldela licenssida, med markerade sök- och markeringsalternativ](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Välj **Tilldelningsalternativ,** se till att du har aktiverat lämpliga licensalternativ och välj sedan **OK**.

    ![Sidan Licensalternativ, med alla alternativ som är tillgängliga i licensplanen](media/license-users-groups/license-option-blade-group-assignments.png)

    Sidan **Tilldela licensuppdateringar** för att visa att en användare är markerad och att tilldelningarna är konfigurerade.

1. Välj **Tilldela**.

    Gruppen läggs till i listan över licensierade grupper och alla medlemmar har åtkomst till de inkluderade Azure AD-tjänsterna.

## <a name="remove-a-license"></a>Ta bort en licens

Du kan ta bort en licens från en användares Azure AD-användarsida, från gruppöversiktssidan för en grupptilldelning eller från sidan Azure **AD-licenser** för att se användare och grupper för en licens.

### <a name="to-remove-a-license-from-a-user"></a>Så här tar du bort en licens från en användare

1. På sidan **Licensierade användare** för serviceplanen väljer du den användare som inte längre ska ha licensen. Till exempel _Alain Charon_.

1. Välj **Ta bort licens**.

    ![Sida med licensierade användare med alternativet Ta bort licens markerat](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licenser som en användare ärver från en grupp kan inte tas bort direkt. I stället måste du ta bort användaren från den grupp som de ärver licensen från.

### <a name="to-remove-a-license-from-a-group"></a>Så här tar du bort en licens från en grupp

1. På sidan **Licensierade grupper** för licensplanen väljer du den grupp som inte längre ska ha licensen.

1. Välj **Ta bort licens**.

    ![Sidan Licensierade grupper med alternativet Ta bort licens markerat](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > När ett lokalt användarkonto som synkroniseras med Azure AD faller utanför synkroniseringen eller när synkroniseringen tas bort, tas användaren bort mjukt i Azure AD. När detta inträffar markeras licenser som tilldelats användaren direkt eller via gruppbaserad licensiering som **avstängda** i stället för **att tas bort**.

## <a name="next-steps"></a>Nästa steg

När du har tilldelat dina licenser kan du utföra följande processer:

- [Identifiera och lösa problem med licenstilldelning](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Lägga till licensierade användare i en grupp för licensiering](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenarier, begränsningar och kända problem med hjälp av grupper för att hantera licensiering i Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)
