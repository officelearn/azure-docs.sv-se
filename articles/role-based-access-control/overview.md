---
title: Vad är rollbaserad åtkomstkontroll (Role-based access control, RBAC) i Azure? | Microsoft Docs
description: Få en översikt över rollbaserad åtkomstkontroll (Role-based access control, RBAC) i Azure. Använd rolltilldelningar för att styra åtkomsten till resurser i Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a2e0bf35f73a355197f821f7cce12294f7b35576
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344757"
---
# <a name="what-is-role-based-access-control-rbac"></a>Vad är rollbaserad åtkomstkontroll (Role-based access control, RBAC)?

Åtkomsthantering för molnresurser är en viktig funktion för alla organisationer som använder molnet. Rollbaserad åtkomstkontroll (RBAC) hjälper till vid hantering av vem som ska ha åtkomst till Azure-resurser, vad de ska kunna göra med resurserna och till vilka områden de ska ha åtkomst.

RBAC är ett auktoriseringssystem som bygger på [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) som ger detaljerad åtkomsthantering av resurser i Azure.

## <a name="what-can-i-do-with-rbac"></a>Vad kan jag göra med RBAC?

Här följer några exempel på vad du kan göra med RBAC:

- Tillåta en användare att hantera virtuella datorer i en prenumeration och en annan användare att hantera virtuella nätverk
- Tillåta en DBA-grupp att hantera SQL-databaser i en prenumeration
- Tillåta en användare att hantera alla resurser i en resursgrupp, till exempel virtuella datorer, webbplatser och undernät
- Tillåta att ett program får åtkomst till alla resurser i en resursgrupp

## <a name="best-practice-for-using-rbac"></a>Metodtips för att använda RBAC

Med hjälp av RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete. I stället för att ge alla obegränsad behörighet i din Azure-prenumeration eller dina resurser kan du tillåta enbart vissa åtgärder i ett visst omfång.

När du planerar din strategi för åtkomstkontroll är det en bra idé att bevilja användare den lägsta behörighet som krävs för att de ska kunna utföra sitt arbete. I följande diagram visas ett föreslaget mönster för att använda RBAC.

![RBAC och lägsta behörighet](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>Så fungerar RBAC

Du styr åtkomst till resurser med hjälp av RBAC genom att skapa rolltilldelningar. Det här är ett viktigt begrepp – det är så här behörigheter tillämpas. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång.

### <a name="security-principal"></a>Säkerhetsobjekt

En *säkerhetsobjekt* är ett objekt som representerar en användare, en grupp eller tjänstens huvudnamn som begär åtkomst till Azure-resurser.

![Säkerhetsobjekt för en rolltilldelning](./media/overview/rbac-security-principal.png)

- Användare – en person som har en profil i Azure Active Directory. Du kan även tilldela roller till användare i andra klientorganisationer. Information om användare i andra organisationer finns i [Azure Active Directory B2B](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Grupp – en uppsättning användare som skapas i Azure Active Directory. När du tilldelar en roll till en grupp får alla användare i gruppen den rollen. 
- Tjänstens huvudnamn – en säkerhetsidentitet som används av program eller tjänster för att få åtkomst till specifika Azure-resurser. Du kan se det som en *användaridentitet* (användarnamn och lösenord eller certifikat) för ett program.

### <a name="role-definition"></a>Rolldefinition

En *rolldefinition* är en uppsättning behörigheter. Ibland kallas det helt enkelt för en *roll*. En rolldefinition listar de åtgärder som kan utföras, till exempel läsa, skriva och ta bort. Roller kan vara på hög nivå, som ägare, eller specifika, som läsare för virtuell dator.

![Rolldefinition för en rolltilldelning](./media/overview/rbac-role-definition.png)

Azure innehåller flera [inbyggda roller](built-in-roles.md) som du kan använda. Följande listar fyra grundläggande inbyggda roller. De första tre gäller för alla resurstyper.

- [Ägare](built-in-roles.md#owner) – har fullständig åtkomst till alla resurser inklusive rätten att delegera åtkomst till andra.
- [Deltagare](built-in-roles.md#contributor) – kan skapa och hantera alla typer av Azure-resurser men kan inte bevilja åtkomst för andra.
- [Läsare](built-in-roles.md#reader) – kan visa befintliga Azure-resurser.
- [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator) – gör att du kan hantera användarnas åtkomst till Azure-resurser.

Resten av de inbyggda rollerna tillåter hantering av specifika Azure-resurser. Till exempel tillåter rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) att en användare skapar och hanterar virtuella datorer. Om de inbyggda rollerna inte uppfyller organisationens specifika krav kan du skapa egna, [anpassade roller](custom-roles.md).

Azure har infört dataåtgärder (för närvarande i förhandsversion) som gör det möjligt att bevilja åtkomst till data i ett objekt. Till exempel kan en användare med dataläsningsåtkomst till ett lagringskonto läsa blobar eller meddelanden i det lagringskontot. Mer information finns i [Förstå rolldefinitioner](role-definitions.md).

### <a name="scope"></a>Omfång

*Omfång* är den gräns som åtkomsten som gäller för. När du tilldelar en roll kan du ytterligare begränsa de åtgärder som tillåts genom att definiera ett omfång. Det här är användbart om du vill göra någon till en [Webbplatsdeltagare](built-in-roles.md#website-contributor) men endast för en resursgrupp.

I Azure kan du ange ett omfång på flera nivåer: prenumeration, resursgrupp eller resurs. Omfång är strukturerade i en överordnad-underordnad-relation där varje underordnad endast har en överordnad.

![Omfång för en rolltilldelning](./media/overview/rbac-scope.png)

Åtkomst som du tilldelar på ett överordnad omfång ärvs av det underordnade omfånget. Exempel:

- Om du tilldelar rollen [Läsare](built-in-roles.md#reader) till en grupp i prenumerationsomfånget kan medlemmarna i den gruppen visa alla resursgrupper och resurser i prenumerationen.
- Om du tilldelar rollen [Deltagare](built-in-roles.md#contributor) till ett program i resursgruppomfånget kan den hantera resurser av alla typer i den resursgruppen, men inte i andra resursgrupper i prenumerationen.

Azure innehåller även ett omfång över prenumerationer som kallas [hanteringsgrupper](../azure-resource-manager/management-groups-overview.md), vilket är i förhandsversion. Hanteringsgrupper är ett sätt att hantera flera prenumerationer. När du anger omfång för RBAC kan du antingen ange en hanteringsgrupp eller ange en prenumeration, resursgrupp eller resurshierarki.

### <a name="role-assignment"></a>Rolltilldelning

En *rolltilldelning* är processen att binda en rolldefinition till en användare, grupp eller tjänstens huvudnamn i ett visst omfång för att bevilja åtkomst. Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort.

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har marknadsföringsgruppen tilldelats rollen [Deltagare](built-in-roles.md#contributor) för resursgruppen pharma-sales (läkemedelsförsäljning). Det innebär att användare i marknadsföringsgruppen kan skapa och hantera vilka Azure-resurser som helst i resursgruppen pharma-sales. Marknadsföringsanvändare har inte åtkomst till resurser utanför resursgruppen pharma-sales såvida de inte är en del av en annan rolltilldelning.

![Rolltilldelning för att kontrollera åtkomst](./media/overview/rbac-overview.png)

Du kan skapa rolltilldelningar med hjälp av Azure-portalen, Azure CLI, Azure PowerShell, Azure-SDK:er eller REST-API:er. Du kan ha upp till 2 000 rolltilldelningar i varje prenumeration. För att kunna skapa och ta bort rolltilldelningar behöver du ha `Microsoft.Authorization/roleAssignments/*`-behörighet. Den här behörigheten beviljas via rollerna [Ägare](built-in-roles.md#owner) eller [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator).

## <a name="next-steps"></a>Nästa steg

- [Snabbstart – Bevilja åtkomst för en användare med RBAC och Azure Portal](quickstart-assign-role-user-portal.md)
- [Hantera åtkomst med hjälp av RBAC och Azure-portalen](role-assignments-portal.md)
- [Förstå de olika rollerna i Azure](rbac-and-directory-admin-roles.md)
