---
title: Återställ borttagna App Service appar – Azure App Service
description: Lär dig hur du återställer en borttagen App Service-app med hjälp av PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: a766f7ed7a82874df8ef7506226de0d6f38a5847
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219546"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Återställa borttagna App Service-appen med PowerShell

Om du har råkat ta bort din app av misstag i Azure App Service kan du återställa den med hjälp av kommandona från [PowerShell-modulen AZ](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Visa borttagna appar

Om du vill hämta samlingen med borttagna appar kan du `Get-AzDeletedWebApp`använda.

För information om en speciell borttagen app kan du använda:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Den detaljerade informationen innehåller:

- **DeletedSiteId**: Unikt ID för appen, används för scenarier där flera appar med samma namn har tagits bort
- **SubscriptionID**: Prenumeration som innehåller den borttagna resursen
- **Plats**: Plats för den ursprungliga appen
- **ResourceGroupName**: Namn på den ursprungliga resurs gruppen
- **Namn på**: Namnet på den ursprungliga appen.
- **Fack**: namnet på platsen.
- **Borttagnings tid**: När togs appen bort  

## <a name="restore-deleted-app"></a>Återställ borttagen app

När den app du vill återställa har identifierats kan du återställa den med hjälp av `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Kommandot Inputs for är:

- **Resursgrupp**: Mål resurs grupp där appen kommer att återställas
- **Namn på**: Namnet på appen måste vara globalt unikt.
- **TargetAppServicePlanName**: App Service plan länkat till appen

Som standard `Restore-AzDeletedWebApp` återställs både din app-konfiguration och ett innehåll. Om du bara vill återställa innehållet använder `-RestoreContentOnly` du flaggan med den här kommandot.
