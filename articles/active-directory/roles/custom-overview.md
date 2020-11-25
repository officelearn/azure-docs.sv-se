---
title: Anpassade administratörs roller i Azure Active Directory | Microsoft Docs
description: Lär dig att förstå anpassade Azure AD-roller i Azure Active Directory (Azure AD) med rollbaserad åtkomst kontroll och resurs omfång.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0785d8070a60ae7594ea0b182a0238bf6b4b6a58
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95899470"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Anpassade administratörs roller i Azure Active Directory (för hands version)

Den här artikeln beskriver hur du förstår anpassade Azure AD-roller i Azure Active Directory (Azure AD) med rollbaserad åtkomst kontroll och resurs omfång. Anpassade Azure AD-roller visar de underliggande behörigheterna för de [inbyggda rollerna](permissions-reference.md), så att du kan skapa och ordna dina egna anpassade roller. Med den här metoden kan du ge åtkomst på ett mer detaljerat sätt än inbyggda roller, när de behövs. I den första versionen av anpassade Azure AD-roller ingår möjligheten att skapa en roll för att tilldela behörigheter för att hantera app-registreringar. Med tiden kommer ytterligare behörigheter för organisations resurser som företags program, användare och enheter att läggas till.  

Dessutom stöder anpassade Azure AD-roller tilldelningar per resurs, utöver de mer traditionella tilldelningarna i hela organisationen. Den här metoden ger dig möjlighet att bevilja åtkomst till att hantera vissa resurser (till exempel en app-registrering) utan att ge åtkomst till alla resurser (alla registrerings program).

Rollbaserad åtkomst kontroll i Azure AD är en offentlig för hands version av Azure AD och är tillgänglig med en betald Azure AD-licensserver. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="understand-azure-ad-role-based-access-control"></a>Förstå rollbaserad åtkomst kontroll i Azure AD

Att bevilja behörighet med anpassade Azure AD-roller är en två stegs process som inbegriper att skapa en anpassad roll definition och sedan tilldela den med en roll tilldelning. En anpassad roll definition är en samling behörigheter som du lägger till från en för inställnings lista. Dessa behörigheter är samma behörigheter som används i de inbyggda rollerna.  

När du har skapat roll definitionen kan du tilldela den till en användare genom att skapa en roll tilldelning. En roll tilldelning ger användaren behörigheten i en roll definition i en angiven omfattning. Med den här två stegs processen kan du skapa en enda roll definition och tilldela den flera gånger i olika omfång. Ett omfång definierar uppsättningen av Azure AD-resurser som roll medlemmen har åtkomst till. Det vanligaste omfånget är hela organisationen (org-wide). En anpassad roll kan tilldelas i hela organisationen, vilket innebär att roll medlemmen har rollen behörigheter för alla resurser i organisationen. En anpassad roll kan också tilldelas i ett objekt område. Ett exempel på ett objekt omfång är ett enda program. Samma roll kan tilldelas till en användare över alla program i organisationen och sedan till en annan användare med endast en omfattning av appen Contosos utgifts rapporter.  

Inbyggda och anpassade Azure AD-roller fungerar på begrepp som liknar [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../active-directory-b2c/overview.md). [Skillnaden mellan dessa två rollbaserade åtkomst kontroll system](../../role-based-access-control/rbac-and-directory-admin-roles.md) är att Azure RBAC styr åtkomsten till Azure-resurser, till exempel virtuella datorer eller lagrings utrymme med hjälp av Azure Resource Management, och anpassade Azure AD-roller styr åtkomsten till Azure AD-resurser med hjälp av Graph API. Båda systemen utnyttjar begreppet roll definitioner och roll tilldelningar. Det går inte att ta med RBAC-behörigheter för Azure AD i Azure-roller och vice versa.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Hur Azure AD avgör om en användare har åtkomst till en resurs

Följande är de övergripande steg som Azure AD använder för att avgöra om du har åtkomst till en hanterings resurs. Använd den här informationen för att felsöka åtkomst problem.

1. En användare (eller tjänstens huvud namn) hämtar en token till Microsoft Graph eller Azure AD Graph-slutpunkten.

1. Användaren gör ett API-anrop till Azure Active Directory (Azure AD) via Microsoft Graph eller Azure AD Graph med Utfärdad token.

1. Beroende på omständigheterna vidtar Azure AD en av följande åtgärder:

    - Utvärderar användarens roll medlemskap baserat på [wids-anspråk](../../active-directory-b2c/access-tokens.md) i användarens åtkomsttoken.
    - Hämtar alla roll tilldelningar som gäller för användaren, antingen direkt eller via grupp medlemskap, till den resurs som åtgärden utförs på.

1. Azure AD avgör om åtgärden i API-anropet ingår i de roller som användaren har för den här resursen.
1. Om användaren inte har någon roll med åtgärden i det begärda omfånget beviljas inte åtkomst. Annars beviljas åtkomst.

### <a name="role-assignments"></a>Rolltilldelningar

En roll tilldelning är det objekt som bifogar en roll definition till en användare i ett visst omfång för att bevilja åtkomst till Azure AD-resurser. Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort. På grund av detta består en roll tilldelning av tre element:

- Användare (en person som har en användar profil i Azure Active Directory)
- Rolldefinition
- Resursomfång

Du kan [skapa roll tilldelningar](custom-create.md) med hjälp av Azure Portal, Azure AD PowerShell eller Graph API. Du kan också [Visa tilldelningarna för en anpassad roll](custom-view-assignments.md#view-the-assignments-of-a-role).

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har Christer grönt tilldelats den anpassade rollen app Registration-administratör i omfånget för Contoso widget Builder-appens registrering. Tilldelningen ger Christer behörigheterna för administratörs rollen för app-registrering enbart för den här aktuella program registreringen.

![Roll tilldelningen är hur behörigheter tillämpas och har tre delar](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Säkerhetsobjekt

Ett säkerhets objekt representerar den användare som ska tilldelas åtkomst till Azure AD-resurser. En *användare* är en person som har en användar profil i Azure Active Directory.

### <a name="role"></a>Roll

En roll definition eller roll är en samling behörigheter. En roll definition visar de åtgärder som kan utföras på Azure AD-resurser, till exempel skapa, läsa, uppdatera och ta bort. Det finns två typer av roller i Azure AD:

- Inbyggda roller som skapats av Microsoft och som inte kan ändras.
- Anpassade roller som skapas och hanteras av din organisation.

### <a name="scope"></a>Omfång

Ett omfång är begränsningen av tillåtna åtgärder till en viss Azure AD-resurs som en del av en roll tilldelning. När du tilldelar en roll kan du ange ett omfång som begränsar administratörens åtkomst till en speciell resurs. Om du till exempel vill ge en utvecklare en anpassad roll, men bara för att hantera en specifik program registrering, kan du inkludera den specifika program registreringen som ett omfång i roll tilldelningen.

  > [!Note]
  > Anpassade roller kan tilldelas i katalog omfattning och resurs omfång. De kan ännu inte tilldelas i det administrativa enhets omfånget.
  > Inbyggda roller kan tilldelas i katalog omfattning och i vissa fall administrativa enhets omfång. De kan ännu inte tilldelas till Azure AD-resursens omfång.

## <a name="required-license-plan"></a>Obligatorisk licens plan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Skapa anpassade roll tilldelningar med hjälp av [Azure Portal, Azure AD PowerShell och Graph API](custom-create.md)
- [Visa tilldelningarna för en anpassad roll](custom-view-assignments.md)
