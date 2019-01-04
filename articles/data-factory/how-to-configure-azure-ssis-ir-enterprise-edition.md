---
title: Etablera Enterprise Edition för Azure-SSIS Integration Runtime | Microsoft Docs
description: Den här artikeln beskriver funktionerna i Enterprise Edition för Azure-SSIS Integration Runtime och hur du etablerar den
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: c475a9c820e577a321491b87d806ed08ddff8693
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013886"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Etablera Enterprise Edition för Azure-SSIS Integration Runtime

Enterprise-utgåvan av Azure-SSIS Integration Runtime kan du använda följande avancerade och premium-funktioner:
-   Ändra Data Capture (CDC)-komponenter
-   Oracle och Teradata SAP BW-kopplingar
-   SQL Server Analysis Services (SSAS) och Azure Analysis Services (AAS)-anslutningar och transformeringar
-   Fuzzy gruppering och Fuzzy Lookup omvandlingar
-   Termen extrahering och termen Lookup omvandlingar

Vissa av dessa funktioner måste du installera ytterligare komponenter om du vill anpassa Azure-SSIS IR. Mer information om hur du installerar ytterligare komponenter finns i [anpassad konfiguration för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Enterprise-funktioner

| **Enterprise-funktioner** | **Beskrivningar** |
|---|---|
| CDC-komponenter | CDC källan, kontroll uppgiften och delare omvandlingen är förinstallerade på Azure-SSIS IR Enterprise Edition. Om du vill ansluta till Oracle, måste du också installera CDC Designer och tjänsten på en annan dator. |
| Oracle-kopplingar | Oracle Anslutningshanteraren, källa och mål är förinstallerade på Azure-SSIS IR Enterprise Edition. Du måste också installera drivrutinen anropa gränssnittet OCI (Oracle) och om det behövs konfigurerar du Oracle Transport nätverk underlag (TNS) på Azure-SSIS IR. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-kopplingar | Du måste installera Anslutningshanteraren Teradata, källa och mål samt av Teradata parallella transportören (TPT) API- och Teradata ODBC-drivrutinen på Azure-SSIS IR Enterprise Edition. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW-anslutningsapparna | Anslutningshanteraren för SAP BW, källa och mål är förinstallerade på Azure-SSIS IR Enterprise Edition. Du måste också installera SAP BW-drivrutinen på Azure-SSIS IR. Dessa anslutningar stöd för SAP BW 7.0 eller tidigare versioner. Om du vill ansluta till senare versioner av SAP BW eller andra SAP-produkter, köpa och installera SAP-anslutningsappar från tredje parts ISV: er i Azure-SSIS IR. Mer information om hur du installerar ytterligare komponenter finns i [anpassad konfiguration för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services-komponenter               | Data Mining Model utbildning mål, målet bearbetning av dimensionen och målet bearbetning av partitionen samt Data Mining fråga transformering har förinstallerats på Azure-SSIS IR Enterprise Edition. Alla dessa komponenter stöder SQL Server Analysis Services (SSAS), men endast Partition bearbetning av målet har stöd för Azure Analysis Services (AAS). Om du vill ansluta till SSAS måste du också behöva [konfigurera Windows-autentiseringsuppgifter i SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Förutom de här komponenterna förinstallerats Analysis Services köra DDL uppgiften, Analysis Services bearbetar uppgiften och åtgärden Data Mining fråga även på Azure-SSIS IR Standard/Enterprise Edition. |
| Fuzzy gruppering och Fuzzy Lookup omvandlingar  | Omvandlingarna Fuzzy gruppering och Fuzzy Lookup är förinstallerade på Azure-SSIS IR Enterprise Edition. De här komponenterna har stöd för både SQL Server och Azure SQL Database för att lagra referensdata. |
| Termen extrahering och termen Lookup omvandlingar | Termen extrahering och termen Lookup omvandlingarna är förinstallerade på Azure-SSIS IR Enterprise Edition. De här komponenterna har stöd för både SQL Server och Azure SQL Database för att lagra referensdata. |

## <a name="instructions"></a>Instruktioner

1.  Ladda ned och installera [Azure PowerShell (version 5.4 eller senare)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  När du etablerar eller konfigurera om Azure-SSIS IR med PowerShell, kör `Set-AzureRmDataFactoryV2IntegrationRuntime` med **Enterprise** som värde för den **Edition** parametern innan du startar Azure-SSIS IR. Här är ett exempelskript:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Nästa steg

-   [Anpassad installation för den Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Hur du utvecklar betald eller licensierad anpassade komponenter för Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
