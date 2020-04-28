---
title: Skala en Azure-molnbaserad tjänst i Windows PowerShell | Microsoft Docs
description: form Lär dig hur du använder PowerShell för att skala en webb roll eller arbets roll i Azure.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "68359035"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Så här skalar du en moln tjänst i PowerShell

Du kan använda Windows PowerShell för att skala en webb roll eller arbets roll i eller ut genom att lägga till eller ta bort instanser.  

## <a name="log-in-to-azure"></a>Logga in på Azure

Innan du kan utföra några åtgärder för din prenumeration via PowerShell måste du logga in:

```powershell
Add-AzureAccount
```

Om du har flera prenumerationer som är kopplade till ditt konto kan du behöva ändra den aktuella prenumerationen beroende på var moln tjänsten finns. Om du vill kontrol lera den aktuella prenumerationen kör du:

```powershell
Get-AzureSubscription -Current
```

Om du behöver ändra den aktuella prenumerationen kör du:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Kontrol lera det aktuella instans antalet för rollen

Kör följande för att kontrol lera den aktuella status för din roll:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Du bör få tillbaka information om rollen, inklusive dess aktuella OS-version och antalet instanser. I det här fallet har rollen en enda instans.

![Information om rollen](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skala ut rollen genom att lägga till fler instanser

Du kan skala ut rollen genom att skicka det önskade antalet instanser som **Count** -parametern till **set-AzureRole** -cmdleten:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Cmdleten blockerar tillfälligt medan de nya instanserna har tillhandahållits och startats. Om du i det här fallet öppnar ett nytt PowerShell-fönster och anropar **Get-AzureRole** som visas tidigare visas antalet nya mål instanser. Och om du inspekterar roll status i portalen bör du se att den nya instansen startar:

![VM-instans som startar i portalen](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

När de nya instanserna har startat kommer cmdleten att returnera:

![Roll instans ökning slutförd](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skala i rollen genom att ta bort instanser

Du kan skala i en roll genom att ta bort instanser på samma sätt. Ange **Count** -parametern på **set-AzureRole** till det antal instanser som du vill ha när skalningen har slutförts.

## <a name="next-steps"></a>Nästa steg

Det går inte att konfigurera automatisk skalning för moln tjänster från PowerShell. Information om hur du gör det finns i [så här skalar du automatiskt en moln tjänst](cloud-services-how-to-scale-portal.md).
