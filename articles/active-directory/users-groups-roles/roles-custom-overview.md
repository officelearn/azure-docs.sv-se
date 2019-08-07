---
title: Förhandsgranska Azure Administrator-roller med anpassningsbara behörigheter – Azure Active Directory | Microsoft Docs
description: Förhandsgranska anpassade Azure AD-roller för att delegera identitets hantering. Hantera Azure-roller i Azure Portal, PowerShell eller Graph API.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82638e3e102f7b8e39cd797960a11f3193132bc1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779394"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Anpassade administratörs roller i Azure Active Directory (för hands version)

Den här artikeln beskriver hur du förstår den nya anpassade RBAC-rollen (rollbaserad åtkomst kontroll) och resurs omfång i Azure Active Directory (Azure AD). Anpassade RBAC-roller har de underliggande behörigheterna för de [inbyggda rollerna](directory-assign-admin-roles.md) , så att du kan skapa och ordna dina egna anpassade roller. Resurs omfattningar ger dig ett sätt att tilldela den anpassade rollen för att hantera vissa resurser (t. ex. ett program) utan att ge åtkomst till alla resurser (alla program).

Att bevilja behörighet med anpassade RBAC-roller är en två stegs process. Först skapar du en anpassad roll definition och lägger till behörigheter till den från listan för för inställningar. Detta är samma behörigheter som används i de inbyggda rollerna. När du har skapat rollen tilldelar du den till någon genom att skapa en roll tilldelning. Med den här två stegs processen kan du skapa en roll och tilldela den flera gånger i olika omfång. En anpassad roll kan tilldelas i katalog omfånget, eller så kan den tilldelas till ett objekt omfång. Ett exempel på ett objekt omfång är ett enda program. På så sätt kan samma roll tilldelas Lisa över alla program i katalogen och sedan Naveen över bara appen Contosos utgifts rapporter.

Den första versionen av anpassade RBAC-roller innehåller möjligheten att skapa en roll för att tilldela behörigheter för att hantera app-registreringar. Med tiden kommer ytterligare behörigheter för organisations resurser som företags program, användare och enheter att läggas till.

För hands versions funktioner:

- Portal UI-uppdateringar för att skapa och hantera anpassade roller och tilldela dem till användare i hela organisationen
- En Preview PowerShell-modul med nya cmdlet: ar för att:
  - Skapa och hantera anpassade roller
  - Tilldela anpassade roller med antingen en organisations bred eller per app-registrerings omfång
  - Tilldela inbyggda roller i hela organisationen omfattning (paritet med GA-cmdletar)
  - Stöd för Azure AD-Graph API

Rollbaserad åtkomst kontroll i Azure AD är en offentlig för hands version av Azure AD och är tillgänglig med en betald Azure AD-licensserver. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Förstå rollbaserad åtkomst kontroll i Azure AD

Med rollbaserad åtkomst kontroll i Azure AD kan du tilldela roller som är anpassade för att tillåta tillåtna åtgärder på endast en viss typ av Azure AD-resurs. Rollbaserad åtkomst i Azure AD fungerar på begrepp som liknar rollbaserad åtkomst kontroll i Azure ([Azure RBAC](../../role-based-access-control/overview.md) för Azure Resource Access, men rollbaserad åtkomst kontroll i Azure AD baseras på Microsoft Graph och Azure RBAC baseras på Azure Resource Manager. Men båda systemen baserar sina funktioner på roll tilldelningar.

### <a name="role-assignments"></a>Rolltilldelningar

Hur du styr åtkomsten med hjälp av rollbaserad åtkomst kontroll i Azure AD är att skapa **roll tilldelningar**som används för att genomdriva behörigheter. En roll tilldelning består av tre element:

- Säkerhetsobjekt
- Rolldefinition
- Resurs omfång

Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort. Du kan [skapa roll tilldelningar](roles-create-custom.md) med hjälp av Azure Portal, Azure AD PowerShell och Graph API. Du kan separat [Visa tilldelningarna för en anpassad roll](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har Christer grönt tilldelats rollen [program administratör](directory-assign-admin-roles.md#application-administrator) i omfattningen för Salesforce-programmet. Christer har inte behörighet att hantera andra program, om de inte ingår i en annan roll tilldelning.

![Roll tilldelningen är hur behörigheter tillämpas och har tre delar](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Säkerhetsobjekt

Ett säkerhets objekt representerar användaren eller tjänstens huvud namn som ska tilldelas åtkomst till Azure AD-resurser. En *användare* är en person som har en användar profil i Azure Active Directory. Ett *huvud namn för tjänsten* är en säkerhets identitet som används av program eller tjänster för att få åtkomst till särskilda Azure AD-resurser.

Ett säkerhets objekt liknar en användar identitet i som den representerar ett användar namn och lösen ord eller certifikat, men för ett program eller en tjänst i stället för en användare.

### <a name="role"></a>Role

En roll definition eller roll är en samling behörigheter. En roll definition visar de åtgärder som kan utföras på Azure AD-resurser, till exempel skapa, läsa, uppdatera och ta bort. Det finns två typer av roller i Azure AD:

- Inbyggda roller som skapats av Microsoft och som inte kan ändras. Den inbyggda rollen global administratör har alla behörigheter för alla Azure AD-resurser.
- Anpassade roller som skapas och hanteras av din organisation.

### <a name="scope"></a>Omfång

Ett omfång är begränsningen av tillåtna åtgärder till en viss Azure AD-resurs. När du tilldelar en roll kan du ange ett omfång som begränsar administratörens tillåtna åtgärder till en speciell resurs. Om du till exempel vill ge en utvecklare en anpassad roll, men bara för att hantera en specifik program registrering, kan du inkludera den specifika program registreringen som ett omfång i roll tilldelningen.

  > [!Note]
  > Anpassade roller kan tilldelas i katalog omfattning och resurs omfång. De kan ännu inte tilldelas i det administrativa enhets omfånget.
  > Inbyggda roller kan tilldelas i katalog omfattning och i vissa fall administrativa enhets omfång. De kan ännu inte tilldelas till objekt omfånget.

## <a name="required-license-plan"></a>Obligatorisk licens plan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Skapa anpassade roll tilldelningar med hjälp av [Azure Portal, Azure AD PowerShell och Graph API](roles-create-custom.md)
- [Visa tilldelningarna för en anpassad roll](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
