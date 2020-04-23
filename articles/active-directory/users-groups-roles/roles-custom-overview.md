---
title: Anpassade administratörsroller i Azure Active Directory | Microsoft-dokument
description: Förhandsgranska anpassade Azure AD-roller för delegering av identitetshantering. Hantera Azure-roller i Azure-portalen, PowerShell eller Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085118"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Anpassade administratörsroller i Azure Active Directory (förhandsversion)

I den här artikeln beskrivs hur du förstår azure AD-anpassade roller i Azure Active Directory (Azure AD) med roller-baserad åtkomstkontroll och resursomfattningar. Anpassade Azure AD-roller visas underliggande behörigheter för de [inbyggda rollerna](directory-assign-admin-roles.md), så att du kan skapa och organisera dina egna roller. Med den här metoden kan du bevilja åtkomst på ett mer detaljerade sätt än inbyggda roller, när de behövs. Den här första versionen av anpassade Azure AD-roller innehåller möjligheten att skapa en roll för att tilldela behörigheter för hantering av appregistreringar. Med tiden läggs ytterligare behörigheter för organisationsresurser som företagsprogram, användare och enheter till.  

Dessutom stöder Azure AD-anpassade roller tilldelningar per resurs, utöver de mer traditionella organisationsomfattande tilldelningarna. Den här metoden ger dig möjlighet att bevilja åtkomst för att hantera vissa resurser (till exempel en appregistrering) utan att ge åtkomst till alla resurser (alla appregistreringar).

Azure AD-rollbaserad åtkomstkontroll är en offentlig förhandsversionsfunktion i Azure AD och är tillgänglig med alla betalda Azure AD-licensplan. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Förstå Azure AD-rollbaserad åtkomstkontroll

Att bevilja behörighet med hjälp av anpassade Azure AD-roller är en tvåstegsprocess som innebär att skapa en anpassad rolldefinition och sedan tilldela den med hjälp av en rolltilldelning. En anpassad rolldefinition är en samling behörigheter som du lägger till från en förinställd lista. Dessa behörigheter är samma behörigheter som används i de inbyggda rollerna.  

När du har skapat din rolldefinition kan du tilldela den till en användare genom att skapa en rolltilldelning. En rolltilldelning ger användaren behörigheterna i en rolldefinition med ett angivet scope. Med den här tvåstegsprocessen kan du skapa en enda rolldefinition och tilldela den många gånger i olika scope. Ett scope definierar uppsättningen Azure AD-resurser som rollmedlemmen har åtkomst till. Det vanligaste omfånget är organisationsomfattande (organisationsomfattande) scope. En anpassad roll kan tilldelas i organisationsomfattande omfång, vilket innebär att rollmedlemmen har rollbehörigheter över alla resurser i organisationen. En anpassad roll kan också tilldelas vid ett objektomfång. Ett exempel på ett objektomfång skulle vara ett enda program. Samma roll kan tilldelas en användare över alla program i organisationen och sedan till en annan användare med ett scope med endast contoso utgiftsrapporter app.  

Azure AD-inbyggda och anpassade roller fungerar på begrepp som liknar [Azure-rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md). [Skillnaden mellan dessa två rollbaserade åtkomstkontrollsystem](../../role-based-access-control/rbac-and-directory-admin-roles.md) är att Azure RBAC styr åtkomst till Azure-resurser som virtuella datorer eller lagring med Azure Resource Management och Azure AD-anpassade roller styr åtkomsten till Azure AD-resurser med Graph API. Båda systemen utnyttjar begreppet rolldefinitioner och rolltilldelningar.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Så här avgör Azure AD om en användare har åtkomst till en resurs

Följande är de steg på hög nivå som Azure AD använder för att avgöra om du har åtkomst till en hanteringsresurs. Använd den här informationen för att felsöka åtkomstproblem.

1. En användare (eller tjänsthuvudnamn) förvärvar en token till Slutpunkten för Microsoft Graph eller Azure AD Graph.

1. Användaren gör ett API-anrop till Azure Active Directory (Azure AD) via Microsoft Graph eller Azure AD Graph med hjälp av den utfärdade token.

1. Beroende på omständigheterna vidtar Azure AD någon av följande åtgärder:

    - Utvärderar användarens rollmedlemskap baserat på [wids-anspråket](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) i användarens åtkomsttoken.
    - Hämtar alla rolltilldelningar som gäller för användaren, antingen direkt eller via gruppmedlemskap, till den resurs som åtgärden vidtas på.

1. Azure AD avgör om åtgärden i API-anropet ingår i de roller som användaren har för den här resursen.
1. Om användaren inte har en roll med åtgärden i det begärda scopet beviljas inte åtkomst. Annars beviljas åtkomst.

### <a name="role-assignments"></a>Rolltilldelningar

En rolltilldelning är det objekt som kopplar en rolldefinition till en användare i ett visst omfång för att bevilja Azure AD-resursåtkomst. Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort. I grunden består ett rolluppdrag av tre delar:

- Användare (en person som har en användarprofil i Azure Active Directory)
- Rolldefinition
- Resursomfattning

Du kan [skapa rolltilldelningar](roles-create-custom.md) med Azure-portalen, Azure AD PowerShell eller Graph API. Du kan också [visa tilldelningar för en anpassad roll](roles-view-assignments.md#view-the-assignments-of-a-role).

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har Chris Green tilldelats den anpassade rollen för appregistreringsadministratören i omfattningen av appregistreringen Contoso Widget Builder. Tilldelningen ger Chris behörigheten för administratörsrollen För appregistrering för endast den här specifika appregistreringen.

![Rolltilldelning är hur behörigheter tillämpas och har tre delar](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Säkerhetsobjekt

Ett säkerhetsobjekt representerar den användare som ska tilldelas åtkomst till Azure AD-resurser. En *användare* är en person som har en användarprofil i Azure Active Directory.

### <a name="role"></a>Roll

En rolldefinition, eller roll, är en samling behörigheter. En rolldefinition visar de åtgärder som kan utföras på Azure AD-resurser, till exempel skapa, läsa, uppdatera och ta bort. Det finns två typer av roller i Azure AD:

- Inbyggda roller som skapats av Microsoft och som inte kan ändras.
- Anpassade roller som skapats och hanterats av din organisation.

### <a name="scope"></a>Omfång

Ett scope är begränsningen av tillåtna åtgärder till en viss Azure AD-resurs som en del av en rolltilldelning. När du tilldelar en roll kan du ange ett scope som begränsar administratörens åtkomst till en viss resurs. Om du till exempel vill ge en utvecklare en anpassad roll, men bara för att hantera en viss programregistrering, kan du inkludera den specifika programregistreringen som ett scope i rolltilldelningen.

  > [!Note]
  > Anpassade roller kan tilldelas i katalogomfattning och resursomfattning. De kan ännu inte tilldelas på den administrativa enhetens omfattning.
  > Inbyggda roller kan tilldelas i katalogomfattning och i vissa fall administrativ enhetsomfång. De kan ännu inte tilldelas på Azure AD-resursomfattning.

## <a name="required-license-plan"></a>Obligatorisk licensplan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Skapa anpassade rolltilldelningar med [Azure-portalen, Azure AD PowerShell och Graph API](roles-create-custom.md)
- [Visa tilldelningar för en anpassad roll](roles-view-assignments.md#view-assignments-of-single-application-scope)
