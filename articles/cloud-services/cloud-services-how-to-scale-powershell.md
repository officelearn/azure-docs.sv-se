---
title: Skala en Azure-molntjänst i Windows PowerShell | Microsoft-dokument
description: (klassisk) Lär dig hur du använder PowerShell för att skala en webbroll eller arbetarroll i eller ut i Azure.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359035"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Så här skalar du en molntjänst i PowerShell

Du kan använda Windows PowerShell för att skala en webbroll eller arbetarroll in eller ut genom att lägga till eller ta bort instanser.  

## <a name="log-in-to-azure"></a>Logga in på Azure

Innan du kan utföra några åtgärder på din prenumeration via PowerShell måste du logga in:

```powershell
Add-AzureAccount
```

Om du har flera prenumerationer kopplade till ditt konto kan du behöva ändra den aktuella prenumerationen beroende på var molntjänsten finns. Om du vill kontrollera den aktuella prenumerationen kör du:

```powershell
Get-AzureSubscription -Current
```

Om du behöver ändra den aktuella prenumerationen kör du:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Kontrollera det aktuella antalet instanser för din roll

Om du vill kontrollera det aktuella tillståndet för din roll kör du:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Du bör få tillbaka information om rollen, inklusive dess aktuella OS-version och instansantal. I det här fallet har rollen en enda instans.

![Information om rollen](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skala ut rollen genom att lägga till fler instanser

Om du vill skala ut din roll skickar du önskat antal instanser som **parametern Count** till **cmdlet set-AzureRole:**

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Cmdletblocken tillfälligt medan de nya instanserna etableras och startas. Under den här tiden, om du öppnar ett nytt PowerShell-fönster och **anropar Get-AzureRole** som tidigare, ser du antalet nya målinstanser. Och om du inspekterar rollstatusen i portalen bör du se den nya instansen starta:

![VM-instans som startar i portalen](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

När de nya instanserna har startat returneras cmdleten:

![Rollinstansen ökar framgången](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skala i rollen genom att ta bort instanser

Du kan skala i en roll genom att ta bort instanser på samma sätt. Ange parametern **Count** på **Set-AzureRole** till antalet instanser som du vill ha när skalan i drift är klar.

## <a name="next-steps"></a>Nästa steg

Det går inte att konfigurera automatisk skalning för molntjänster från PowerShell. Det gör du genom att se [Så här skalar du en molntjänst automatiskt](cloud-services-how-to-scale-portal.md).
