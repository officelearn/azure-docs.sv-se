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
ms.openlocfilehash: eabf29b10814d19e89c21f27ec66fce5355c9bfb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880693"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Anpassade administratörs roller i Azure Active Directory (för hands version)

Den här artikeln beskriver hur du förstår den nya anpassade rollbaserad åtkomst kontroll (RBAC) och resurs omfång i Azure Active Directory (Azure AD). Anpassade RBAC-roller har de underliggande behörigheterna för de [inbyggda rollerna](directory-assign-admin-roles.md) , så att du kan skapa och ordna dina egna anpassade roller. Med den här metoden kan du ge åtkomst på ett mer detaljerat sätt än inbyggda roller, vid behov. Den första versionen av anpassade RBAC-roller innehåller möjligheten att skapa en roll för att tilldela behörigheter för att hantera app-registreringar. Med tiden kommer ytterligare behörigheter för organisations resurser som företags program, användare och enheter att läggas till.  

Dessutom stöder anpassade RBAC-roller tilldelningar per resurs, utöver de mer traditionella tilldelningarna i hela organisationen. Den här metoden ger dig möjlighet att bevilja åtkomst till att hantera vissa resurser (till exempel en app-registrering) utan att ge åtkomst till alla resurser (alla registrerings program).

Rollbaserad åtkomst kontroll i Azure AD är en offentlig för hands version av Azure AD och är tillgänglig med en betald Azure AD-licensserver. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Förstå rollbaserad åtkomst kontroll i Azure AD

Att bevilja behörighet med anpassade RBAC-roller är en två stegs process som inbegriper att skapa en anpassad roll definition och sedan tilldela den med en roll tilldelning. En anpassad roll definition är en samling behörigheter som du lägger till från en för inställnings lista. Dessa behörigheter är samma behörigheter som används i de inbyggda rollerna.  

När du har skapat roll definitionen kan du tilldela den till någon genom att skapa en roll tilldelning. En roll tilldelning ger någon av behörigheterna i en roll definition i ett särskilt omfång. Med den här två stegs processen kan du skapa en roll definition och tilldela den flera gånger i olika omfång. Ett omfång definierar den uppsättning resurser som roll medlemmen har åtkomst till. Det vanligaste omfånget är hela organisationen (org-wide). En anpassad roll kan tilldelas i hela organisationen, vilket innebär att roll medlemmen har rollen behörigheter för alla resurser i organisationen. En anpassad roll kan också tilldelas i ett objekt område. Ett exempel på ett objekt omfång är ett enda program. På så sätt kan samma roll tilldelas Lisa över alla program i organisationen och sedan Naveen över bara appen Contosos utgifts rapporter.  

Azure AD RBAC arbetar på begrepp som liknar [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/overview.md). Skillnaden är att Azure RBAC styr åtkomsten till Azure-resurser, till exempel virtuella datorer och webbplatser, och Azure AD RBAC kontrollerar åtkomsten till Azure AD. Båda systemen utnyttjar begreppet roll definitioner och roll tilldelningar.

### <a name="role-assignments"></a>Rolltilldelningar

En roll tilldelning är en process för att koppla en roll definition till en användare i ett visst omfång i syfte att bevilja åtkomst. Åtkomst beviljas genom att en rolltilldelning skapas, och åtkomst återkallas genom att en rolltilldelning tas bort. En roll tilldelning består av tre element:
- Användare
- Rolldefinition
- Resurs omfång

Du kan [skapa roll tilldelningar](roles-create-custom.md) med hjälp av Azure Portal, Azure AD PowerShell eller Graph API. Du kan också [Visa tilldelningarna för en anpassad roll](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Följande diagram visar ett exempel på en rolltilldelning. I det här exemplet har Christer grönt tilldelats den anpassade rollen app Registration-administratör i omfånget för Contoso widget Builder-appens registrering. Den här tilldelningen ger Chris behörighet för administratörs rollen för app-registrering endast på den här aktuella app-registreringen.

![Roll tilldelningen är hur behörigheter tillämpas och har tre delar](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Säkerhetsobjekt

Ett säkerhets objekt representerar den användare som ska tilldelas åtkomst till Azure AD-resurser. En *användare* är en person som har en användar profil i Azure Active Directory.

### <a name="role"></a>Role

En roll definition eller roll är en samling behörigheter. En roll definition visar de åtgärder som kan utföras på Azure AD-resurser, till exempel skapa, läsa, uppdatera och ta bort. Det finns två typer av roller i Azure AD:

- Inbyggda roller som skapats av Microsoft och som inte kan ändras. Den inbyggda rollen global administratör har alla behörigheter för alla Azure AD-resurser.
- Anpassade roller som skapas och hanteras av din organisation.

### <a name="scope"></a>Omfång

Ett omfång är begränsningen av tillåtna åtgärder till en viss Azure AD-resurs som en del av en roll tilldelning. När du tilldelar en roll kan du ange ett omfång som begränsar administratörens åtkomst till en speciell resurs. Om du till exempel vill ge en utvecklare en anpassad roll, men bara för att hantera en specifik program registrering, kan du inkludera den specifika program registreringen som ett omfång i roll tilldelningen.

  > [!Note]
  > Anpassade roller kan tilldelas i katalog omfattning och resurs omfång. De kan ännu inte tilldelas i det administrativa enhets omfånget.
  > Inbyggda roller kan tilldelas i katalog omfattning och i vissa fall administrativa enhets omfång. De kan ännu inte tilldelas till objekt omfånget.

## <a name="required-license-plan"></a>Obligatorisk licens plan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Skapa anpassade roll tilldelningar med hjälp av [Azure Portal, Azure AD PowerShell och Graph API](roles-create-custom.md)
- [Visa tilldelningarna för en anpassad roll](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
