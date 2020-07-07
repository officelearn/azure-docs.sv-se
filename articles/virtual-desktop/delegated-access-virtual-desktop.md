---
title: Delegerad åtkomst i Windows Virtual Desktop – Azure
description: Så här delegerar du administrativa funktioner på en distribution av virtuella Windows-datorer, inklusive exempel.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 16b4fca475f91a8cb5b7f9a20ea5aa74b6b674a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82612868"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Delegerad åtkomst i Windows Virtual Desktop

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/delegated-access-virtual-desktop-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop har en delegerad åtkomst modell som gör att du kan definiera hur mycket åtkomst en viss användare får ha genom att tilldela dem en roll. En roll tilldelning har tre komponenter: säkerhets objekt, roll definition och omfattning. Den delegerade åtkomst modellen för Windows Virtual Desktop baseras på Azure RBAC-modellen. Mer information om de olika roll tilldelningarna och deras komponenter finns i [Översikt över Azure rollbaserad åtkomst kontroll](../role-based-access-control/built-in-roles.md).

Windows Virtual Desktop-delegerad åtkomst stöder följande värden för varje element i roll tilldelningen:

* Säkerhetsobjekt
    * Användare
    * Användargrupper
    * Tjänstens huvudnamn
* Rolldefinition
    * Inbyggda roller
    * Anpassade roller
* Omfång
    * Värdar för pooler
    * Appgrupper
    * Arbetsytor

## <a name="powershell-cmdlets-for-role-assignments"></a>PowerShell-cmdletar för roll tilldelningar

Innan du börjar ska du kontrol lera att du följer anvisningarna i [Konfigurera PowerShell-modulen](powershell-module.md) för att konfigurera Windows Virtual Desktop PowerShell-modulen om du inte redan har gjort det.

Windows Virtual Desktop använder Azure rollbaserad åtkomst kontroll (RBAC) när du publicerar program grupper till användare eller användar grupper. Användar rollen för Skriv bords virtualisering tilldelas till användaren eller användar gruppen och omfånget är app-gruppen. Den här rollen ger användaren särskilda data åtkomst till app-gruppen.  

Kör följande cmdlet för att lägga till Azure Active Directory användare i en app-grupp:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'  
```

Kör följande cmdlet för att lägga till Azure Active Directory användar grupp i en app-grupp:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

## <a name="next-steps"></a>Nästa steg

En fullständig lista över PowerShell-cmdletar som varje roll kan använda finns i [PowerShell-referensen](/powershell/windows-virtual-desktop/overview).

En fullständig lista över roller som stöds i Azure RBAC finns i [inbyggda roller i Azure](../role-based-access-control/built-in-roles.md).

Rikt linjer för hur du konfigurerar en Windows Virtual Desktop-miljö finns i [Windows Virtual Desktop-miljö](environment-setup.md).
