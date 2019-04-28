---
title: Skala en Azure-molntjänst i Windows PowerShell | Microsoft Docs
description: (klassisk) Lär dig hur du skalar en web-roll eller worker-roll in eller ut i Azure med hjälp av PowerShell.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: 240b34abd2f9b937c8a7ea449ad7f148870a9d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61433274"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Skala en molntjänst i PowerShell

Du kan använda Windows PowerShell för att skala en web-roll eller worker-roll in eller ut genom att lägga till eller ta bort instanser.  

## <a name="log-in-to-azure"></a>Logga in på Azure

Innan du kan utföra åtgärder i din prenumeration via PowerShell, måste du logga in:

```powershell
Add-AzureAccount
```

Om du har flera prenumerationer som är associerat med ditt konto, kan du behöva ändra den aktuella prenumerationen beroende på där Molntjänsten finns. Du kan kontrollera den aktuella prenumerationen genom att köra:

```powershell
Get-AzureSubscription -Current
```

Om du behöver ändra den aktuella prenumerationen, kör:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Kontrollera det aktuella instansantalet för din roll

Du kan kontrollera det aktuella tillståndet för din roll genom att köra:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Du bör få tillbaka information om rollen, inklusive dess nuvarande OS-version och instans antal. I det här fallet har rollen en enda instans.

![Information om rollen](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skala ut rollen genom att lägga till fler instanser

Om du vill skala ut din roll, skickar det önskade antalet instanser som den **antal** parametern till den **Set-AzureRole** cmdlet:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Cmdlet-block tillfälligt när nya instanser är etablerad och igång. Under denna tid om du öppnar en ny PowerShell-fönster och anropa **Get-AzureRole** som visades tidigare, visas det nya mål-instansantalet. Och om du inspektera Rollstatus i portalen, bör du se den nya instansen startas:

![VM-instans med början i portalen](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

När nya instanser har startat, returnerar cmdleten har:

![Rollen instans ökning lyckades](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skala i rollen genom att ta bort instanser

Du kan skala i en roll genom att ta bort instanser på samma sätt. Ange den **antal** parametern på **Set-AzureRole** till antal instanser som du vill ha när skalan i åtgärden har slutförts.

## <a name="next-steps"></a>Nästa steg

Det går inte att konfigurera automatisk skalning för molntjänster från PowerShell. Om du vill göra det, se [hur att automatiskt skala en molntjänst](cloud-services-how-to-scale-portal.md).
