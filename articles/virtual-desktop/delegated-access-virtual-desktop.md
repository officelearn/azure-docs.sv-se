---
title: Delegerad åtkomst i Windows Virtual Desktop - Azure
description: Delegera administrativa funktioner för en Windows Virtual Desktop-distribution, inklusive exempel.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91451ff3024a9a5019b3982b0e4471e2c4d80c74
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683921"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegerad åtkomst i Windows Virtual Desktop

Windows Virtual Desktop har en delegerad åtkomstmodell som gör att du kan definiera hur mycket åtkomst en viss användare får ha genom att tilldela dem en roll. En rolltilldelning har tre komponenter: säkerhetsobjekt, rolldefinition och omfattning. Den delegerade åtkomstmodellen för Virtuellt skrivbord i Windows baseras på Azure RBAC-modellen. Mer information om specifika rolltilldelningar och deras komponenter finns [i översikten över Azure-rollbaserad åtkomstkontroll](../role-based-access-control/built-in-roles.md).

Delegerad åtkomst för Virtuellt skrivbord i Windows Stöder följande värden för varje element i rolltilldelningen:

* Säkerhetsobjekt
    * Användare
    * Tjänstens huvudnamn
* Rolldefinition
    * Inbyggda roller
* Omfång
    * Klientgrupper
    * Klientorganisationer
    * Värdpooler
    * Appgrupper

## <a name="built-in-roles"></a>Inbyggda roller

Delegerad åtkomst i Windows Virtual Desktop har flera inbyggda rolldefinitioner som du kan tilldela användare och tjänsthuvudnamn.

* En RDS-ägare kan hantera allt, inklusive åtkomst till resurser.
* En RDS-deltagare kan hantera allt, men kan inte komma åt resurser.
* En RDS Reader kan visa allt, men kan inte göra några ändringar.
* En RDS-operatör kan visa diagnostikaktiviteter.

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-cmdletar för rolltilldelningar

Du kan köra följande cmdlets för att skapa, visa och ta bort rolltilldelningar:

* **Get-RdsRoleAssignment** visar en lista över rolltilldelningar.
* **New-RdsRoleTilldelning** skapar en ny rolltilldelning.
* **Ta bort-RdsRoleTilldelning tar** bort rolltilldelningar.

### <a name="accepted-parameters"></a>Godkända parametrar

Du kan ändra de grundläggande tre cmdlets med följande parametrar:

* **AadTenantId**: anger Azure Active Directory-klient-ID som tjänstens huvudnamn är medlem i.
* **AppGroupName**: namnet på appgruppen För fjärrskrivbord.
* **Diagnostik**: anger diagnostikomfånget. (Måste paras ihop med antingen **parametrarna Infrastruktur** eller **Klient.)**
* **HostPoolName**: namnet på värdpoolen för fjärrskrivbord.
* **Infrastruktur**: anger infrastrukturomfattningen.
* **RoleDefinitionName**: namnet på rollbaserad åtkomstkontrollroll för fjärrskrivbordstjänster som tilldelats användaren, gruppen eller appen. (Till exempel ägare av fjärrskrivbordstjänster, läsare av fjärrskrivbordstjänster och så vidare.)
* **ServerPrincipleName**: namnet på Azure Active Directory-programmet.
* **SignInName**: användarens e-postadress eller användarens huvudnamn.
* **TenantName**: namnet på klientinnehavaren för fjärrskrivbord.

## <a name="next-steps"></a>Nästa steg

En mer komplett lista över PowerShell-cmdlets som varje roll kan använda finns i [PowerShell-referensen](/powershell/windows-virtual-desktop/overview).

Riktlinjer för hur du konfigurerar en Windows Virtual Desktop-miljö finns i [Windows Virtual Desktop-miljö](environment-setup.md).
