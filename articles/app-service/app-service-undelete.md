---
title: Återställ borttagna appar
description: Lär dig hur du återställer en borttagen app i Azure App Service. Undvik att ta bort en app som har tagits bort av misstag.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c3c79944aa4add0a32dbb584b13606e32e146a1a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050302"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Återställa borttagen App Service-app med hjälp av PowerShell

Om du har råkat ta bort din app av misstag i Azure App Service kan du återställa den med hjälp av kommandona från [PowerShell-modulen AZ](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Borttagna appar rensas från systemet 30 dagar efter den första borttagningen. När en app har rensats kan den inte återställas.
>

> [!NOTE]
> Funktionen för att ångra borttagning stöds inte för förbruknings planen.
>

## <a name="re-register-app-service-resource-provider"></a>Registrera App Service Resource Provider igen
Vissa kunder kan komma över ett problem där det inte går att hämta listan över borttagna appar. Lös problemet genom att köra följande kommando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lista över borttagna appar

Om du vill hämta samlingen med borttagna appar kan du använda `Get-AzDeletedWebApp` .

För information om en speciell borttagen app kan du använda:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Den detaljerade informationen innehåller:

- **DeletedSiteId**: unik identifierare för appen som används för scenarier där flera appar med samma namn har tagits bort
- **SubscriptionID**: prenumerationen som innehåller den borttagna resursen
- **Plats**: den ursprungliga appens plats
- **ResourceGroupName**: namnet på den ursprungliga resurs gruppen
- **Namn**: namnet på den ursprungliga appen.
- **Fack**: namnet på platsen.
- **Borttagnings tid**: när appen togs bort  

## <a name="restore-deleted-app"></a>Återställ borttagen app
>[!NOTE]
> `Restore-AzDeletedWebApp`stöds inte för Function-appar.

När den app du vill återställa har identifierats kan du återställa den med hjälp av `Restore-AzDeletedWebApp` .

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Distributions platser återställs inte som en del av din app. Om du behöver återställa en mellanlagringsplats använder du- `-Slot <slot-name>` flaggan.
>

Kommandot Inputs for är:

- **Mål resurs grupp**: mål resurs grupp där appen kommer att återställas
- **Namn**: appens namn måste vara globalt unik.
- **TargetAppServicePlanName**: App Service plan länkade till appen

Som standard `Restore-AzDeletedWebApp` återställs både din app-konfiguration och allt innehåll. Om du bara vill återställa innehållet använder du `-RestoreContentOnly` flaggan med den här kommandot.

> [!NOTE]
> Om appen fanns på och sedan togs bort från en App Service-miljön kan den bara återställas om motsvarande App Service-miljön fortfarande finns.
>

Du hittar den fullständiga kommandot-referensen här: [restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
