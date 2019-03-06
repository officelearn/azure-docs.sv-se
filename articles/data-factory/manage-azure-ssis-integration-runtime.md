---
title: Konfigurera om Azure-SSIS integration runtime | Microsoft Docs
description: Lär dig mer om att konfigurera om en Azure-SSIS integration runtime i Azure Data Factory efter att du redan har skapat den.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454952"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Konfigurera om Azure-SSIS integration runtime
Den här artikeln beskriver hur du konfigurerar om en befintlig Azure-SSIS integration runtime. Om du vill skapa en Azure-SSIS integration runtime (IR) i Azure Data Factory [skapar ett Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt 
Du kan använda Data Factory-användargränssnitt för att stoppa, redigera/konfigurera om eller ta bort en Azure-SSIS IR. 

1. I den **Användargränssnittet för Data Factory**, växla till den **redigera** fliken. Om du vill starta Användargränssnittet för Data Factory klickar du på **författare och Övervakare** på startsidan på din datafabrik.
2. I den vänstra rutan klickar du på **anslutningar**.
3. I den högra rutan, växlar du till den **Integreringskörningar**. 
4. Du kan använda knappar i kolumnen åtgärder att **stoppa**, **redigera**, eller **ta bort** integration runtime. Den **kod** knappen i den **åtgärder** kolumn kan du visa JSON-definition som är associerade med integration runtime.  
    
    ![Åtgärder för Azure SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Konfigurera om en Azure-SSIS IR
1. Stoppa integration runtime genom att klicka på **stoppa** i den **åtgärder** kolumn. Om du vill uppdatera listan klickar du på **uppdatera** i verktygsfältet. När IR har stoppats visas att den första åtgärden kan du starta i IR. 

    ![Åtgärder för Azure SSIS IR – när stoppats](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Redigera/omkonfiguration IR genom att klicka på **redigera** knappen i den **åtgärder** kolumn. I den **av Integration Runtime** fönstret Ändra inställningar (till exempel storleken på noden, antal noder eller högsta parallella körningar per nod). 
3. Om du vill starta om IR, klickar du på **starta** knappen i den **åtgärder** kolumn.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du etablera och starta en instans av Azure-SSIS integration runtime kan du konfigurera den genom att köra en serie `Stop`  -  `Set`  -  `Start` PowerShell-cmdlets i följd. Till exempel ändrar följande PowerShell-skript antalet noder som allokerats för Azure-SSIS integration runtime-instans till fem.

### <a name="reconfigure-an-azure-ssis-ir"></a>Konfigurera om en Azure-SSIS IR

1. Först stoppa Azure-SSIS-integreringskörning med hjälp av den [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) cmdlet. Det här kommandot släpper alla dess noder och stoppar fakturering.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Konfigurera Azure-SSIS IR med hjälp av den [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet. Kommandot i följande exempel skalas ut en Azure-SSIS integration runtime till fem noder.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Starta Azure-SSIS-integreringskörning med hjälp av den [Start AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet. Det här kommandot allokerar alla dess noder för att köra SSIS-paket.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Ta bort en Azure-SSIS IR
1. Först, lista alla befintliga Azure-SSIS IR under din data factory.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Därefter stoppa alla befintliga Azure SSIS IR i din datafabrik.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ta sedan bort alla befintliga Azure SSIS IR i din datafabrik i taget.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Slutligen ska du ta bort din data factory.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Om du har skapat en ny resursgrupp, tar du bort resursgruppen.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om Azure-SSIS runtime finns i följande avsnitt: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integreringskörningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Anvisningar: Skapa en Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md). Den här artikeln utökas med självstudien och innehåller instruktioner för hur du använder Azure SQL Database Managed Instance och ansluter IR till ett virtuellt nätverk. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
 
