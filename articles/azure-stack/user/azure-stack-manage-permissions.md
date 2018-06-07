---
title: Hantera behörigheter till resurser per användare i Azure-stacken | Microsoft Docs
description: Lär dig hur du hantera RBAC behörigheter som en tjänstadministratör eller innehavare.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 026c686b1d5654aa50dd63b9addd619dd5322da0
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808153"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Hantera åtkomst till resurser med Azure Stack Role-Based åtkomstkontroll

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure stacken stöder rollbaserad åtkomstkontroll (RBAC), samma [säkerhetsmodell för access management](https://docs.microsoft.com/azure/role-based-access-control/overview) som använder Microsoft Azure. Du kan använda RBAC för att hantera användare, grupp eller programåtkomst för prenumerationer, resurser och tjänster.

## <a name="basics-of-access-management"></a>Grunderna i åtkomsthantering

Rollbaserad åtkomstkontroll ger detaljerad åtkomstkontroll som du kan använda för att skydda din miljö. Ge användare behörigheterna exakt som de behöver genom att tilldela rollen RBAC för ett visst område. Omfånget för rolltilldelningen kan vara en prenumeration, resursgrupp eller en enskild resurs. Läs den [rollbaserad åtkomstkontroll i Azure portal](https://docs.microsoft.com/azure/role-based-access-control/overview) artikeln för att få mer detaljerad information om åtkomsthantering av.

### <a name="built-in-roles"></a>Inbyggda roller

Azure-stacken har tre grundläggande roller som du kan använda för alla typer av resurser:

* **Ägare** kan hantera allt, inklusive åtkomst till resurser.
* **Deltagare** kan hantera allt utom åtkomst till resurser.
* **Läsaren** kan visa allt, men inte göra några ändringar.

### <a name="resource-hierarchy-and-inheritance"></a>Resurs-hierarkin och arv

Azure-stacken har följande resurs-hierarki:

* Varje prenumeration som hör till en katalog.
* Varje resursgrupp som hör till en prenumeration.
* Varje resurs tillhör en resursgrupp.

Åtkomst som du har gett på en överordnad omfattning ärvs på underordnade omfattningar. Exempel:

* Du kan tilldela rollen Läsare för en Azure AD-gruppen på prenumerationsomfattningen. Medlemmar i gruppen kan visa varje resursgrupp och resurser i prenumerationen.
* Du tilldelar deltagarrollen till ett program på resursen Gruppomfång. Programmet kan hantera resurser av alla typer i resursgruppen, men inte andra resursgrupper i prenumerationen.

### <a name="assigning-roles"></a>tilldela roller

Du kan tilldela fler än en roll till en användare och varje roll kan vara associerat med ett annat omfång. Exempel:

* Du tilldelar rollen TestUser-A i läsare prenumeration-1.
* Du tilldelar TestUser-A ägarrollen TestVM-1.

Azure [rolltilldelningar](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artikeln innehåller detaljerad information om Visa, tilldela och ta bort roller.

### <a name="resource-hierarchy-and-inheritance"></a>Resurs-hierarkin och arv

Azure-stacken har följande resurs-hierarki:

* Varje prenumeration som hör till en katalog.
* Varje resursgrupp som hör till en prenumeration.
* Varje resurs tillhör en resursgrupp.

Åtkomst som du har gett på en överordnad omfattning ärvs på underordnade omfattningar. Exempel:

* Du tilldelar den **Reader** rollen till en Azure AD-grupp i omfånget för prenumerationen. Medlemmar i gruppen kan visa varje resursgrupp och resurser i prenumerationen.
* Du tilldelar den **deltagare** roll till ett program på resursen Gruppomfång. Programmet kan hantera resurser av alla typer i resursgruppen, men inte andra resursgrupper i prenumerationen.

### <a name="assigning-roles"></a>tilldela roller

Du kan tilldela fler än en roll till en användare och varje roll kan vara associerat med ett annat omfång. Exempel:

* Du tilldelar rollen TestUser-A i läsare prenumeration-1.
* Du tilldelar TestUser-A ägarrollen TestVM-1.

Azure [rolltilldelningar](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artikeln innehåller detaljerad information om Visa, tilldela och ta bort roller.

## <a name="set-access-permissions-for-a-user"></a>Ange åtkomstbehörighet för en användare

Följande steg beskriver hur du konfigurerar behörigheter för en användare.

1. Logga in med ett konto som har ägarbehörighet för till resursen som du vill hantera.
2. Välj **Resursgrupper** i det vänstra navigeringsfönstret.
3. Välj namnet på resursgruppen som du vill ange behörigheter för.
4. I navigeringsfönstret resurs grupp väljer **åtkomstkontroll (IAM)**. Den **åtkomstkontroll** vyn visar de objekt som har åtkomst till resursgruppen. Du kan filtrera resultaten och använder en menyrad att lägga till eller ta bort behörigheter.
5. På den **åtkomstkontroll** menyn menyraden, Välj **+ Lägg till**.
6. På **lägga till behörigheter**:

   * Välj den roll som du vill tilldela från den **rollen** listrutan.
   * Välj den resurs som du vill tilldela från den **bevilja åtkomst till** listrutan.
   * Välj den användare, den grupp eller det program i katalogen som du vill bevilja åtkomst till. Du kan söka i katalogen med visningsnamn, e-postadresser och objektidentifierare.

7. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Skapa tjänsthuvudnamn](azure-stack-create-service-principals.md)