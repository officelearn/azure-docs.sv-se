---
title: Etablera Enterprise Edition för Azure-SSIS Integration Runtime
description: I den här artikeln beskrivs funktionerna i Enterprise Edition för Azure-SSIS Integration Runtime och hur du etablerar den
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: a0e35a309fd30a36263b498b6a8e74a312004a99
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606158"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Etablera Enterprise Edition för Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Med Enterprise Edition av Azure-SSIS Integration Runtime kan du använda följande avancerade funktioner och premiumfunktioner:
-   Komponenter för ändring av datainsamling (CDC)
-   Oracle-, Teradata- och SAP BW-kontakter
-   SQL Server Analysis Services (SSAS) och Azure Analysis Services (AAS) kopplingar och omvandlingar
-   Fuzzy Gruppering och Fuzzy Lookup omvandlingar
-   Termextrahering och termsökningsomvandlingar

Vissa av dessa funktioner kräver att du installerar ytterligare komponenter för att anpassa Azure-SSIS IR. Mer information om hur du installerar ytterligare komponenter finns i [Anpassad installation för Azure-SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Enterprise-funktioner

| **Företagsfunktioner** | **Beskrivningar** |
|---|---|
| CDC-komponenter | CDC Source, Control Task och Splitter Transformation är förinstallerade på Azure-SSIS IR Enterprise Edition. Om du vill ansluta till Oracle måste du också installera CDC Designer and Service på en annan dator. |
| Oracle-kontakter | Oracles anslutningshanteraren, källa och mål är förinstallerade i Azure-SSIS IR Enterprise Edition. Du måste också installera Oracle Call Interface (OCI) drivrutin, och vid behov konfigurera Oracle Transport Network Substrate (TNS), på Azure-SSIS IR. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-kopplingar | Du måste installera Teradata Connection Manager, Source och Destination, samt TPT-API:et (TPT) och Teradata ODBC-drivrutinen på Azure-SSIS IR Enterprise Edition. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW-kontakter | SAP BW Connection Manager, Source och Destination är förinstallerade på Azure-SSIS IR Enterprise Edition. Du måste också installera SAP BW-drivrutinen på Azure-SSIS IR. Dessa kopplingar stöder SAP BW 7.0 eller tidigare versioner. Om du vill ansluta till senare versioner av SAP BW eller andra SAP-produkter kan du köpa och installera SAP-kopplingar från ISV:er från tredje part på Azure-SSIS IR. Mer information om hur du installerar ytterligare komponenter finns i [Anpassad installation för Azure-SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Komponenter till Analysis Services               | Utbildningsmålet för data mining-modellen, dimensionbearbetningsmålet och mål för partitionsbearbetning samt omvandlingen av data mining-frågor är förinstallerade på Azure-SSIS IR Enterprise Edition. Alla dessa komponenter stöder SQL Server Analysis Services (SSAS), men endast mål för partitionsbearbetning stöder Azure Analysis Services (AAS). Om du vill ansluta till SSAS måste du också [konfigurera autentiseringsuppgifter för Windows i SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Utöver dessa komponenter är Analysis Services Execute DDL-uppgift, Analysis Services Processing Task och data mining query-uppgiften också förinstallerad på Azure-SSIS IR Standard/Enterprise Edition. |
| Fuzzy Gruppering och Fuzzy Lookup omvandlingar  | Omvandlingarna fuzzy Grouping och Fuzzy Lookup är förinstallerade i Azure-SSIS IR Enterprise Edition. Dessa komponenter stöder både SQL Server och Azure SQL Database för lagring av referensdata. |
| Termextrahering och termsökningsomvandlingar | Term extraction och term-uppslagsomvandlingar förinstalleras på Azure-SSIS IR Enterprise Edition. Dessa komponenter stöder både SQL Server och Azure SQL Database för lagring av referensdata. |

## <a name="instructions"></a>Instruktioner

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Ladda ned och installera [Azure PowerShell](/powershell/azure/install-az-ps).

2.  När du etablerar eller konfigurerar om Azure-SSIS IR med PowerShell körs du `Set-AzDataFactoryV2IntegrationRuntime` med **Enterprise** som värde för parametern **Edition** innan du startar Azure-SSIS IR. Här är ett exempel skript:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Nästa steg

-   [Anpassad konfiguration för Azure-SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Så här utvecklar du betalda eller licensierade anpassade komponenter för Azure-SSIS-integreringskörningen](how-to-develop-azure-ssis-ir-licensed-components.md)
