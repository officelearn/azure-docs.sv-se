---
title: "Skala en Azure-molntjänst i Windows PowerShell | Microsoft Docs"
description: "(klassisk) Lär dig hur du använder PowerShell för att skala en webbroll och en arbetsroll in eller ut i Azure."
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: a7ae8ff202d403dff19b8c9a6a09492235db27ac
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Så här skalar en tjänst i molnet i PowerShell

Du kan använda Windows PowerShell för att skala en webbroll eller arbetsrollen in eller ut genom att lägga till eller ta bort instanser.  

## <a name="log-in-to-azure"></a>Logga in på Azure

Du måste logga in innan du kan utföra några åtgärder på din prenumeration med hjälp av PowerShell:

```powershell
Add-AzureAccount
```

Om du har flera prenumerationer som är kopplade till ditt konto kan behöva du ändra den aktuella prenumerationen beroende på där Molntjänsten finns. Om du vill kontrollera den aktuella prenumerationen, kör du:

```powershell
Get-AzureSubscription -Current
```

Om du behöver ändra den aktuella prenumerationen kör:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Kontrollera den aktuella instanser för din roll

Om du vill kontrollera det aktuella tillståndet för din roll, kör du:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Du bör få tillbaka information om rollen, inklusive dess aktuella OS-version och instans antal. I det här fallet har rollen en enda instans.

![Information om rollen](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skala ut rollen genom att lägga till flera instanser

Om du vill skala upp din roll skicka antalet instanser som den **antal** parametern till den **Set AzureRole** cmdlet:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Cmdlet-block tillfälligt när nya instanser etablerad och igång. Under denna tid om du öppnar en ny PowerShell-fönster och anropet **Get-AzureRole** enligt tidigare, visas antal för nya mål-instans. Och om du inspektera Rollstatus i portalen, bör du se den nya instansen skulle startas:

![VM-instans med början i portalen](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

När nya instanser har startat, returnerar cmdleten har:

![Rollen instans öka lyckades](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skala i rollen genom att ta bort instanser

Du kan skala i en roll genom att ta bort instanser på samma sätt. Ange den **antal** parameter på **Set AzureRole** till antal instanser som du vill ha när skalan i åtgärden har slutförts.

## <a name="next-steps"></a>Nästa steg

Det går inte att konfigurera Autoskala för molntjänster från PowerShell. För att göra det, se [så att automatiskt skala en tjänst i molnet](cloud-services-how-to-scale-portal.md).
