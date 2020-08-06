---
title: Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?
description: Få en översikt över rollbaserad åtkomst kontroll i Azure (Azure RBAC). Använd rolltilldelningar för att styra åtkomsten till resurser i Azure.
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
ms.date: 07/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: azuread-video-2020
ms.openlocfilehash: cb77bfb6173e94ea3cdaadf4456947de75676565
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87761135"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Vad är rollbaserad åtkomstkontroll i Azure (Azure RBAC)?

Åtkomsthantering för molnresurser är en viktig funktion för alla organisationer som använder molnet. Rollbaserad åtkomst kontroll i Azure (Azure RBAC) hjälper dig att hantera vem som har åtkomst till Azure-resurser, vad de kan göra med dessa resurser och vilka områden de har åtkomst till.

Azure RBAC är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) som ger detaljerad åtkomst hantering av Azure-resurser.

Den här videon ger en snabb översikt över Azure RBAC.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>Vad kan jag göra med Azure RBAC?

Här följer några exempel på vad du kan göra med Azure RBAC:

- Tillåt en användare att hantera virtuella datorer i en prenumeration och en annan användare att hantera virtuella nätverk
- Tillåta en DBA-grupp att hantera SQL-databaser i en prenumeration
- Tillåt en användare att hantera alla resurser i en resursgrupp, till exempel virtuella datorer, webbplatser och undernät
- Tillåt att ett program får åtkomst till alla resurser i en resursgrupp

## <a name="how-azure-rbac-works"></a>Så här fungerar Azure RBAC

Hur du styr åtkomsten till resurser med hjälp av Azure RBAC är att skapa roll tilldelningar. Det här är ett viktigt begrepp att förstå – det är hur behörigheter tillämpas. En rolltilldelning består av tre delar: säkerhetsobjekt, rolldefinition och omfång.

### <a name="security-principal"></a>Säkerhetsobjekt

En *säkerhetsobjekt* är ett objekt som representerar en användare, en grupp, tjänstens huvudnamn eller hanterad identitet som begär åtkomst till Azure-resurser.

![Säkerhetsobjekt för en rolltilldelning](./media/overview/rbac-security-principal.png)

- Användare – en person som har en profil i Azure Active Directory. Du kan även tilldela roller till användare i andra klientorganisationer. Information om användare i andra organisationer finns i [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Grupp – en uppsättning användare som skapas i Azure Active Directory. När du tilldelar en roll till en grupp får alla användare i gruppen den rollen. 
- Tjänstens huvudnamn – en säkerhetsidentitet som används av program eller tjänster för att få åtkomst till specifika Azure-resurser. Du kan se det som en *användaridentitet* (användarnamn och lösenord eller certifikat) för ett program.
- Hanterad identitet – en identitet i Azure Active Directory som hanteras automatiskt av Azure. Normalt använder du [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) när du utvecklar molnbaserade program för att hantera uppgifterna för autentisering i Azure-tjänster.

### <a name="role-definition"></a>Rolldefinition

En *rolldefinition* är en samling behörigheter. Den brukar bara kallas en *roll*. En rolldefinition listar de åtgärder som kan utföras, till exempel läsa, skriva och ta bort. Roller kan vara på hög nivå, som ägare, eller specifika, som läsare för virtuell dator.

![Rolldefinition för en rolltilldelning](./media/overview/rbac-role-definition.png)

Azure innehåller flera [inbyggda roller](built-in-roles.md) som du kan använda. Följande listar fyra grundläggande inbyggda roller. De första tre gäller för alla resurstyper.

- [Owner](built-in-roles.md#owner) -har fullständig åtkomst till alla resurser, inklusive rätten att delegera åtkomst till andra.
- [Deltagare](built-in-roles.md#contributor) – kan skapa och hantera alla typer av Azure-resurser, men kan inte bevilja åtkomst till andra.
- [Reader](built-in-roles.md#reader) – kan visa befintliga Azure-resurser.
- [Administratör för användar åtkomst](built-in-roles.md#user-access-administrator) – låter dig hantera användar åtkomst till Azure-resurser.

Resten av de inbyggda rollerna tillåter hantering av specifika Azure-resurser. Till exempel tillåter rollen [Virtuell datordeltagare](built-in-roles.md#virtual-machine-contributor) att en användare skapar och hanterar virtuella datorer. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa egna [Azure-anpassade roller](custom-roles.md).

Den här videon ger en snabb översikt över inbyggda roller och anpassade roller.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

Azure har data åtgärder som gör att du kan bevilja åtkomst till data i ett objekt. Till exempel kan en användare med dataläsningsåtkomst till ett lagringskonto läsa blobar eller meddelanden i det lagringskontot. Mer information finns i [förstå definitioner av Azure-roller](role-definitions.md).

### <a name="scope"></a>Omfång

*Omfång* är den uppsättning resurser som åtkomsten som gäller för. När du tilldelar en roll kan du ytterligare begränsa de åtgärder som tillåts genom att definiera ett omfång. Detta är användbart om du vill göra någon till en [webbplats deltagare](built-in-roles.md#website-contributor), men bara för en resurs grupp.

I Azure kan du ange ett omfång på flera nivåer: [hanterings grupp](../governance/management-groups/overview.md), prenumeration, resurs grupp eller resurs. Omfång är strukturerade i en överordnad/underordnad relation.

![Omfång för en rolltilldelning](./media/overview/rbac-scope.png)

När du beviljar åtkomst i ett överordnat omfång ärvs dessa behörigheter av underordnade omfång. Exempel:

- Om du tilldelar rollen [Ägare](built-in-roles.md#owner) till en användare i hanteringsgruppsomfånget kan den användaren hantera allt i alla prenumerationer i hanteringsgruppen.
- Om du tilldelar rollen [Läsare](built-in-roles.md#reader) till en grupp i prenumerationsomfånget kan medlemmarna i den gruppen visa alla resursgrupper och resurser i prenumerationen.
- Om du tilldelar rollen [Deltagare](built-in-roles.md#contributor) till ett program i resursgruppomfånget kan den hantera resurser av alla typer i den resursgruppen, men inte i andra resursgrupper i prenumerationen.

### <a name="role-assignments"></a>Rolltilldelningar

En *rolltilldelning* är processen att koppla en rolldefinition till en användare, grupp, tjänstens huvudnamn eller hanterad identitet i ett visst omfång för att bevilja åtkomst. Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort.

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har marknadsföringsgruppen tilldelats rollen [Deltagare](built-in-roles.md#contributor) för resursgruppen pharma-sales (läkemedelsförsäljning). Det innebär att användare i marknadsföringsgruppen kan skapa och hantera vilka Azure-resurser som helst i resursgruppen pharma-sales. Marknadsföringsanvändare har inte åtkomst till resurser utanför resursgruppen pharma-sales såvida de inte är en del av en annan rolltilldelning.

![Rolltilldelning för att kontrollera åtkomst](./media/overview/rbac-overview.png)

Du kan skapa rolltilldelningar med hjälp av Azure-portalen, Azure CLI, Azure PowerShell, Azure-SDK:er eller REST-API:er. Du kan ha upp till **2000** roll tilldelningar i varje prenumeration. Den här gränsen omfattar roll tilldelningar i prenumerations-, resurs grupps-och resurs omfång. Du kan ha upp till **500** roll tilldelningar i varje hanterings grupp. För att kunna skapa och ta bort rolltilldelningar behöver du ha `Microsoft.Authorization/roleAssignments/*`-behörighet. Den här behörigheten beviljas via rollerna [Ägare](built-in-roles.md#owner) eller [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator).

## <a name="multiple-role-assignments"></a>Flera rolltilldelningar

Så vad händer om du har flera överlappande rolltilldelningar? Azure RBAC är en additiv modell, så dina effektiva behörigheter är summan av dina roll tilldelningar. Fundera på följande exempel där en användare beviljas deltagarrollen i prenumerationsomfånget och läsarrollen på en resursgrupp. Summan av deltagar behörigheterna och läsar behörigheten är effektivt i rollen deltagare för resurs gruppen. Därför har läsarrolltilldelningen ingen inverkan i det här fallet.

![Flera rolltilldelningar](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Avvisa tilldelning

Tidigare var Azure RBAC en modell utan neka, men nu stöder Azure RBAC neka-tilldelningar på ett begränsat sätt. På samma sätt som en rolltilldelning kopplar ett *tilldelningsnekande* en uppsättning nekandeåtgärder till en användare, grupp, tjänstens huvudnamn eller hanterad identitet i ett visst omfång för att neka åtkomst. En rolltilldelning definierar en uppsättning åtgärder som är *tillåtna*, medan en nekandetilldelning definierar en uppsättning åtgärder som *inte är tillåtna*. Med andra ord blockerar tilldelningsnekanden användare från att utföra angivna åtgärder, även om en rolltilldelning ger dem åtkomst. Tilldelningsnekanden åsidosätter rolltilldelningar. Mer information finns i [förstå Azure Deny-tilldelningar](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Hur Azure RBAC bestämmer om en användare har åtkomst till en resurs

Följande är de övergripande steg som Azure RBAC använder för att avgöra om du har åtkomst till en resurs i hanterings planet. Det här är bra att förstå om du vill felsöka ett problem med åtkomst.

1. En användare (eller tjänstens huvudnamn) hämtar en token för Azure Resource Manager.

    Token innehåller användarens gruppmedlemskap (inklusive transitiva gruppmedlemskap).

1. Användaren gör ett REST API-anrop till Azure Resource Manager med den token som är ansluten.

1. Azure Resource Manager hämtar alla rolltilldelningar och avvisar tilldelningar som tillämpas på resursen som åtgärden som utförs på.

1. Azure Resource Manager begränsar rolltilldelningarna som gäller för den här användaren eller gruppen och avgör vilka roller som användaren har för den här resursen.

1. Azure Resource Manager anger om åtgärden i API-anropet ingår i de roller som användaren har för den här resursen.

1. Om användaren inte har någon roll med åtgärden i det begärda omfånget beviljas inte åtkomst. I annat fall kontrollerar Azure Resource Manager om det gäller ett tilldelningsnekande.

1. Om det gäller ett tilldelningsnekanden blockeras åtkomsten. Annars beviljas åtkomst.

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort roll tilldelningar i Azure med hjälp av Azure Portal](role-assignments-portal.md)
- [Förstå de olika rollerna](rbac-and-directory-admin-roles.md)
- [Ramverk för moln införande: resurs åtkomst hantering i Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
