---
title: Hantera app-grupper för Windows Virtual Desktop – Azure
description: Beskriver hur du konfigurerar Windows virtuella Skriv bords klienter i Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: e158c0a6090493bec0169c144f030300de921516
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679461"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Självstudier: Hantera app-grupper för virtuellt Windows-skrivbord

Standard-app-gruppen som skapas för en ny Windows-pool för virtuella skriv bord publicerar också hela Skriv bordet. Dessutom kan du skapa en eller flera RemoteApp-programgrupper för poolen. Följ den här självstudien för att skapa en RemoteApp-app-grupp och publicera enskilda **Start** meny-appar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en RemoteApp-grupp.
> * Bevilja åtkomst till RemoteApp-program.

Innan du börjar kan du [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Skapa en RemoteApp-grupp

1. Kör följande PowerShell-cmdlet för att skapa en ny tom RemoteApp-app.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Valfritt För att kontrol lera att app-gruppen har skapats kan du köra följande cmdlet för att se en lista över alla app-grupper för poolen.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Kör följande cmdlet för att hämta en lista över **Start** -menyns appar på värddatorns avbildning av den virtuella datorn. Skriv ned värdena för **sökväg**, **IconPath**, **IconIndex**och annan viktig information för det program som du vill publicera.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Kör följande cmdlet för att installera programmet baserat på `AppAlias`. `AppAlias` blir synliga när du kör utdata från steg 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Valfritt Kör följande cmdlet för att publicera ett nytt RemoteApp-program till den program grupp som skapades i steg 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Verifiera att appen har publicerats genom att köra följande cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Upprepa steg 1 – 5 för varje program som du vill publicera för den här program gruppen.
8. Kör följande cmdlet för att ge användarna åtkomst till RemoteApp-programmen i app-gruppen.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en app-grupp, fyller den med RemoteApp-program och tilldelar användare till app-gruppen. Information om hur du skapar en pool för validerings värdar finns i följande självstudie. Du kan använda en pool för validerings värdar för att övervaka tjänst uppdateringar innan du återställer dem till din produktions miljö.

> [!div class="nextstepaction"]
> [Skapa en adresspool för att verifiera tjänst uppdateringar](./create-validation-host-pool.md)
