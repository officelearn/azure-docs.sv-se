---
title: Konfigurera om Azure-SSIS-integrering runtime | Microsoft Docs
description: Lär dig hur du konfigurerar om en Azure-SSIS-integrering körning i Azure Data Factory efter redan har etablerats.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 91887bad9c3ca976612522c161663fb0b35f587c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266931"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Konfigurera om Azure-SSIS-integrering runtime
Den här artikeln beskriver hur du konfigurerar om en befintlig Azure-SSIS-integrering körning. Om du vill skapa en Azure-SSIS-integrering körning (IR) i Azure Data Factory finns [skapa en Azure-SSIS-integrering körning](create-azure-ssis-integration-runtime.md).  

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen om Data Factory version 1](v1/data-factory-introduction.md).


## <a name="data-factory-ui"></a>Data Factory-användargränssnitt 
Du kan använda Data Factory UI stoppa, redigera/konfigurera om eller ta bort en Azure-SSIS-IR. 

1. I den **Data Factory UI**, växla till den **redigera** fliken. Om du vill starta Användargränssnittet för Data Factory, klickar du på **författare & övervakaren** på startsidan i din data factory.
2. I den vänstra rutan klickar du på **anslutningar**.
3. Växla till i den högra rutan i **integrering körningar**. 
4. Du kan använda knapparna i kolumnen åtgärder att **stoppa**, **redigera**, eller **ta bort** integrering körningsmiljön. Den **kod** knappen i den **åtgärder** kolumn kan du visa JSON-definition som är associerade med integration körningsmiljön.  
    
    ![Åtgärder för Azure SSIS-IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Konfigurera om en Azure-SSIS-IR
1. Stoppa integration runtime genom att klicka på **stoppa** i den **åtgärder** kolumn. Uppdatera listan, klicka på **uppdatera** i verktygsfältet. När IR stoppas kan se du att den första åtgärden kan du starta IR. 

    ![Åtgärder för Azure SSIS IR - när stoppad](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Redigera/reconfigure IR genom att klicka på **redigera** knappen i den **åtgärder** kolumn. I den **integrering Runtime installationsprogrammet** fönster, ändra inställningar (till exempel storleken på noden, antalet noder eller maximal parallella körningar per nod). 
3. Starta om IR, klicka på **starta** knappen i den **åtgärder** kolumn.     

## <a name="azure-powershell"></a>Azure PowerShell
När du etablera och starta en instans av Azure-SSIS-integrering körning kan du konfigurera om den genom att köra en sekvens av `Stop`  -  `Set`  -  `Start` PowerShell-cmdlets i följd. Till exempel ändrar följande PowerShell-skript antalet noder som allokerats för Azure-SSIS-integrering runtime-instans till fem.

### <a name="reconfigure-an-azure-ssis-ir"></a>Konfigurera om en Azure-SSIS-IR

1. Först stoppa körningen Azure SSIS-integrering med hjälp av [stoppa AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet. Det här kommandot släpper alla dess noder och stoppar fakturering.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Konfigurera Azure SSIS-IR med hjälp av den [Set AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet. Följande exempelkommando skalas ut en Azure-SSIS-integrering körning till fem noder.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Starta körningen Azure SSIS-integrering med hjälp av den [Start AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet. Det här kommandot allokerar alla dess noder för att köra SSIS-paket.   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Ta bort en Azure-SSIS-IR
1. Lista över alla befintliga Azure SSIS IRs under din data factory först.

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Därefter stoppa alla befintliga Azure SSIS IRs i din data factory.

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. För att ta bort alla befintliga Azure SSIS IRs i din data factory i taget.

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Slutligen ska du ta bort din data factory.

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Om du har skapat en ny resursgrupp kan du ta bort resursgruppen.

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS runtime finns i följande avsnitt: 

- [Azure-SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar i allmänhet inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln kan utökas med kursen och innehåller instruktioner om med Azure SQL hanteras instans (förhandsversion) och koppla IR till ett virtuellt nätverk. 
- [Ansluta till en Azure-SSIS-IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller information om att ansluta till en Azure-SSIS-IR till ett Azure virtual network. Det ger också steg för att använda Azure-portalen för att konfigurera virtuella nätverk så att Azure-SSIS IR kan ansluta till det virtuella nätverket. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
 
