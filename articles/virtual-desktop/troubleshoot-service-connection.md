---
title: Felsöka tjänst anslutning Windows Virtual Desktop – Azure
description: Så här löser du problem när du konfigurerar klient anslutningar i en Windows-klient för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: bf1af6ab0d8187452ecc2e48dcf72de1093690fb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477114"
---
# <a name="windows-virtual-desktop-service-connections"></a>Anslutningar till virtuella Windows-datorer

Använd den här artikeln för att lösa problem med klient anslutningar för virtuella Windows-datorer.

## <a name="provide-feedback"></a>Ge feedback

Du kan ge oss feedback och diskutera Windows Virtual Desktop-tjänsten med produkt teamet och andra aktiva community-medlemmar i [Tech-communityn för Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Användaren ansluter men inget visas (ingen feed)

En användare kan starta fjärr skrivbords klienter och kan autentisera, men användaren ser inga ikoner i webb identifierings flödet.

Bekräfta att användaren som rapporterar problemen har tilldelats program grupper genom att använda den här kommando raden:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Bekräfta att användaren loggar in med rätt autentiseringsuppgifter.

Om webb klienten används kontrollerar du att det inte finns några problem med cachelagrade autentiseringsuppgifter.

## <a name="windows-10-enterprise-multi-session-virtual-machines-dont-respond"></a>Virtuella datorer med Windows 10 Enterprise multi-session svarar inte

Om en virtuell dator inte svarar och du inte kan komma åt den via RDP, måste du Felsöka den med funktionen diagnostik genom att kontrol lera värd statusen.

Kör denna cmdlet för att kontrol lera värd statusen:

```powershell
Get-RdsSessionHost -TenantName $TenantName -HostPoolName $HostPool | ft SessionHostName, LastHeartBeat, AllowNewSession, Status
```

Om värd statusen är `NoHeartBeat`innebär det att den virtuella datorn inte svarar och att agenten inte kan kommunicera med Windows Virtual Desktop-tjänsten.

```powershell
SessionHostName          LastHeartBeat     AllowNewSession    Status 
---------------          -------------     ---------------    ------ 
WVDHost1.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost2.contoso.com     21-Nov-19 5:21:35            True  Available 
WVDHost3.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost4.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
WVDHost5.contoso.com     21-Nov-19 5:21:35            True  NoHeartBeat 
```

Det finns några saker du kan göra för att åtgärda nopulsslag-statusen.

### <a name="update-fslogix"></a>Uppdatera FSLogix

Om din FSLogix inte är uppdaterad, särskilt om det är version 2.9.7205.27375 av frxdrvvt. sys, kan det orsaka ett död läge. Se till att [Uppdatera FSLogix till den senaste versionen](https://go.microsoft.com/fwlink/?linkid=2084562).

### <a name="disable-bgtaskregistrationmaintenancetask"></a>Inaktivera BgTaskRegistrationMaintenanceTask

Om uppdatering av FSLogix inte fungerar kan problemet vara att en BiSrv-komponent förbrukar system resurser under en veckovis underhålls uppgift. Inaktivera underhålls aktiviteten tillfälligt genom att inaktivera BgTaskRegistrationMaintenanceTask med någon av följande två metoder:

- Gå till Start-menyn och Sök efter **Schemaläggaren**. Gå till **biblioteket för schemaläggaren** > **Microsoft** > **Windows** > **BrokerInfrastructure**. Leta efter en aktivitet med namnet **BgTaskRegistrationMaintenanceTask**. När du har hittat den högerklickar du på den och väljer **inaktivera** på den nedrullningsbara menyn.
- Öppna en kommando rads meny som administratör och kör följande kommando:
    
    ```cmd
    schtasks /change /tn "\Microsoft\Windows\BrokerInfrastructure\BgTaskRegistrationMaintenanceTask" /disable 
    ```

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/resource-manager-tutorial-troubleshoot.md).
