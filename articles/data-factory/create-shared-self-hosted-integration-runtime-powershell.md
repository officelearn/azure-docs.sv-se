---
title: Skapa en delad lokal integration runtime i Azure Data Factory med PowerShell | Microsoft Docs
description: Lär dig mer om att skapa en delad lokal integration runtime i Azure Data Factory, så att flera olika datafabriker för som har åtkomst till integration runtime.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: b32ea4293daa9206c6b0da4bdee777677c5d340d
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685522"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Skapa en delad lokal integration runtime i Azure Data Factory med PowerShell

Den här stegvisa guiden visar hur du skapar en delad lokal integration runtime i Azure Data Factory med hjälp av Azure PowerShell. Du kan använda delade lokal integration runtime i en annan data factory. I den här självstudien gör du följande: 

1. Skapa en datafabrik. 
1. Skapa Integration Runtime med egen värd.
1. Dela en lokal integration runtime med andra datafabriker.
1. Skapa en länkad integration runtime.
1. Återkalla delning.

## <a name="prerequisites"></a>Förutsättningar 

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar. 

- **Azure PowerShell**. Följ instruktionerna i [installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.11.0). Du kan använda PowerShell för att köra ett skript för att skapa en lokal integration runtime som kan delas med andra datafabriker. 

> [!NOTE]  
> Välj de regioner som intresserar dig på en lista över Azure-regioner som Data Factory är för närvarande tillgängligt [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta Windows PowerShell ISE.

1. Skapa variabler. Kopiera och klistra in följande skript. Ersätt variabler, till exempel **SubscriptionName** och **ResourceGroupName**, med faktiska värden: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Logga in och välj en prenumeration. Lägg till följande kod till skriptet för att logga in och välj din Azure-prenumeration:

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Skapa en resursgrupp och en data factory.

    > [!NOTE]  
    > Det här steget är valfritt. Om du redan har en data factory kan du hoppa över det här steget. 

    Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med hjälp av den [New-AzureRmResourceGroup](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.11.0) kommando. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på WestEurope plats: 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Skapa en datafabrik genom att köra följande kommandon: 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

> [!NOTE]  
> Det här steget är valfritt. Om du redan har en lokal integration runtime som du vill dela med andra datafabriker kan du hoppa över det här steget.

Kör följande kommando för att skapa en lokal integration runtime:

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Hämta nyckel för integration runtime-autentisering och registrera en nod

Kör följande kommando för att hämta autentiseringsnyckeln för lokal integration runtime:

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Svaret innehåller autentiseringsnyckeln för den här lokal integration runtime. Du kan använda den här nyckeln när du registrerar noden för integration runtime.

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Installera och registrera den lokala integreringskörningen

1. Hämta lokal integration runtime-installationsprogrammet från [Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Kör installationsprogrammet för att installera IR på en lokal dator.

3. Registrera den nya IR med den autentiseringsnyckel som du hämtade i föregående steg.

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Dela en lokal integration runtime med en annan data factory

### <a name="create-another-data-factory"></a>Skapa en annan datafabrik

> [!NOTE]  
> Det här steget är valfritt. Hoppa över det här steget om du redan har data factory som du vill dela med.

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Bevilja behörighet

Bevilja behörighet till data factory som behöver åtkomst till den lokala integreringskörningen du skapat och registrerad.

> [!IMPORTANT]  
> Hoppa inte över det här steget!

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>Skapa en länkad lokal integration runtime

Kör följande kommando för att skapa en länkad lokal integration runtime:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Nu kan du använda den här länkade integration runtime i alla länkade tjänsten. Länkade integreringskörningen använder delade integration runtime för att köra aktiviteter.

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Återkalla delning från en data factory-integreringskörning

Om du vill återkalla åtkomst till en data factory från delade integration runtime kör du följande kommando:

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Kör följande kommando för att ta bort befintliga länkade integreringskörningen mot delade integration runtime:

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Nästa steg

- Granska [integration runtime-koncept i Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/concepts-integration-runtime).

- Lär dig hur du [skapa en lokal integration runtime i Azure-portalen](https://docs.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime).
