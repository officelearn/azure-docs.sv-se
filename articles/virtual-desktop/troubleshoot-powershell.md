---
title: Windows Virtual Desktop PowerShell – Azure
description: Så här felsöker du problem med PowerShell när du konfigurerar en Windows-klient för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 38d9a2dda945f3a9459aa8e3360012c6ef422608
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163317"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

Använd den här artikeln för att lösa fel och problem när du använder PowerShell med Windows Virtual Desktop. Mer information om Fjärrskrivbordstjänster PowerShell finns i [Windows Virtual Desktop PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>PowerShell-kommandon som används vid installation av virtuella Windows-datorer

Det här avsnittet innehåller PowerShell-kommandon som vanligt vis används när du konfigurerar Windows Virtual Desktop och ger möjlighet att lösa problem som kan uppstå när du använder dem.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Fel: Add-RdsAppGroupUser-kommandot--angivet UserPrincipalName har redan kopplats till en RemoteApp-app-grupp i den angivna poolen

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Orsak:** Det användar namn som används har redan tilldelats en app-grupp av en annan typ. Användare kan inte tilldelas både till en fjärr skrivbord och en fjärran sluten program grupp under samma sessions värd pool.

**KORRIGERA:** Om användaren behöver både fjärrappar och fjärr skrivbord, kan du skapa olika lagringspooler eller bevilja användar åtkomst till fjärr skrivbordet, vilket innebär att alla program kan användas på den virtuella datorn för sessionen.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Fel: Add-RdsAppGroupUser-kommandot--angivet UserPrincipalName finns inte i den Azure Active Directory som är kopplad till fjärr skrivbords klienten

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Orsak:** Användaren som anges av-UserPrincipalName går inte att hitta i den Azure Active Directory som är kopplad till den virtuella Windows-klienten för fjärr skrivbord.

**KORRIGERA:** Bekräfta objekten i följande lista.

- Användaren är synkroniserad med Azure Active Directory.
- Användaren är inte knuten till B2C-eller Business-to-Business-handel (B2B).
- Windows-klienten för virtuella skriv bord är kopplad till rätt Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Fel: get-RdsDiagnosticActivities – användaren har inte behörighet att fråga hanterings tjänsten

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Orsak:** -TenantName-parameter

**KORRIGERA:** Utfärda get-RdsDiagnosticActivities med-TenantName \<TenantName >.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: get-RdsDiagnosticActivities – användaren har inte behörighet att fråga hanterings tjänsten

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Orsak:** Använd växeln-Deployment.

**KORRIGERA:** -distributions växeln kan endast användas av distributions administratörer. Dessa administratörer är vanligt vis medlemmar i den virtuella Skriv bords gruppen Fjärrskrivbordstjänster/Windows. Ersätt växeln-Deployment med-TenantName \<TenantName >.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: New-RdsRoleAssignment – användaren har inte behörighet att fråga hanterings tjänsten

**Orsak 1:** Kontot som används har inte Fjärrskrivbordstjänster ägar behörighet till klienten.

**Korrigering 1:** En användare med Fjärrskrivbordstjänster ägar behörighet måste köra roll tilldelningen.

**Orsak 2:** Kontot som används har Fjärrskrivbordstjänster ägar behörighet men är inte en del av klientens Azure Active Directory eller har inte behörighet att fråga den Azure Active Directory där användaren finns.

**Korrigera 2:** En användare med Active Directory behörigheter måste köra roll tilldelningen.

>[!Note]
>New-RdsRoleAssignment kan inte ge behörighet till en användare som inte finns i Azure Active Directory (AD).

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en klient och en adresspool i en Windows Virtual Desktop-miljö finns i [skapa innehavare och skapa värdar för pooler](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [fjärr skrivbords klient anslutningar](troubleshoot-client-connection.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
