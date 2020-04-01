---
title: Hantera appgrupper för Windows Virtual Desktop – Azure
description: I artikeln beskrivs hur du konfigurerar Windows Virtual Desktop-klienter i Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9a9d92ea525c6b5a64fdf7cc74babdce6a97f923
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127805"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Självstudiekurs: Hantera appgrupper för Windows Virtual Desktop

Standardappgruppen som skapats för en ny värdpool för Windows Virtual Desktop publicerar också hela skrivbordet. Dessutom kan du skapa en eller flera RemoteApp-programgrupper för värdpoolen. Följ den här självstudien om du **Start** vill skapa en RemoteApp-appgrupp och publicera enskilda Start-menyappar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en RemoteApp-grupp.
> * Bevilja åtkomst till RemoteApp-program.

Innan du börjar [laddar du ned och importerar Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan har gjort det. Därefter kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Skapa en RemoteApp-grupp

1. Kör följande PowerShell-cmdlet för att skapa en ny tom RemoteApp-appgrupp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Valfritt) Om du vill kontrollera att appgruppen har skapats kan du köra följande cmdlet för att se en lista över alla appgrupper för värdpoolen.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Kör följande cmdlet för att **Start** få en lista över Start-menyappar på värdpoolens avbildning för virtuella datorer. Skriv ned värdena för **FilePath,** **IconPath**, **IconIndex**och annan viktig information för det program som du vill publicera.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Kör följande cmdlet för att `AppAlias`installera programmet baserat på . `AppAlias`blir synlig när du kör utdata från steg 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Valfritt) Kör följande cmdlet för att publicera ett nytt RemoteApp-program till den programgrupp som skapades i steg 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Om du vill kontrollera att appen har publicerats kör du följande cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Upprepa steg 1–5 för varje program som du vill publicera för den här appgruppen.
8. Kör följande cmdlet för att ge användarna åtkomst till RemoteApp-programmen i appgruppen.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar en appgrupp, fyller i den med RemoteApp-program och tilldelar användare till appgruppen. Mer information om hur du skapar en verifieringsvärdpool finns i följande självstudiekurs. Du kan använda en verifieringsvärdpool för att övervaka tjänstuppdateringar innan du distribuerar dem till produktionsmiljön.

> [!div class="nextstepaction"]
> [Skapa en värdpool för att verifiera tjänstuppdateringar](./create-validation-host-pool.md)
