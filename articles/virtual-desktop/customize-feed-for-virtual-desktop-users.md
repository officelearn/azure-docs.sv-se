---
title: Anpassa feed för virtuella Windows-skrivbordet användare – Azure
description: Anpassa feed för virtuella Windows-användare med PowerShell-cmdletar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128083"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Anpassa feed för Windows Virtual Desktop-användare

Du kan anpassa flödet så att RemoteApp-och fjärr skrivbords resurserna visas på ett igenkännbart sätt för dina användare.

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Anpassa visnings namnet för en RemoteApp

Du kan ändra visnings namnet för en publicerad RemoteApp genom att ange det egna namnet. Som standard är det egna namnet samma som namnet på RemoteApp-programmet.

Om du vill hämta en lista över publicerade RemoteApp-program för en app-grupp kör du följande PowerShell-cmdlet:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/get-rdsremoteapp.png)

Om du vill tilldela ett eget namn till en RemoteApp kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![En skärm bild av PowerShell-cmdleten Set-RDSRemoteApp med namnet och nytt FriendlyName har marker ATS.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Anpassa visnings namnet för ett fjärr skrivbord

Du kan ändra visnings namnet för ett publicerat fjärr skrivbord genom att ange ett eget namn. Om du har skapat en pool för värdar och skriv bord manuellt via PowerShell är standard namnet "session Desktop". Om du har skapat en adresspool och en grupp för Skriv bords appar via GitHub Azure Resource Manager-mallen eller Azure Marketplace-erbjudandet, är standard namnet samma som namnet på värddatorn.

Kör följande PowerShell-cmdlet för att hämta fjärr skrivbords resursen:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/get-rdsremotedesktop.png)

Om du vill tilldela fjärr skrivbords resursen ett eget namn kör du följande PowerShell-cmdlet:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![En skärm bild av PowerShell-cmdleten Set-RDSRemoteApp med namnet och nytt FriendlyName har marker ATS.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat feeden för användare kan du logga in på en Windows Virtual Desktop-klient för att testa den. Det gör du genom att fortsätta till Windows Virtual Desktop-instruktionen how-TOS:
    
 * [Anslut från Windows 10 eller Windows 7](connect-windows-7-and-10.md)
 * [Anslut från en webbläsare](connect-web.md) 
