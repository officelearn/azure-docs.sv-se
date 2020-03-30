---
title: Personlig skrivbordsuppgiftstyp för Windows Virtual Desktop - Azure
description: Konfigurera tilldelningstypen för en personlig skrivbordsvärdpool för Windows Virtual Desktop.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128281"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurera tilldelningstypen för den personliga värdpoolen

Du kan konfigurera tilldelningstypen för din personliga skrivbordsvärdpool för att justera windows virtual desktop-miljön så att den passar dina behov bättre. I det här avsnittet visar vi hur du konfigurerar automatisk eller direkt tilldelning för dina användare.

>[!NOTE]
> Instruktionerna i den här artikeln gäller endast för personliga skrivbordsvärdpooler, inte poolerade värdpooler, eftersom användare i poolerade värdpooler inte tilldelas specifika sessionsvärdar.

## <a name="configure-automatic-assignment"></a>Konfigurera automatisk tilldelning

Automatisk tilldelning är standardtilldelningstypen för nya personliga skrivbordsvärdpooler som skapats i windows virtual desktop-miljö. Att automatiskt tilldela användare kräver inte en viss sessionsvärd.

Om du vill tilldela användare automatiskt tilldelar du dem först till den personliga värdpoolen för skrivbordet så att de kan se skrivbordet i sin feed. När en tilldelad användare startar skrivbordet i sin feed gör de anspråk på en tillgänglig sessionsvärd om de inte redan är anslutna till värdpoolen, som slutför tilldelningsprocessen.

Innan du börjar [laddar du ned och importerar Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) om du inte redan har gjort det. 

> [!NOTE]
> Kontrollera att du har installerat Windows Virtual Desktop PowerShell-modul version 1.0.1534.2001 eller senare innan du följer dessa instruktioner.

Därefter kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Om du vill konfigurera en värdpool för att automatiskt tilldela användare till virtuella datorer kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Om du vill tilldela en användare till den personliga värdpoolen för skrivbordet kör du följande PowerShell-cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Konfigurera direkt tilldelning

Till skillnad från automatisk tilldelning måste du, när du använder direkt tilldelning, tilldela användaren till både den personliga skrivbordsvärdpoolen och en viss sessionsvärd innan de kan ansluta till sitt personliga skrivbord. Om användaren bara tilldelas en värdpool utan en sessionsvärdtilldelning kan han eller hon inte komma åt resurser.

Om du vill konfigurera en värdpool så att den kräver direkt tilldelning av användare till sessionsvärdar kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Om du vill tilldela en användare till den personliga värdpoolen för skrivbordet kör du följande PowerShell-cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Om du vill tilldela en användare till en viss sessionsvärd kör du följande PowerShell-cmdlet:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat den personliga tilldelningstypen för skrivbordet kan du logga in på en Windows Virtual Desktop-klient för att testa den som en del av en användarsession. De här två följande how-tos kommer att berätta hur du ansluter till en session med hjälp av den klient som du väljer:

- [Ansluta med Windows-skrivbordsklienten](connect-windows-7-and-10.md)
- [Ansluta med webbklienten](connect-web.md)
