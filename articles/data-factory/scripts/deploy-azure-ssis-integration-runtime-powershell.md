---
title: PowerShell-skript – distribuera Azure-SSIS integration runtime | Microsoft Docs
description: Det här PowerShell-skriptet skapar en Azure-SSIS integration runtime som kan köra SSIS-paket i molnet.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: a2d53ddb9f55942cd6ad396d42cc926f20c2d396
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574726"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell-skript – distribuera Azure-SSIS integration runtime

Det här Skriptexemplet för PowerShell skapar en Azure-SSIS integration runtime som kan köra dina SSIS-paket i Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Rensa distribution

När du kör exempelskriptet kan använda du följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Om du vill ta bort datafabriken från resursgruppen, kör du följande kommando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Skapar en Azure-SSIS integration runtime som kan köra SSIS-paket i molnet |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Startar Azure-SSIS integration runtime. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Hämtar information om Azure-SSIS integration runtime. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Factory PowerShell-skriptexempel finns i den [Azure Data Factory PowerShell-exempel](../samples-powershell.md).
