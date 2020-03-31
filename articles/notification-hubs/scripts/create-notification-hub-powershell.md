---
title: Skapa en Azure-meddelandehubb med PowerShell | Microsoft-dokument
description: Lär dig hur du använder ett PowerShell-skript för att skapa en Azure-meddelandehubb.
author: dimazaid
manager: femila
services: notification-hubs
editor: sethmanheim
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2020
ms.author: dimazaid
ms.openlocfilehash: 01749e1ae5c29a7e77b2143939e25cf1ccd1aec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263989"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Använda PowerShell för att skapa en Azure-meddelandehubb

Det här exemplet powershell-skript skapar ett exempel på Azure-meddelandehubb. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]

## <a name="clean-up-deployment"></a>Rensa distribution

När du har kört exempelskriptet kan du använda följande kommando för att ta bort resursgruppen och alla resurser som är associerade med det:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Nytt-AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | Skapar ett namnområde för meddelandehubben. |
| [Ny-AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | Skapar en meddelandehubb. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).
