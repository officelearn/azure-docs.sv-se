---
title: 'PowerShell-skript: Skapa en Azure notification hub | Microsoft Docs'
description: Det här PowerShell-skriptet skapar en Azure notification hub.
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d1d9628c073b298b6e01f044abfde8b1d40ece2d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245686"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Använd PowerShell för att skapa en Azure notification hub

Det här Skriptexemplet för PowerShell skapar en Azure notification hub som exempel. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Rensa distribution

När du kör exempelskriptet kan använda du följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzNotificationHubsNamespace](/powershell/module//azurerm.notificationhubs/new-Aznotificationhubsnamespace) | Skapar ett namnområde för meddelandehubben. |
| [New-AzNotificationHub](/powershell/module//azurerm.notificationhubs/new-Aznotificationhubsnamespace) | Skapar en notification hub. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).
