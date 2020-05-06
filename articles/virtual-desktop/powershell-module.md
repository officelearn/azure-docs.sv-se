---
title: PowerShell-modul Windows Virtual Desktop – Azure
description: Installera och konfigurera PowerShell-modulen för virtuella Windows-datorer.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 54501e7e00ba8a28dd7cb421232b9a9587604338
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653134"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Konfigurera PowerShell-modulen för virtuellt Windows-skrivbord

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt.
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. 
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Windows Virtual Desktop PowerShell-modulen är integrerad i Azure PowerShell-modulen. I den här artikeln får du lära dig hur du konfigurerar PowerShell-modulen så att du kan köra cmdlets för virtuella Windows-datorer.

## <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön

Börja med att installera den [senaste versionen av PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)för att komma igång med att använda modulen. Windows Virtual Desktop-cmdletar fungerar för närvarande bara med PowerShell Core.

Sedan måste du installera DesktopVirtualization-modulen som ska användas i din PowerShell-session.

Kör följande PowerShell-cmdlet i förhöjd läge för att installera modulen:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Om den här cmdleten inte fungerar kan du försöka köra den igen med förhöjd behörighet.

Kör sedan följande cmdlet för att ansluta till Azure:

```powershell
Connect-AzAccount
```

Om du loggar in på ditt Azure-konto krävs en kod som genereras när du kör Connect-cmdleten. Logga in genom att gå till <https://microsoft.com/devicelogin>, ange koden och logga in med dina autentiseringsuppgifter för Azure-administratören.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Då loggas du in direkt i den prenumeration som är standard för dina administratörs uppgifter.

## <a name="change-the-default-subscription"></a>Ändra standard prenumerationen

Om du vill ändra standard prenumerationen efter att du har loggat in, kör du denna cmdlet:

```powershell
Select-AzureSubscription -SubscriptionName <preferredsubscriptionname>
```

När du väljer en ny prenumeration som du vill använda, behöver du inte ange den prenumerationens ID i cmdletar som du kör efteråt. Följande cmdlet hämtar t. ex. en speciell värd för sessionen utan att behöva prenumerations-ID:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Du kan också ändra prenumerationer per cmdlet-basis genom att lägga till önskat prenumerations namn som en parameter. Nästa cmdlet är detsamma som i föregående exempel, förutom med det prenumerations-ID som har lagts till som en parameter för att ändra vilken prenumeration som cmdleten använder.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Hämta platser

Parametern Location är obligatorisk för alla **New-AzWVD-** cmdletar som skapar nya objekt.

Kör följande cmdlet för att hämta en lista över platser som din prenumeration stöder:

```powershell
Get-AzLocation
```

Utdata för **Get-AzLocation** ser ut så här:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

När du känner till ditt kontos plats kan du använda det i en-cmdlet. Här är till exempel en cmdlet som skapar en adresspool på platsen "Sydostasien":

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat din PowerShell-modul kan du köra-cmdlet: ar för att utföra alla typer av saker i det virtuella Windows-skrivbordet. Här är några platser som du kan använda modulen:

- Kör genom våra [Windows-guider för virtuella datorer]() för att konfigurera din egen Windows-miljö för virtuella skriv bord.
- [Skapa en värdpool med PowerShell](create-host-pools-powershell.md)
- [Konfigurera metoden för belastningsutjämning för Windows Virtual Desktop](configure-host-pool-load-balancing.md)
- [Konfigurera tilldelnings typen egen Skriv bords värd pool](configure-host-pool-personal-desktop-assignment-type.md)
- och mycket annat!

Om du stöter på problem kan du läsa [artikeln om PowerShell-felsökning](troubleshoot-powershell.md) för hjälp.

