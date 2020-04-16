---
title: Omkonfigurera Azure SSIS-integreringskörningen
description: Lär dig hur du konfigurerar om en Azure-SSIS-integreringskörning i Azure Data Factory när du redan har etablerat den.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415773"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Omkonfigurera Azure SSIS-integreringskörningen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs hur du konfigurerar om en befintlig Azure-SSIS-integreringskörning. Information om hur du skapar en Azure-SSIS-integrationskörning (IR) i Azure Data Factory finns i [Skapa en Azure-SSIS-integreringskörning](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt 
Du kan använda Data Factory UI för att stoppa, redigera/konfigurera om eller ta bort en Azure-SSIS IR. 

1. Växla till fliken **Redigera** i **datafabriken.** Om du vill starta Data Factory UI klickar du på **Författare & Monitor** på startsidan för datafabriken.
2. Klicka på **Anslutningar**i den vänstra rutan .
3. I den högra rutan växlar du till **integrationskörningarna**. 
4. Du kan använda knappar i kolumnen Åtgärder för att **stoppa,** **redigera**eller **ta bort** integrationskörningen. Med knappen **Kod** i kolumnen **Åtgärder** kan du visa JSON-definitionen som är associerad med integrationskörningen.  
    
    ![Åtgärder för Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Så här konfigurerar du om en Azure-SSIS IR
1. Stoppa integrationskörningen genom att klicka på **Stoppa** i kolumnen **Åtgärder.** Om du vill uppdatera listvyn klickar du på **Uppdatera** i verktygsfältet. När IR har stoppats ser du att den första åtgärden låter dig starta IR.After the IR is stopped, you see that the first action lets you start the IR. 

    ![Åtgärder för Azure SSIS IR – efter att ha stoppats](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Redigera/konfigurera om IR genom att klicka på **knappen Redigera** i kolumnen **Åtgärder.** I fönstret Inställningar för **integrationskörning** ändrar du inställningar (till exempel nodens storlek, antal noder eller maximala parallella körningar per nod). 
3. Om du vill starta om IR:et klickar du på **Start-knappen** i kolumnen **Åtgärder.**     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du har etablerat och starta en instans av Azure-SSIS-integreringskörning kan `Stop`  -  `Set`  -  du konfigurera om den genom att köra en sekvens av `Start` PowerShell-cmdletar i följd. Följande PowerShell-skript ändrar till exempel antalet noder som allokerats för Azure-SSIS-integrationskörningsinstansen till fem.

### <a name="reconfigure-an-azure-ssis-ir"></a>Konfigurera om en Azure-SSIS IR

1. Stoppa först Azure-SSIS-integreringen med hjälp av cmdleten [Stop-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) Det här kommandot släpper alla dess noder och stoppar faktureringen.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Konfigurera sedan om Azure-SSIS IR med hjälp av cmdleten [Set-AzDataFactoryV2IntegrationRuntime.](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) Följande exempelkommando skalar ut en Azure-SSIS-integreringskörning till fem noder.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Starta sedan Azure-SSIS-integreringen med [start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet. Det här kommandot allokerar alla sina noder för att köra SSIS-paket.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Ta bort en Azure-SSIS IR
1. Visa först alla befintliga Azure SSIS IR:er under din datafabrik.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Stoppa sedan alla befintliga Azure SSIS IRs i din datafabrik.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ta sedan bort alla befintliga Azure SSIS IRs i din datafabrik en efter en.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Ta slutligen bort datafabriken.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Om du hade skapat en ny resursgrupp tar du bort resursgruppen.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS-körning finns i följande avsnitt: 

- [Azure-SSIS-integreringskörning](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller begreppsmässig information om integrationskörningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Så här skapar du en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökar självstudien och innehåller instruktioner om hur du använder Azure SQL Database Managed Instance och ansluter till IR till ett virtuellt nätverk. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
 
