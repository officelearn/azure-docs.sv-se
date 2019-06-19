---
title: Hantera app-grupper för Windows Virtual Desktop Preview – Azure
description: Beskriver hur du ställer in förhandsversion för virtuella skrivbord i Windows-klienter i Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 73425df1f0cfedd2a681650fc2b536a652b621d5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206675"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Självstudier: Hantera app-grupper för Windows Virtual Desktop-förhandsversion

Standardgrupp för app som skapats för en ny pool för förhandsversion för virtuella skrivbord i Windows-värd publicerar även hela skrivbordet. Dessutom kan du skapa en eller flera RemoteApp programgrupper för värd-poolen. Den här kursen för att skapa en grupp för RemoteApp-app och publicera enskilda **starta** menyn appar.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en RemoteApp-grupp.
> * Bevilja åtkomst till RemoteApp-program.

Innan du börjar [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

## <a name="create-a-remoteapp-group"></a>Skapa en RemoteApp-grupp

1. Kör följande PowerShell-cmdlet för att skapa en ny tom RemoteApp appgrupp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Valfritt) Du kan köra följande cmdlet om du vill se en lista över alla app-grupper för värden poolen för att kontrollera att app-gruppen har skapats.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Kör följande cmdlet för att hämta en lista över **starta** menyn appar på värden poolens avbildning av virtuell dator. Anteckna värdena för **FilePath**, **IconPath**, **IconIndex**, och annan viktig information för det program som du vill publicera.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Kör följande cmdlet för att installera programmet utifrån `AppAlias`. `AppAlias` visas när du kör utdata från steg 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Valfritt) Kör följande cmdlet för att publicera en ny RemoteApp-program i programgruppen som skapades i steg 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Kontrollera att appen har publicerats med följande cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Upprepa steg 1 – 5 för varje program som du vill publicera för den här app-gruppen.
8. Kör följande cmdlet för att ge användarna åtkomst till RemoteApp-program i app-gruppen.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa en appgrupp, fyller den med RemoteApp-program och tilldela användare till app-gruppen. Information om hur du skapar en pool för verifiering värden, se följande självstudie. Du kan använda poolen verifiering värden för att övervaka uppdateringar av tjänsten innan du distribuerar dem till produktionsmiljön.

> [!div class="nextstepaction"]
> [Skapa en pool med värden för att verifiera tjänstuppdateringar](./create-validation-host-pool.md)
