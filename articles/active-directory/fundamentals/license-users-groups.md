---
title: Tilldela eller ta bort licenser – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tilldelar eller tar bort Azure Active Directory licenser från dina användare eller grupper.
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
ms.openlocfilehash: 9f690d23ac4d8c452d731c2f6fd4ae67dc281639
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805529"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Tilldela eller ta bort licenser i Azure Active Directorys portalen

Många Azure Active Directory (Azure AD)-tjänster kräver att du licensierar var och en av dina användare eller grupper (och associerade medlemmar) för den tjänsten. Endast användare med aktiva licenser kommer att kunna komma åt och använda de licensierade Azure AD-tjänster som är sanna.

## <a name="available-license-plans"></a>Tillgängliga licens planer

Det finns flera tillgängliga licens planer för Azure AD-tjänsten, inklusive:

- Azure AD Kostnadsfri

- Azure AD Premium P1

- Azure AD Premium P2

För detaljerad information om varje licens plan och tillhör ande licens information, se [vilken licens du behöver?](https://azure.microsoft.com/pricing/details/active-directory/).

Alla Microsoft-tjänster är inte tillgängliga på alla platser. Innan du kan tilldela en licens till en grupp måste du ange **användnings platsen** för alla medlemmar. Du kan ange det här värdet i **Azure Active Directory &gt; användare &gt; profil &gt; inställnings** områden i Azure AD. Alla användare vars användnings plats inte har angetts ärver platsen för Azure AD-organisationen.

## <a name="view-license-plans-and-plan-details"></a>Visa licens planer och information om plan

Du kan visa dina tillgängliga Service planer, inklusive de enskilda licenserna, kontrol lera väntande förfallo datum och Visa antalet tillgängliga tilldelningar.

### <a name="to-find-your-service-plan-and-plan-details"></a>Hitta din service plan och planera information

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett licens administratörs konto i din Azure AD-organisation.

1. Välj **Azure Active Directory**och välj sedan **licenser**.

    ![Sidan licenser, med antal köpta tjänster och tilldelade licenser](media/license-users-groups/license-details-blade.png)

1. Välj den **inköpta** länken för att visa sidan **produkter** och för att se de **tilldelade**, **tillgängliga**och **sista giltighets** siffrorna för dina licens planer.

    ![Sidan tjänster med service licens planer och tillhör ande licens information](media/license-users-groups/license-products-blade-with-products.png)

1. Välj ett schema namn om du vill visa dess licensierade användare och grupper.

## <a name="assign-licenses-to-users-or-groups"></a>Tilldela licenser till användare eller grupper

Se till att alla som behöver använda en licensierad Azure AD-tjänst har rätt licens. Du kan lägga till licens rättigheterna till användare eller till en hel grupp.

### <a name="to-assign-a-license-to-a-user"></a>Tilldela en licens till en användare

1. På sidan **produkter** väljer du namnet på den licens plan som du vill tilldela användaren.

    ![Sidan tjänster med en markerad service licens plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. På översikts sidan licens plan väljer du **tilldela**.

    ![Sidan tjänster med markerat alternativet tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. På sidan **tilldela** väljer du **användare och grupper**och sedan söker du efter och väljer den användare som du tilldelar licensen.

    ![Sidan Tilldela licens, med markerad sökning och välj alternativ](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Välj **tilldelnings alternativ**, se till att du har rätt licens alternativ aktiverat och välj sedan **OK**.

    ![Licens alternativ sidan med alla alternativ som är tillgängliga i licens planen](media/license-users-groups/license-option-blade-assignments.png)

    Sidan **tilldela licens** uppdateringar som visar att en användare har marker ATS och att tilldelningarna har kon figurer ATS.

    > [!NOTE]
    > Alla Microsoft-tjänster är inte tillgängliga på alla platser. Innan du kan tilldela en licens till en användare måste du ange **användnings platsen**. Du kan ange det här värdet i **Azure Active Directory &gt; användare &gt; profil &gt; inställnings** områden i Azure AD. Alla användare vars användnings plats inte har angetts ärver platsen för Azure AD-organisationen.

1. Välj **Tilldela**.

    Användaren läggs till i listan över licensierade användare och har till gång till de Azure AD-tjänster som ingår.
    > [!NOTE]
    > Licenser kan också tilldelas direkt till en användare från användarens **licens** sida. Om en användare har en licens tilldelad via ett grupp medlemskap och du vill tilldela samma licens till användaren direkt, kan du bara göra det från sidan **produkter** som nämns i steg 1.

### <a name="to-assign-a-license-to-a-group"></a>Tilldela en licens till en grupp

1. På sidan **produkter** väljer du namnet på den licens plan som du vill tilldela användaren.

    ![Bladet produkter med en markerad produkt licens plan](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. På sidan **Azure Active Directory Premium plan 2** väljer du **tilldela**.

    ![Sidan produkter med markerat alternativet tilldela](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. På sidan **tilldela** väljer du **användare och grupper**och sedan söker du efter och väljer den grupp som du vill tilldela licensen.

    ![Sidan Tilldela licens, med markerad sökning och välj alternativ](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Välj **tilldelnings alternativ**, se till att du har rätt licens alternativ aktiverat och välj sedan **OK**.

    ![Licens alternativ sidan med alla alternativ som är tillgängliga i licens planen](media/license-users-groups/license-option-blade-group-assignments.png)

    Sidan **tilldela licens** uppdateringar som visar att en användare har marker ATS och att tilldelningarna har kon figurer ATS.

1. Välj **Tilldela**.

    Gruppen läggs till i listan över licensierade grupper och alla medlemmar har åtkomst till de Azure AD-tjänster som ingår.

## <a name="remove-a-license"></a>Ta bort en licens

Du kan ta bort en licens från en användares Azure AD-användare på sidan grupp översikt för en grupp tilldelning eller genom att starta från sidan för Azure AD- **licenser** och se användare och grupper för en licens.

### <a name="to-remove-a-license-from-a-user"></a>Så här tar du bort en licens från en användare

1. På sidan **licensierade användare** för service planen väljer du den användare som inte längre ska ha licensen. Till exempel _Alain Charon_.

1. Välj **ta bort licens**.

    ![Sidan licensierade användare med alternativet ta bort licens markerad](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Det går inte att ta bort licenser som en användare ärver från en grupp direkt. I stället måste du ta bort användaren från gruppen som de ärver licensen från.

### <a name="to-remove-a-license-from-a-group"></a>Ta bort en licens från en grupp

1. Välj den grupp som inte längre ska ha licensen på sidan **licensierade grupper** för licens planen.

1. Välj **ta bort licens**.

    ![Sidan licensierade grupper med alternativet ta bort licens markerat](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

## <a name="next-steps"></a>Nästa steg

När du har tilldelat dina licenser kan du utföra följande processer:

- [Identifiera och lösa problem med licens tilldelning](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Lägga till licensierade användare i en grupp för licensiering](../users-groups-roles/licensing-groups-migrate-users.md)

- [Scenarier, begränsningar och kända problem med grupper för att hantera licensiering i Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Lägga till eller ändra profil information](active-directory-users-profile-azure-portal.md)
