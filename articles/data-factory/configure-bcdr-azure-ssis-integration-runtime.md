---
title: Rekommendationer för företag-affärskontinuitet och haveriberedskap recovery (BCDR) för Azure-SSIS Integration Runtime | Microsoft Docs
description: Den här artikeln innehåller rekommendationer för företag-affärskontinuitet och haveriberedskap återställning för Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285862"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Rekommendationer för företag-affärskontinuitet och haveriberedskap recovery (BCDR) för Azure-SSIS Integration Runtime

I syfte att haveriberedskap du stoppar dina Azure-SSIS integration runtime i den region där den körs och växla till en annan region för att starta den igen. Vi rekommenderar att du använder [parade Azure-regioner](../best-practices-availability-paired-regions.md) för detta ändamål.

## <a name="prerequisites"></a>Förutsättningar

- Kontrollera att du har aktiverat haveriberedskap för Azure SQL Database-servern om servern har ett avbrott på samma gång. Mer information finns i [översikt över affärskontinuitet med Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Om du använder ett virtuellt nätverk i den aktuella regionen, som du behöver använda ett annat virtuellt nätverk i det nya området för att ansluta din Azure-SSIS integration runtime. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för blob-behållare som lagrar dina anpassade installationsskriptet och associerade filer, så att det fortsätter att vara tillgänglig under ett avbrott. Mer information finns i [konfigurera en anpassad installation på en Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="steps"></a>Steg

Följ dessa steg för att stoppa din Azure-SSIS IR, växla IR till en ny region och starta den igen.

1. Stoppa IR i den ursprungliga regionen.

2. Anropa följande kommando i PowerShell för att uppdatera IR

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Mer information om det här PowerShell-kommandot finns i [skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Starta IR igen.

## <a name="next-steps"></a>Nästa steg

Överväg dessa andra konfigurationsalternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS Integration Runtime för höga prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa installationsprogrammet för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
