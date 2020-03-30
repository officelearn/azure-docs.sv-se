---
title: Felsöka tjänstanslutning Windows Virtual Desktop - Azure
description: Så här löser du problem när du konfigurerar klientanslutningar i en windows virtual desktop-klientmiljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 57d5198cb54dc096fb09bb52d76539b1e4bbc1f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127459"
---
# <a name="windows-virtual-desktop-service-connections"></a>Serviceanslutningar för Windows Virtual Desktop

Använd den här artikeln för att lösa problem med Windows Virtual Desktop-klientanslutningar.

## <a name="provide-feedback"></a>Ge feedback

Du kan ge oss feedback och diskutera Windows Virtual Desktop Service med produktteamet och andra aktiva community-medlemmar på [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Användaren ansluter men ingenting visas (ingen feed)

En användare kan starta fjärrskrivbordsklienter och kan autentisera, men användaren ser inga ikoner i webbidentifieringsflödet.

Bekräfta att användaren som rapporterar problemen har tilldelats programgrupper med hjälp av den här kommandoraden:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Bekräfta att användaren loggar in med rätt autentiseringsuppgifter.

Om webbklienten används bekräftar du att det inte finns några cachelagrade autentiseringsuppgifter.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Virtuella datorer med flera flera sessioner i Windows 10 svarade inte

Om en virtuell dator inte svarar och du inte kan komma åt den via RDP måste du felsöka den med diagnostikfunktionen genom att kontrollera värdstatusen.

Om du vill kontrollera värdstatusen kör du den här cmdleten:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Om värdstatusen `NoHeartBeat`är betyder det att den virtuella datorn inte svarar och agenten inte kan kommunicera med Windows Virtual Desktop-tjänsten.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True     Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True     NoHeartBeat 
```

Det finns några saker du kan göra för att åtgärda NoHeartBeat-statusen.

### <a name="update-fslogix"></a>Uppdatera FSLogix

Om din FSLogix inte är uppdaterad, särskilt om det är version 2.9.7205.27375 av frxdrvvt.sys, kan det orsaka ett dödläge. Se till att [uppdatera FSLogix till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Inaktivera BgTaskRegistrationMaintenanceTask

Om uppdateringen av FSLogix inte fungerar kan problemet bero på att en BiSrv-komponent uttömmer systemresurser under en underhållsaktivitet varje vecka. Inaktivera underhållsuppgiften tillfälligt genom att inaktivera BgTaskRegistrationMaintenanceTask med en av följande två metoder:

- Gå till Start-menyn och sök efter **Schemaläggaren**. Navigera till **Schemaläggarens bibliotek** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Leta efter en uppgift som heter **BgTaskRegistrationMaintenanceTask**. När du hittar den högerklickar du på den och väljer **Inaktivera** på den nedrullningsbara menyn.
- Öppna en kommandoradsmeny som administratör och kör följande kommando:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Nästa steg

- En översikt över felsökning av Windows Virtual Desktop och eskaleringsspåren finns i [Felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient- och värdpool i en Windows Virtual Desktop-miljö finns i [Skapandet av klient- och värdpooler](troubleshoot-set-up-issues.md).
- Mer om du vill felsöka problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [Konfigurationen för virtuell dator för session värd](troubleshoot-vm-configuration.md).
- Mer om du vill felsöka problem när du använder PowerShell med Virtuellt Windows-skrivbord finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Information om hur du går igenom en felsökningsguide finns i [Självstudiekurs: Felsöka Resource Manager-malldistributioner](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
