---
title: Windows Virtual Desktop PowerShell – Azure
description: Felsöka problem med PowerShell när du konfigurerar en windows virtual desktop-klientmiljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127465"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

Använd den här artikeln för att lösa fel och problem när du använder PowerShell med Windows Virtual Desktop. Mer information om PowerShell för fjärrskrivbordstjänster finns i [Windows Virtual Desktop Powershell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produktteamet och aktiva communitymedlemmar.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>PowerShell-kommandon som används under installationen av Virtuell dator i Windows

I det här avsnittet visas PowerShell-kommandon som vanligtvis används när du konfigurerar Windows Virtual Desktop och du kan hitta sätt att lösa problem som kan uppstå när du använder dem.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Fel: Kommandot Add-RdsAppGroupUser – Det angivna UserPrincipalName har redan tilldelats en RemoteApp-appgrupp i den angivna värdpoolen

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Orsak:** Användarnamnet som används har redan tilldelats en appgrupp av en annan typ. Användare kan inte tilldelas både ett fjärrskrivbord och en fjärrappgrupp under samma sessionsvärdpool.

**Rättad:** Om användaren behöver både fjärrappar och fjärrskrivbord skapar du olika värdpooler eller ger användaren åtkomst till fjärrskrivbordet, vilket gör det möjligt att använda alla program på den virtuella sessionens värd.If user needs both remote apps and remote desktop, create different host pools or grant user access to the remote desktop, which will permit the use of any application on the session host VM.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Fel: Kommandot Add-RdsAppGroupUser – Det angivna UserPrincipalName finns inte i Azure Active Directory som är associerat med klientinnehavaren för fjärrskrivbord

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Orsak:** Användaren som anges av -UserPrincipalName kan inte hittas i Azure Active Directory som är knuten till Windows Virtual Desktop-klienten.

**Rättad:** Bekräfta objekten i följande lista.

- Användaren synkroniseras med Azure Active Directory.
- Användaren är inte knuten till företag till konsument (B2C) eller business-to-business (B2B) handel.
- Windows Virtual Desktop-klienten är knuten till korrekt Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Fel: Get-RdsDiagnosticActivities – Användaren har inte behörighet att fråga hanteringstjänsten

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Orsak:** -Parametern TenantName

**Rättad:** Problem Get-RdsDiagnosticActivities med \<-TenantName TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Get-RdsDiagnosticActivities – användaren har inte behörighet att fråga hanteringstjänsten

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Orsak:** Använda -Deployment switch.

**Åtgärdat:** -Distributionsväxeln kan endast användas av distributionsadministratörer. Dessa administratörer är vanligtvis medlemmar i teamet för fjärrskrivbordstjänster/Virtuella Windows-datorer. Ersätt växeln -Distribution med \<-TenantName TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Ny-RdsRoleAstilldelning – användaren har inte behörighet att fråga hanteringstjänsten

**Orsak 1:** Kontot som används har inte ägare av fjärrskrivbordstjänster för klienten.

**Åtgärda 1:** En användare med ägarbehörighet för Fjärrskrivbordstjänster måste köra rolltilldelningen.

**Orsak 2:** Kontot som används har ägarebehörigheter för Fjärrskrivbordstjänster men ingår inte i klientens Azure Active Directory eller har inte behörighet att fråga Azure Active Directory där användaren finns.

**Åtgärda 2:** En användare med Active Directory-behörigheter måste köra rolltilldelningen.

>[!Note]
>New-RdsRoleAssignment kan inte ge behörighet till en användare som inte finns i Azure Active Directory (AD).

## <a name="next-steps"></a>Nästa steg

- En översikt över felsökning av Windows Virtual Desktop och eskaleringsspåren finns i [Felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient- och värdpool i en Windows Virtual Desktop-miljö finns i [Skapandet av klient- och värdpooler](troubleshoot-set-up-issues.md).
- Mer om du vill felsöka problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [Konfigurationen för virtuell dator för session värd](troubleshoot-vm-configuration.md).
- Mer om du vill felsöka problem med Windows Virtual Desktop-klientanslutningar finns i [Windows Virtual Desktop-tjänstanslutningar](troubleshoot-service-connection.md).
- Om du vill felsöka problem med fjärrskrivbordsklienter finns i [Felsöka klienten för fjärrskrivbord](troubleshoot-client.md)
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en felsökningsguide finns i [Självstudiekurs: Felsöka Resource Manager-malldistributioner](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om granskningsåtgärder finns i [Granskningsåtgärder med Resurshanteraren](../azure-resource-manager/management/view-activity-logs.md).
- Mer information om åtgärder för att fastställa fel under distributionen finns i [Visa distributionsåtgärder](../azure-resource-manager/templates/deployment-history.md).
