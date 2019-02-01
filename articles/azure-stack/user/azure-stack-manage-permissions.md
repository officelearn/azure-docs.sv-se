---
title: Hantera behörigheter till resurser per användare i Azure Stack | Microsoft Docs
description: Lär dig hantera RBAC-behörigheter som en tjänstadministratör eller klient.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 03c1879c34a00bb09b5a4dee6af7da7b984045fc
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241193"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Hantera åtkomst till resurser med Azure Stack Role-Based Access Control

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack stöd för rollbaserad åtkomstkontroll (RBAC), samma [säkerhetsmodell för åtkomsthantering](https://docs.microsoft.com/azure/role-based-access-control/overview) som använder Microsoft Azure. Du kan använda RBAC för att hantera användare, grupp eller programåtkomst för prenumerationer, resurser och tjänster.

## <a name="basics-of-access-management"></a>Grunderna för åtkomsthantering

Rollbaserad åtkomstkontroll ger detaljerad åtkomstkontroll som du kan använda för att skydda din miljö. Du ger användarna de exakta behörigheter de behöver genom att tilldela RBAC-roll för ett visst omfång. Omfånget för rolltilldelningen kan vara en prenumeration, en resursgrupp eller en enskild resurs. Läs den [rollbaserad åtkomstkontroll i Azure-portalen](https://docs.microsoft.com/azure/role-based-access-control/overview) artikeln för att få mer detaljerad information om åtkomsthantering.

### <a name="built-in-roles"></a>Inbyggda roller

Azure Stack har tre grundläggande roller som du kan använda för alla typer av resurser:

* **Ägare** kan hantera allt, inklusive åtkomst till resurser.
* **Deltagare** kan hantera allt förutom åtkomst till resurser.
* **Läsare** kan visa allt, men inte göra några ändringar.

### <a name="resource-hierarchy-and-inheritance"></a>Resurs-hierarkin och arv

Azure Stack har följande resurs hierarki:

* Varje prenumeration hör till en katalog.
* Varje resursgrupp tillhör en prenumeration.
* Varje resurs tillhör en resursgrupp.

Åtkomst som du beviljar på en överordnad omfattning ärvs på underordnade omfång. Exempel:

* Du tilldelar rollen läsare till en Azure AD-grupp prenumerationsområde. Medlemmar i gruppen kan visa varje resursgrupp och resurser i prenumerationen.
* Du kan tilldela rollen som deltagare till ett program i resursgruppomfånget. Programmet kan hantera resurser av alla typer i resursgruppen, men inte andra resursgrupper i prenumerationen.

### <a name="assigning-roles"></a>Tilldela roller

Du kan tilldela mer än en roll till en användare och varje roll kan associeras med ett annat omfång. Exempel:

* Du kan tilldela TestUser-A läsarrollen till prenumeration 1.
* Du tilldelar TestUser-A ägarrollen TestVM-1.

Azure [rolltilldelningar](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artikeln innehåller detaljerad information om att visa, tilldela och ta bort roller.

### <a name="resource-hierarchy-and-inheritance"></a>Resurs-hierarkin och arv

Azure Stack har följande resurs hierarki:

* Varje prenumeration hör till en katalog.
* Varje resursgrupp tillhör en prenumeration.
* Varje resurs tillhör en resursgrupp.

Åtkomst som du beviljar på en överordnad omfattning ärvs på underordnade omfång. Exempel:

* Du tilldelar den **läsare** rollen till en Azure AD-grupp prenumerationsområde. Medlemmar i gruppen kan visa varje resursgrupp och resurser i prenumerationen.
* Du tilldelar den **deltagare** rollen till ett program i resursgruppomfånget. Programmet kan hantera resurser av alla typer i resursgruppen, men inte andra resursgrupper i prenumerationen.

### <a name="assigning-roles"></a>Tilldela roller

Du kan tilldela mer än en roll till en användare och varje roll kan associeras med ett annat omfång. Exempel:

* Du kan tilldela TestUser-A läsarrollen till prenumeration 1.
* Du tilldelar TestUser-A ägarrollen TestVM-1.

Azure [rolltilldelningar](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) artikeln innehåller detaljerad information om att visa, tilldela och ta bort roller.

## <a name="set-access-permissions-for-a-user"></a>Ange åtkomstbehörighet för en användare

Följande steg beskriver hur du konfigurerar behörigheter för en användare.

1. Logga in med ett konto som har ägarbehörighet för den resurs du vill hantera.
2. Välj **Resursgrupper** i det vänstra navigeringsfönstret.
3. Välj namnet på resursgruppen som du vill ange behörigheter för.
4. I navigeringsfönstret resource group välja **åtkomstkontroll (IAM)**. Den **rolltilldelningar** vyn visar de objekt som har åtkomst till resursgruppen. Du kan filtrera och gruppera resultatet.
5. På den **åtkomstkontroll** menyn stapeln, Välj **Lägg till rolltilldelning**.
6. På **Lägg till rolltilldelning** fönstret:

   * Välj den roll som du vill tilldela från den **rollen** listrutan.
   * Välj den resurs som du vill tilldela från den **tilldela åtkomst till** listrutan.
   * Välj den användare, den grupp eller det program i katalogen som du vill bevilja åtkomst till. Du kan söka i katalogen med visningsnamn, e-postadresser och objektidentifierare.

7. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

[Skapa tjänsthuvudnamn](azure-stack-create-service-principals.md)