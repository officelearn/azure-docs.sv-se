---
title: 'PowerShell-skript: skapa ett Azure notification hub | Microsoft Docs'
description: Detta PowerShell-skript skapar ett Azure notification hub.
services: data-factory
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 747d743a0573bd959b4d3c7100be8ae9451c5ed5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790981"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Använd PowerShell för att skapa ett Azure notification hub

PowerShell-exempelskriptet skapar en exemplet Azure notification hub. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration** - om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Rensa distribution

Du kan använda följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den när du har kört exempelskript:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Anteckningar |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Ny AzureRmNotificationHubsNamespace](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | Skapar ett namnområde för meddelandehubben. |
| [Ny AzureRmNotificationHub](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | Skapar en meddelandehubb. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).
