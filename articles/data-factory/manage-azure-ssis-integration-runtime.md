---
title: Omkonfigurera Azure SSIS-integreringskörningen
description: Lär dig hur du konfigurerar om en Azure-SSIS integration runtime i Azure Data Factory när du redan har gjort det.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: ffebb8f82a69f0404974e6c8ea91bec951ca80e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415773"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Omkonfigurera Azure SSIS-integreringskörningen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du konfigurerar om en befintlig Azure-SSIS integration Runtime. Om du vill skapa en Azure-SSIS integration Runtime (IR) i Azure Data Factory, se [skapa en Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt 
Du kan använda Data Factory UI för att stoppa, redigera/omkonfigurera eller ta bort ett Azure-SSIS IR. 

1. I **Data Factory användar gränssnitt**växlar du till fliken **Redigera** . Starta Data Factory användar gränssnitt genom att klicka på **författare & Monitor** på Start sidan för din data fabrik.
2. I den vänstra rutan klickar du på **anslutningar**.
3. I den högra rutan växlar du till **integration Runtimes**. 
4. Du kan använda knapparna i kolumnen åtgärder för att **stoppa**, **Redigera**eller **ta bort** integrerings körningen. Med knappen **kod** i kolumnen **åtgärder** kan du Visa den JSON-definition som är kopplad till integration Runtime.  
    
    ![Åtgärder för Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Så här konfigurerar du om en Azure-SSIS IR
1. Stoppa integrerings körningen genom att klicka på **stoppa** i kolumnen **åtgärder** . Uppdatera listvyn genom att klicka på **Uppdatera** i verktygsfältet. När IR har stoppats ser du att den första åtgärden låter dig starta IR. 

    ![Åtgärder för Azure SSIS IR – efter stopp](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Redigera/konfigurera om IR genom att klicka på knappen **Redigera** i kolumnen **åtgärder** . I fönstret **integration runtime installation** ändrar du inställningar (till exempel storlek på noden, antalet noder eller maximalt antal parallella körningar per nod). 
3. Starta om IR-knappen genom att klicka på **Start** -knappen i kolumnen **åtgärder** .     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du har etablerat och startat en instans av Azure-SSIS integration runtime kan du konfigurera den igen genom att köra en sekvens `Stop`  -  `Set`  -  `Start` med PowerShell-cmdlets i följd. Följande PowerShell-skript ändrar till exempel antalet noder som har allokerats för Azure-SSIS integration runtime-instansen till fem.

### <a name="reconfigure-an-azure-ssis-ir"></a>Konfigurera om en Azure-SSIS IR

1. Stoppa först integrerings körningen för Azure-SSIS med hjälp av cmdleten [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) . Detta kommando frigör alla noder och avslutar faktureringen.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Konfigurera sedan Azure-SSIS IR med hjälp av cmdleten [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) . Följande exempel kommando skalar upp en Azure-SSIS integration runtime till fem noder.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Starta sedan Azure-SSIS integration runtime genom att använda cmdleten [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) . Detta kommando allokerar alla noder för att köra SSIS-paket.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Ta bort ett Azure-SSIS IR
1. Börja med att lista alla befintliga Azure SSIS-IRs under din data fabrik.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Stoppa sedan alla befintliga Azure SSIS-IRs i din data fabrik.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ta sedan bort alla befintliga Azure SSIS-IRs i din data fabrik en i taget.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Slutligen tar du bort data fabriken.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Om du har skapat en ny resurs grupp tar du bort resurs gruppen.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS runtime finns i följande avsnitt: 

- [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller konceptuell information om integrerings körningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln är utökad i självstudien och innehåller instruktioner om hur du använder Azure SQL Database hanterade instansen och ansluter till IR till ett virtuellt nätverk. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
 
