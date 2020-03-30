---
title: Skapa en delad självvärd integreringskörning med PowerShell
description: Lär dig hur du skapar en delad självvärd integreringskörning i Azure Data Factory, så att flera datafabriker kan komma åt integrationskörningen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 10/31/2018
ms.openlocfilehash: a2f24d8203ac5fb9724370cbdf4309bdc43c166a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444103"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Skapa en delad självvärd för integrationskörning i Azure Data Factory

Den här guiden visar hur du skapar en delad självvärd integreringskörning i Azure Data Factory. Sedan kan du använda den delade självvärderade integrationskörningen i en annan datafabrik.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Skapa en delad självvärd IR med Azure Data Factory UI

Om du vill skapa en delad IR med egen värdbaserad IR med Azure Data Factory-användargränssnittet kan du vidta följande åtgärder:

1. I den självvärdbaserade IR som ska delas beviljar du behörighet till den datafabrik där du vill skapa den länkade IR:et.
      
    ![Knapp för att bevilja behörighet på fliken Delning](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)
      
    ![Val för tilldelning av behörigheter](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)     
    
2. Observera resurs-ID för den självvärdbaserade IR som ska delas.
      
   ![Plats för resurs-ID](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)
    
3. Skapa en ny självvärd iR (länkad) i den datafabrik som behörigheterna beviljades till och ange resurs-ID.
      
   ![Knapp för att skapa en länkad självvärd integrationskörning](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)
      
    ![Rutor för namn och resurs-ID](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Skapa en delad självvärd IR med Azure PowerShell

Om du vill skapa en delad IR med egen värdbaserad med Azure PowerShell kan du vidta följande åtgärder: 
1. Skapa en datafabrik. 
1. Skapa Integration Runtime med egen värd.
1. Dela den självvärderade integrationskörningen med andra datafabriker.
1. Skapa en länkad integrationskörning.
1. Återkalla delningen.

### <a name="prerequisites"></a>Krav 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

- **Azure PowerShell**. Följ instruktionerna i [Installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps). Du använder PowerShell för att köra ett skript för att skapa en självvärderad integrationskörning som kan delas med andra datafabriker. 

> [!NOTE]  
> För en lista över Azure-regioner där Data Factory är tillgängligt väljer du de regioner som intresserar dig för Produkter som [är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta Windows PowerShell ISE.

1. Skapa variabler. Kopiera och klistra in följande skript. Ersätt variablerna, till exempel **SubscriptionName** och **ResourceGroupName**, med verkliga värden: 

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

1. Logga in och välj en prenumeration. Lägg till följande kod i skriptet för att logga in och välj din Azure-prenumeration:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Skapa en resursgrupp och en datafabrik.

    > [!NOTE]  
    > Det här steget är valfritt. Om du redan har en datafabrik hoppar du över det här steget. 

    Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med kommandot [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas `myResourceGroup` en resursgrupp med namnet WestEurope-plats: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Skapa en datafabrik genom att köra följande kommandon: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

> [!NOTE]  
> Det här steget är valfritt. Om du redan har den självvärderade integrationskörningen som du vill dela med andra datafabriker hoppar du över det här steget.

Kör följande kommando för att skapa en självvärd för integrationskörning:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Hämta autentiseringsnyckeln för integreringskörning och registrera en nod

Kör följande kommando för att hämta autentiseringsnyckeln för den självvärderade integrationskörningen:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Svaret innehåller autentiseringsnyckeln för den här självvärderade integrationskörningen. Du använder den här nyckeln när du registrerar noden integrationskörningstid.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Installera och registrera den självvärderade integrationskörningen

1. Hämta det självvärderade integrationskörningsinstallationsprogrammet från [Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Kör installationsprogrammet för att installera den självvärderade integreringen på en lokal dator.

3. Registrera den nya självvärderade integreringen med autentiseringsnyckeln som du hämtade i ett tidigare steg.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Dela självvärd för integrationskörning med en annan datafabrik

#### <a name="create-another-data-factory"></a>Skapa en annan datafabrik

> [!NOTE]  
> Det här steget är valfritt. Om du redan har den datafabrik som du vill dela med dig av hoppar du över det här steget.

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Bevilja behörighet

Bevilja behörighet till datafabriken som behöver komma åt den självvärderade integrationskörningen som du skapade och registrerade.

> [!IMPORTANT]  
> Hoppa inte över det här steget!

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Skapa en länkad självvärd integrationskörning

Kör följande kommando för att skapa en länkad självvärd för integrationskörning:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

Nu kan du använda den här länkade integrationskörningen i en länkad tjänst. Den länkade integrationskörningen använder den delade integrationskörningen för att köra aktiviteter.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Återkalla integreringskörningsdelning från en datafabrik

Om du vill återkalla åtkomsten för en datafabrik från den delade integrationskörningen kör du följande kommando:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

Om du vill ta bort den befintliga länkade integrationskörningen kör du följande kommando mot den delade integrationskörningen:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>Nästa steg

- Granska [integrationskörningskoncept i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).

- Lär dig hur du [skapar en självvärd integreringskörning i Azure-portalen](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).
