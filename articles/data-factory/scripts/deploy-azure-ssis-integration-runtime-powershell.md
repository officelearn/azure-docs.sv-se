---
title: Distribuera Azure SSIS-integreringskörning med PowerShell
description: Det här PowerShell-skriptet skapar en Azure-SSIS-integreringskörning som kan köra SSIS-paket i molnet.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: sawinark
author: swinarko
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c6d9a9299fa25013a440ee6ac45f5eae407225b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929808"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShell-skript – distribuera Azure-SSIS-integreringskörning

Det här exemplet powershell-skript skapar en Azure-SSIS-integreringskörning som kan köra dina SSIS-paket i Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

## <a name="clean-up-deployment"></a>Rensa distribution

När du har kört exempelskriptet kan du använda följande kommando för att ta bort resursgruppen och alla resurser som är associerade med det:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Om du vill ta bort datafabriken från resursgruppen kör du följande kommando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Skapar en Azure-SSIS-integreringskörning som kan köra SSIS-paket i molnet |
| [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Startar Azure-SSIS-integreringskörningen. |
| [Hämta-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Hämtar information om Azure-SSIS-integreringskörningen. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Factory PowerShell-skriptexempel finns i [Azure Data Factory PowerShell-exemplen](../samples-powershell.md).
