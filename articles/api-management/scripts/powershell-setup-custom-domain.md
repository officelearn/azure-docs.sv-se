---
title: "Azure PowerShell skriptexempel - ställa in anpassad domän | Microsoft Docs"
description: "Azure PowerShell skriptexempel - ställa in anpassad domän"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 11/16/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 484805d1e31cd115d9b8a7c8ae9fdbc5899d932b
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="set-up-custom-domain"></a>Konfigurera anpassade domäner

Det här exempelskriptet ställer in anpassad domän för proxy och portalen slutpunkten för API Management-tjänsten.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/api-management/setup-custom-domain/setup_custom_domain.ps1 "Set up custom domain")]

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) kommandot för att ta bort resursgruppen och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure API Management finns i den [PowerShell-exempel](../powershell-samples.md).
