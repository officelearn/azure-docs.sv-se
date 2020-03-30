---
title: Anpassa flödet för Windows Virtual Desktop-användare - Azure
description: Anpassa flödet för Windows Virtual Desktop-användare med PowerShell-cmdletar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128083"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Anpassa feed för Windows Virtual Desktop-användare

Du kan anpassa flödet så att RemoteApp- och fjärrskrivbordsresurserna visas på ett igenkännligt sätt för användarna.

Hämta och importera först [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det. Därefter kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Anpassa visningsnamnet för en RemoteApp

Du kan ändra visningsnamnet för en publicerad RemoteApp genom att ange det egna namnet. Som standard är det egna namnet detsamma som namnet på RemoteApp-programmet.

Om du vill hämta en lista över publicerade RemoteApps för en appgrupp kör du följande PowerShell-cmdlet:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![En skärmbild av PowerShell cmdlet Get-RDSRemoteApp med Namn och FriendlyName markerat.](media/get-rdsremoteapp.png)

Om du vill tilldela ett eget namn till en RemoteApp kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![En skärmbild av PowerShell cmdlet Set-RDSRemoteApp med Namn och Nytt FriendlyName markerat.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Anpassa visningsnamnet för ett fjärrskrivbord

Du kan ändra visningsnamnet för ett publicerat fjärrskrivbord genom att ange ett eget namn. Om du manuellt har skapat en värdpool och skrivbordsappgrupp via PowerShell är standardnamnet "Session Desktop". Om du har skapat en värdpool och skrivbordsappgrupp via GitHub Azure Resource Manager-mallen eller Azure Marketplace-erbjudandet är standardvänliga namn detsamma som värdpoolnamnet.

Om du vill hämta fjärrskrivbordsresursen kör du följande PowerShell-cmdlet:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![En skärmbild av PowerShell cmdlet Get-RDSRemoteApp med Namn och FriendlyName markerat.](media/get-rdsremotedesktop.png)

Om du vill tilldela ett eget namn till fjärrskrivbordsresursen kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![En skärmbild av PowerShell cmdlet Set-RDSRemoteApp med Namn och Nytt FriendlyName markerat.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat flödet för användare kan du logga in på en Windows Virtual Desktop-klient för att testa det. Det gör du genom att fortsätta till hur du gör i anslutning till Windows Virtuellt skrivbord:
    
 * [Ansluta från Windows 10 eller Windows 7](connect-windows-7-and-10.md)
 * [Ansluta från en webbläsare](connect-web.md) 
