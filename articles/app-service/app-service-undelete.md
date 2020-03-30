---
title: Återställa borttagna appar
description: Läs om hur du återställer en borttagen app i Azure App Service. Undvik huvudvärk i en app som tagits bort av misstag.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689608"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Återställa borttagen App Service-app med hjälp av PowerShell

Om du råkat ta bort din app i Azure App Service av misstag kan du återställa den med kommandona från [Az PowerShell-modulen](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Borttagna appar rensas från systemet 30 dagar efter den första borttagningen. När en app har rensats kan den inte återställas.
>

## <a name="re-register-app-service-resource-provider"></a>Registrera om apptjänstresursleverantören
Vissa kunder kan stöta på ett problem där det går inte att hämta listan över borttagna appar. LÃ¶s problemet genom att köra följande kommando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Lista borttagna appar

Om du vill hämta samlingen av `Get-AzDeletedWebApp`borttagna appar kan du använda .

Om du vill ha mer information om en viss app som du kan använda:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Den detaljerade informationen omfattar:

- **DeletedSiteId**: Unik identifierare för appen, som används för scenarier där flera appar med samma namn har tagits bort
- **SubscriptionID**: Prenumeration som innehåller den borttagna resursen
- **Plats**: Plats för den ursprungliga appen
- **ResourceGroupName**: Namnet på den ursprungliga resursgruppen
- **Namn**: Namnet på den ursprungliga appen.
- **Platsplats:** namnet på platsen.
- **Borttagningstid**: När togs appen bort  

## <a name="restore-deleted-app"></a>Återställa borttagen app

När appen som du vill återställa har identifierats `Restore-AzDeletedWebApp`kan du återställa den med .

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Ingångarna för kommandot är:

- **Resursgrupp**: Målgrupp för resursgrupp där appen ska återställas
- **Namn**: Namn för appen, bör vara globalt unikt.
- **TargetAppServicePlanName**: App Service-plan kopplad till appen

Som `Restore-AzDeletedWebApp` standard återställs både appkonfigurationen och ett innehåll. Om du bara vill återställa innehåll `-RestoreContentOnly` använder du flaggan med det här kommandoletet.

> [!NOTE]
> Om appen var värd och sedan tas bort från en App Service-miljö kan den bara återställas om motsvarande App Service-miljö fortfarande finns.
>

Du hittar den fullständiga kommandoreferensen här: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
