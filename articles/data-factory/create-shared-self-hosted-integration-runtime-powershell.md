---
title: Skapa en delad lokal integration runtime i Azure Data Factory med PowerShell | Microsoft Docs
description: Lär dig hur du skapar en delad lokal integration runtime i Azure Data Factory, som gör att flera fabriker dataåtkomst integration runtime.
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
ms.openlocfilehash: d7f3fbcb3235c8c620876e68a62f3e491770779d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252140"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>Skapa en delad lokal integration runtime i Azure Data Factory med PowerShell

Den här stegvisa guiden visar hur du skapar en delad lokal integration runtime (IR) i Azure Data Factory med hjälp av Azure PowerShell. Du kan använda delade lokal integration runtime i en annan data factory. I den här självstudien gör du följande: 

1. Skapa en datafabrik. 
1. Skapa Integration Runtime med egen värd.
1. Dela en lokal integration runtime med andra datafabriker.
1. Skapa en länkad integration runtime.
1. Återkalla delning.

## <a name="prerequisites"></a>Förutsättningar 

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar. 

- **Azure PowerShell**. Följ instruktionerna i [installera Azure PowerShell på Windows](/powershell/azure/install-azurerm-ps). Du kan använda PowerShell för att köra ett skript för att skapa en lokal integration runtime som kan delas med andra datafabriker. 

> [!NOTE]
> För en lista över Azure-regioner som Data Factory är för närvarande tillgängligt, väljer du de regioner som intresserar dig på följande sida: [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta Windows PowerShell ISE.

1. Skapa variabler.

    Kopiera och klistra in följande skript och Ersätt variabler (SubscriptionName, ResourceGroupName osv.) med faktiska värden. 

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

1. Logga in och välj en prenumeration.

    Lägg till följande kod till skriptet för att logga in och välj din Azure-prenumeration:

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. Skapa en resursgrupp och en Data Factory.

    *(Det här steget är valfritt. Om du redan har en data factory, hoppa över detta steg.)* 

    Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på WestEurope plats. 

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

*(Det här steget är valfritt. Om du redan har en lokal integration runtime som du vill dela med andra datafabriker hoppa över detta steg.)*

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

*(Det här steget är valfritt. Om du redan har data factory som du vill dela, hoppa över detta steg.)*

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>Bevilja behörighet

Bevilja behörighet till Data Factory som behöver åtkomst till den lokala integreringskörningen du skapat och registrerad.

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

Om du vill återkalla åtkomst till en data factory från att komma åt delade integration runtime kan du köra följande kommando:

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Du kan köra följande kommando på delade integration runtime för att ta bort den befintliga länkade integration runtime:

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>Nästa steg

- Granska integration runtime-koncept i [Integration runtime i Azure Data Factory](concepts-integration-runtime.md).

- Lär dig hur du skapar en lokal integration runtime i Azure-portalen i [skapa och konfigurera en lokal integration runtime](create-self-hosted-integration-runtime.md).
