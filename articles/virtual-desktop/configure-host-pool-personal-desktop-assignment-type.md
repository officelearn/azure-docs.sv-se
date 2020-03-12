---
title: Windows Virtual Desktop, personlig Skriv bords typ – Azure
description: Så här konfigurerar du tilldelnings typen för en virtuell Windows-dator med egen Skriv bords värd.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128281"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurera tilldelnings typen egen Skriv bords värd pool

Du kan konfigurera tilldelnings typen för din personliga Skriv bords värd för att anpassa din Windows Virtual Desktop-miljö så att den bättre passar dina behov. I det här avsnittet visar vi hur du konfigurerar automatisk eller direkt tilldelning för dina användare.

>[!NOTE]
> Anvisningarna i den här artikeln gäller bara för personliga Skriv bords värd pooler, inte pooler med pooler, eftersom användare i pooler värdar inte har tilldelats till vissa värdar för fjärrskrivbordssessioner.

## <a name="configure-automatic-assignment"></a>Konfigurera automatisk tilldelning

Automatisk tilldelning är standard tilldelnings typen för nya personliga Skriv bords värdar som skapats i din Windows Virtual Desktop-miljö. Automatisk tilldelning av användare kräver ingen speciell värd för sessionen.

Om du vill tilldela användare automatiskt måste du först tilldela dem till den personliga Skriv bords värds adresspoolen så att de kan se Skriv bordet i deras flöde. När en tilldelad användare startar Skriv bordet i sitt flöde ansluts de till en tillgänglig sessionsgräns om de inte redan har anslutit till den, vilket slutför tilldelnings processen.

Innan du börjar kan du [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) om du inte redan har gjort det. 

> [!NOTE]
> Kontrol lera att du har installerat PowerShell-modulen version 1.0.1534.2001 eller senare av Windows Virtual Desktop innan du följer dessa anvisningar.

Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Kör följande PowerShell-cmdlet för att konfigurera en värddator för att automatiskt tilldela användare till virtuella datorer:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Kör följande PowerShell-cmdlet för att tilldela en användare till den personliga Skriv bords värd poolen:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Konfigurera direkt tilldelning

Till skillnad från automatisk tilldelning måste du tilldela användaren till både den personliga Skriv bords värd poolen och en speciell sessionsnyckel innan de kan ansluta till sitt personliga skriv bord när du använder direkt tilldelning. Om användaren bara är tilldelad till en adresspool utan tilldelning av en sessionsgräns, kommer de inte att kunna komma åt resurser.

Kör följande PowerShell-cmdlet för att konfigurera en adresspool så att den kräver direkt tilldelning av användare till sessionsbaserade värdar:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Kör följande PowerShell-cmdlet för att tilldela en användare till den personliga Skriv bords värd poolen:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Kör följande PowerShell-cmdlet om du vill tilldela en användare till en speciell värd för sessionen:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat den personliga Skriv bords typen kan du logga in på en Windows Virtual Desktop-klient för att testa den som en del av en användarsession. Följande två instruktioner visar hur du ansluter till en session med valfri klient:

- [Ansluta till Windows Desktop-klienten](connect-windows-7-and-10.md)
- [Anslut till webb klienten](connect-web.md)
