---
title: Windows virtuella skrivbord PowerShell – Azure
description: Så här felsöker du problem med PowerShell när du konfigurerar en miljö för virtuella Windows-skrivbordet organisationer.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927514"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows PowerShell på virtuella skrivbord

Använd den här artikeln för att lösa fel och problem när du använder PowerShell med virtuella Windows-skrivbordet. Mer information om Remote Desktop Services PowerShell finns i [Windows Powershell för virtuella skrivbord](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Ge feedback

Vi inte är för närvarande tar supportärenden när virtuella Windows-skrivbordet är i förhandsversionen. Gå till den [Windows Desktop Tech-Community virtuella](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) att diskutera virtuellt skrivbord i Windows-tjänsten med produktteamet och aktiva community-medlemmar.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>PowerShell-kommandon som används under installationen av Windows virtuella skrivbord

Det här avsnittet innehåller PowerShell-kommandon som används vanligtvis när du konfigurerar virtuella Windows-skrivbordet och ger sätt att lösa problem som kan uppstå när du använder dem.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Fel: Lägg till RdsAppGroupUser kommandot--angivna UserPrincipalName har redan tilldelats en RemoteApp-app-grupp i den angivna värd-poolen

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Orsak:** Användarnamnet som används har redan tilldelats till en appgrupp med en annan typ. Användare kan inte tilldelas till både en fjärransluten fjärrskrivbord och app-grupp under samma session värd pool.

**Fix:** Om användaren behöver både appar för fjärråtkomst och fjärrskrivbord, skapa pooler med olika värden eller bevilja användaråtkomst till fjärrskrivbord, vilket tillåter användning av alla program på den Virtuella värddatorn i sessionen.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Fel: Lägg till RdsAppGroupUser kommandot--angivna UserPrincipalName finns inte i Azure Active Directory som är associerade med Remote Desktop-klient

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Orsak:** Den användare som anges av - UserPrincipalName kan inte hittas i Azure Active Directory som är kopplad till virtuella skrivbord i Windows-klient.

**Fix:** Bekräfta objekten i listan nedan.

- Användaren är synkroniserad till Azure Active Directory.
- Användaren är inte kopplat till företag till konsument (B2C) eller business-to-business (B2B) handel.
- Virtuellt skrivbord i Windows-klient är kopplad till rätt Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Fel: Get-RdsDiagnosticActivities – Användaren inte behörighet att skicka frågor till management-tjänsten

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Orsak:** TenantName - parameter

**Fix:** Utfärda Get-RdsDiagnosticActivities med - TenantName <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Get-RdsDiagnosticActivities – användaren inte behörighet att skicka frågor till management-tjänsten

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Orsak:** Med - distribution växel.

**Korrigering:** -distribution växeln kan endast användas av administratörer för distribution. Dessa administratörer är vanligtvis medlemmar av Remote Desktop Services/Windows Virtual Desktop-teamet. Ersätt-distribution-växel med - TenantName <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Nya RdsRoleAssignment – användaren inte behörighet att skicka frågor till management-tjänsten

**Orsak 1:** Kontot som används har inte Remote Desktop Services ägarbehörighet för innehavaren.

**Fix 1:** En användare med Remote Desktop Services ägarbehörighet måste köra rolltilldelningen.

**Orsak 2:** Kontot som används har ägarbehörighet för Remote Desktop Services men är inte en del av klientens Azure Active Directory eller har inte behörighet att fråga efter Azure Active Directory där användaren finns.

**Fix 2:** En användare med Active Directory-behörigheter måste köra rolltilldelningen.

>[!Note]
>Nya RdsRoleAssignment kan inte ge behörigheter till en användare som inte finns i Azure Active Directory (AD).

## <a name="next-steps"></a>Nästa steg

- En översikt om hur du felsöker virtuella Windows-skrivbordet och Eskalering spårar finns [översikt, feedback och support](troubleshoot-set-up-overview.md).
- Felsökning av problem när du skapar en pool med klient- och värden i en miljö med virtuella Windows-skrivbordet beskrivs [klient och värden lagringspoolen skapa](troubleshoot-set-up-issues.md).
- Felsökning av problem när du konfigurerar en virtuell dator (VM) i virtuella Windows-skrivbordet beskrivs [Session Värdkonfiguration för virtuell dator](troubleshoot-vm-configuration.md).
- Felsökning av problem med virtuella skrivbord i Windows-klientanslutningar beskrivs [Remote Desktop-klientanslutningar](troubleshoot-client-connection.md).
- Läs mer om förhandsversionen av tjänsten i [Windows Desktop förhandsversionsmiljön](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Om du vill gå igenom en självstudiekurs om felsökning finns i [självstudien: Felsöka malldistributioner för Resource Manager-](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).