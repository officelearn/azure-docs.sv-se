---
title: Anpassa feed för virtuellt skrivbord i Windows - användare i Azure
description: Anpassa flödet för virtuellt skrivbord i Windows-användare med PowerShell-cmdletar.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 869760a12089ed7453199ad8a3fa18a6cca87511
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157095"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Anpassa feed för Windows Virtual Desktop-användare

Du kan anpassa flödet så RemoteApp- och remote desktop resurser visas i en identifierbara enkelt för användarna.

Först [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Anpassa visningsnamn för en RemoteApp

Du kan ändra visningsnamnet för en publicerade RemoteApp-genom att ange det egna namnet. Som standard är det egna namnet samma som namnet på RemoteApp-program.

Kör följande PowerShell-cmdlet för att hämta en lista över publicerade RemoteApps för en appgrupp:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![En skärmbild av PowerShell-cmdlet Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/get-rdsremoteapp.png)

Om du vill tilldela ett eget namn till en RemoteApp, kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![En skärmbild av PowerShell-cmdlet Set-RDSRemoteApp med namn och ny FriendlyName markerat.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Anpassa visningsnamn för en anslutning till fjärrskrivbord

Du kan ändra visningsnamnet för en publicerad fjärrskrivbord genom att ange ett eget namn. Om du manuellt skapade en värd pool och skrivbordsapp grupp via PowerShell är standard egna namn ”Session Desktop”. Om du har skapat en värd pool och skrivbordsapp grupp via GitHub Azure Resource Manager-mall eller Azure Marketplace erbjuder är standard egna namn samma som värdnamn för poolen.

Kör följande PowerShell-cmdlet för att hämta remote desktop resursen:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![En skärmbild av PowerShell-cmdlet Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/get-rdsremotedesktop.png)

Om du vill tilldela ett eget namn fjärresursen för fjärrskrivbord, kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![En skärmbild av PowerShell-cmdlet Set-RDSRemoteApp med namn och ny FriendlyName markerat.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat flöde för användare kan logga du in till ett virtuellt skrivbord i Windows-klienten att testa processen. Du gör detta genom att fortsätta att ansluta till virtuella skrivbord instruktioner för Windows:
    
 * [Ansluta från Windows 10 eller Windows 7](connect-windows-7-and-10.md)
 * [Ansluta från en webbläsare](connect-web.md) 
