---
title: Tilldela eller ta bort licenser – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tilldelar eller ta bort Azure Active Directory-licenser från användare eller grupper.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 0d3f417b5a169b3d8d91d85b5f47496064c8f5f3
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063360"
---
# <a name="assign-or-remove-licenses-using-the-azure-active-directory-portal"></a>Tilldela eller ta bort licenser med hjälp av Azure Active Directory-portalen
Många Azure Active Directory (Azure AD) tjänster måste du aktivera Azure AD-produkt och licensiera var och en av dina användare eller grupper (och associerade medlemmar) för produkten. Endast användare med aktiva licenser kommer att kunna komma åt och använda den licensierade Azure AD-tjänster.

## <a name="available-product-editions"></a>Tillgängliga produkten utgåvor
Det finns flera versioner som är tillgängliga för Azure AD-produkt.

- Azure AD Kostnadsfri

- Azure AD Basic

- Azure AD Premium 1 (Azure AD P1)

- Azure AD Premium 2 (Azure AD P2)

Specifik information om varje produkt-version och tillhörande licensiering information finns i [vilken licens behöver jag?](../authentication/concept-sspr-licensing.md).

## <a name="view-your-product-edition-and-license-details"></a>Visa din Produktinformation för version och licens
Du kan visa dina tillgängliga produkter, inklusive enskilda licenser söker efter alla väntande förfallodatum och antal tilldelningar som är tillgängliga.

### <a name="to-find-your-product-and-license-details"></a>Dina produkter och licenser information
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **licenser**.

    Den **licenser** visas.

    ![Licenser sidan som visar antalet köpta produkter och tilldelade licenser](media/license-users-groups/license-details-blade.png)
    
3. Välj den **köpta produkter** länken för att visa den **produkter** sidan och se den **tilldelad**, **tillgänglig**, och  **Upphör snart att gälla** information för varje specifik produkt-version.

    ![Sidan produkter med produkten utgåvor och associerade licensinformation](media/license-users-groups/license-products-blade-with-products.png)

4. Välj ett produktnamn edition att se dess licensierade användare och grupper.

## <a name="assign-licenses-to-users-or-groups"></a>Tilldela licenser till användare eller grupper
Se till att vem som helst att behöva använda en licensierad Azure AD-tjänsten har rätt licens. Det är upp till dig om du vill lägga till licensrättigheter för enskilda användare eller för hela gruppen.

>[!Note]
>Gruppbaserad licensiering är en funktion som offentlig förhandsversion av Azure AD och är tillgängliga med något betalda Azure AD-licensplan. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).<br><br>Detaljerad information om hur du lägger till användare finns i [lägga till eller ta bort användare i Azure Active Directory](add-users-azure-active-directory.md). Detaljerad information om hur du skapar grupper och lägga till medlemmar finns i [skapa en basgrupp och lägga till medlemmar](active-directory-groups-create-azure-portal.md).

### <a name="to-assign-a-license-to-a-specific-user"></a>Tilldela en licens till en specifik användare
1. På den **produkter** markerar du namnet på den version som du vill tilldela till användaren. Till exempel _Azure Active Directory Premium Plan 2_.

    ![Sidan produkter med markerade produkt-versionen](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. På den **Azure Active Directory Premium Plan 2** väljer **tilldela**.

    ![Sidan produkter med markerade alternativet för att tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. På den **tilldela** väljer **användare och grupper**, och sök sedan efter och välj den användare som du tilldelar licensen. Till exempel _Mary Parker_.

    ![Tilldela licens sidan med markerade Sök och välj alternativ](media/license-users-groups/assign-license-blade-with-highlight.png)

4. Välj **tilldelningsalternativ**, kontrollera att du har rätt licens alternativen har aktiverats och välj sedan **OK**.

    ![Licens alternativet sidan som visar alla alternativ som är tillgänglig i versionen](media/license-users-groups/license-option-blade-assignments.png)

    Den **tilldela licens** sidan uppdateringar att visa att en användare har valts och att tilldelningarna har konfigurerats.

    >[!NOTE]
    >Inte alla Microsoft-tjänster är tillgängliga på alla platser. Innan en användare kan tilldelas en licens, måste du ange den **användningsplats**. Du kan ange ett värde den **Azure Active Directory &gt; användare &gt; profil &gt; inställningar** område i Azure AD.

5. Välj **Tilldela**.

    Användaren har lagts till i listan över licensierade användare och har åtkomst till den medföljande Azure AD-tjänster.

### <a name="to-assign-a-license-to-an-entire-group"></a>Tilldela en licens till en hel grupp
1. På den **produkter** markerar du namnet på den version som du vill tilldela till användaren. Till exempel _Azure Active Directory Premium Plan 2_.

    ![Produkter bladet med markerade produkt-versionen](media/license-users-groups/license-products-blade-with-product-highlight.png)

2. På den **Azure Active Directory Premium Plan 2** väljer **tilldela**.

    ![Sidan produkter med markerade alternativet för att tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

3. På den **tilldela** väljer **användare och grupper**, och sök sedan efter och välj den grupp som du tilldelar licensen. Till exempel _MDM princip – västra_.

    ![Tilldela licens sidan med markerade Sök och välj alternativ](media/license-users-groups/assign-group-license-blade-with-highlight.png)

4. Välj **tilldelningsalternativ**, kontrollera att du har rätt licens alternativen har aktiverats och välj sedan **OK**.

    ![Licens alternativet sidan som visar alla alternativ som är tillgänglig i versionen](media/license-users-groups/license-option-blade-group-assignments.png)

    Den **tilldela licens** sidan uppdateringar att visa att en användare har valts och att tilldelningarna har konfigurerats.

    >[!NOTE]
    >Inte alla Microsoft-tjänster är tillgängliga på alla platser. Innan en licens kan tilldelas till en grupp, måste du ange den **användningsplats** för alla medlemmar. Du kan ange ett värde den **Azure Active Directory &gt; användare &gt; profil &gt; inställningar** område i Azure AD. Alla användare vars användningsplats anges ärver platsen för klienten.

5. Välj **Tilldela**.

    Gruppen har lagts till i listan över licensierade grupper och alla medlemmar har åtkomst till den medföljande Azure AD-tjänster.


## <a name="remove-a-license"></a>Ta bort en licens
Du kan ta bort en licens från en användare eller grupp från den **licenser** sidan.

### <a name="to-remove-a-license-from-a-specific-user"></a>Ta bort en licens från en viss användare
1. På den **licensierade användare** för versionen av produkten, Välj den användare som inte längre ska ha licensen. Till exempel _Alain Charon_.

2. Välj **ta bort licens**.

    ![Licensierade användare sida med ta bort licens alternativet är markerat](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

### <a name="to-remove-a-license-from-a-group"></a>Ta bort en licens från en grupp
1. På den **licensierade grupper** för versionen av produkten, Välj den grupp som inte längre ska ha licensen. Till exempel _MDM princip – västra_.

2. Välj **ta bort licens**.

    ![Licensierade grupper sida med ta bort licens alternativet är markerat](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

>[!Important]
>Licenser som ärvs av en användare från en grupp kan inte tas bort direkt. Du behöver ta bort användaren från gruppen som de ärver licensen.

## <a name="next-steps"></a>Nästa steg
När du har tilldelat dina licenser, kan du utföra följande processer:

- [Identifiera och lösa licensproblem för tilldelning](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Lägg till licensierade användare till en grupp för licensiering](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenarier, begränsningar och kända problem med hjälp av grupper för att hantera licensiering i Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)
