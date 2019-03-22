---
title: Delegerad åtkomst i Windows virtuellt skrivbord (förhandsversion), Azure
description: Hur du delegerar administrativa funktioner på en distribution med virtuella Windows-skrivbordet, inklusive exempel.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 49645b697071abb8a2f8c85ebde1e6761a3536ab
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336107"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Delegerad åtkomst i Windows virtuellt skrivbord (förhandsversion)

Virtuella Windows-skrivbordet (förhandsversion) har en delegerad åtkomst-modell som kan du definiera mängden åtkomst som en viss användare tillåts ha genom att tilldela dem en roll. En rolltilldelning har tre komponenter: säkerhetsobjekt, rolldefinitionen och omfång. Virtuella Windows-skrivbordet delegerad åtkomstmodellen baseras på Azure RBAC-modellen. Läs mer om specifika rolltilldelningar och komponenter i [i Azure rollbaserad åtkomstkontroll: översikt](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Virtuella Windows-skrivbordet delegerad åtkomst stöder följande värden för varje element i rolltilldelningen:

* Säkerhetsobjekt
    * Användare
    * Tjänstens huvudnamn
* Rolldefinition
    * Inbyggda roller
* Scope
    * Klient-grupper
    * Klienter
    * Värd-pooler
    * App-grupper

## <a name="built-in-roles"></a>Inbyggda roller

Delegerad åtkomst i virtuella Windows-skrivbordet har flera inbyggda rolldefinitioner som du kan tilldela till användare och tjänstens huvudnamn.

* En RDS-ägare kan hantera allt, inklusive åtkomst till resurser.
* En RDS-deltagare kan hantera allt utom åtkomst till resurser.
* En RDS-läsare kan visa allt, men göra inte några ändringar.
* En RDS-operatör kan visa diagnostik aktiviteter.

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-cmdletar för rolltilldelningar

Du kan köra följande cmdlets för att skapa, visa och redigera rolltilldelningar:

* **Get-RdsRoleAssignment** visar en lista över rolltilldelningar.
* **Ny RdsRoleAssignment** skapar en ny rolltilldelning.
* **Set-RdsRoleAssignment** redigerar rolltilldelningar.

### <a name="accepted-parameters"></a>Accepterade parametrar

Du kan ändra grundläggande tre cmdletar med följande parametrar:

* **AadTenantId**: Anger Azure Active Directory-klient-ID som tjänstens huvudnamn är medlem.
* **AppGroupName**: namnet på gruppen Remote Desktop-appen.
* **Diagnostik**: anger omfattningen för diagnostik. (Måste kopplas till antingen den **infrastruktur** eller **klient** parametrar.)
* **HostPoolName**: namnet på poolen Remote Desktop-värd.
* **Infrastruktur**: anger omfattningen för infrastruktur.
* **RoleDefinitionName**: namnet på rollen Fjärrskrivbordstjänster rollbaserad åtkomstkontroll tilldelat till användaren, gruppen eller app. (Till exempel Remote Desktop Services ägare, Remote Desktop Services läsare och så vidare.)
* **ServerPrincipleName**: namnet på Azure Active Directory-programmet.
* **SignInName**: användarens e-postadress eller användarens huvudnamn.
* **TenantName**: namnet på den Remote Desktop-klienten.

## <a name="next-steps"></a>Nästa steg

En fullständig lista över varje roll kan använda PowerShell-cmdlets, se den [PowerShell-referens](/powershell/windows-virtual-desktop/overview).

Riktlinjer för hur du ställer in en virtuellt skrivbord i Windows-miljö finns i [ställer in en miljö för virtuella Windows-skrivbordet](environment-setup.md).
