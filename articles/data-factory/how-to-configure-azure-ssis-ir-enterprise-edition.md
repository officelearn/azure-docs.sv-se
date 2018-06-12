---
title: Etablera Enterprise Edition för Azure-SSIS-integrering Runtime | Microsoft Docs
description: Den här artikeln beskriver funktionerna i Enterprise Edition för Azure-SSIS-integrering Runtime och hur du etablerar den
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: 55f4fd18dbebe8a4c666c5512b9cad46ddf9f7d7
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296864"
---
# <a name="enterprise-edition-of-the-azure-ssis-integration-runtime"></a>Enterprise-versionen av körningsmiljön Azure SSIS-integrering

Enterprise-versionen av körningsmiljön Azure SSIS-integrering kan du använda följande avancerade och premium-funktioner:
-   Ändra Change Data Capture (CDC)-komponenter
-   Oracle, Teradata och SAP BW kopplingar
-   SQL Server Analysis Services (SSAS) och Azure Analysis Services (AAS) kopplingar och omvandlingar
-   Fuzzy gruppering och Fuzzy Lookup omvandlingar
-   Termen extrahering och termen sökning omvandlingar

Vissa av dessa funktioner måste du installera ytterligare komponenter för att anpassa Azure SSIS-IR. Mer information om hur du installerar ytterligare komponenter finns [anpassad installation för Azure-SSIS-integrering runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Enterprise-funktioner

| **Enterprise-funktioner** | **Beskrivningar** |
|---|---|
| CDC-komponenter | CDC källan, kontroll-aktivitet och delningslisten omvandling förinstallerat på Azure-SSIS IR Enterprise Edition. För att ansluta till Oracle, måste du också installera CDC Designer och tjänsten på en annan dator. |
| Oracle-kopplingar | Oracle Anslutningshanteraren, källa och mål förinstallerat på Azure-SSIS IR Enterprise Edition. Du måste också installera drivrutinen anropa OCI (Oracle Interface) och om det behövs konfigurerar du Oracle Transport nätverket underlag (TNS) på Azure-SSIS-IR. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-kopplingar | Du måste installera Anslutningshanteraren Teradata, källa och mål samt API: et Teradata parallella transportören (TPT) och Teradata ODBC-drivrutinen på Azure-SSIS IR Enterprise Edition. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW-kopplingar | Anslutningshanteraren för SAP BW, källa och mål förinstallerat på Azure-SSIS IR Enterprise Edition. Du måste också installera drivrutinen för SAP BW på Azure-SSIS-IR. Dessa kopplingar stöd för SAP BW 7.0 eller tidigare versioner. För att ansluta till senare versioner av SAP BW eller andra SAP-produkter som du kan köpa och installera SAP-kopplingar från tredje parts ISV: er på Azure-SSIS-IR. Mer information om hur du installerar ytterligare komponenter finns [anpassad installation för Azure-SSIS-integrering runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services-komponenter               | Data Mining Model utbildning mål, målet bearbetning av dimensionen Partition bearbetning mål samt Data Mining frågan Transformation, förinstallerat på Azure-SSIS IR Enterprise Edition. Alla dessa komponenter stöder SQL Server Analysis Services (SSAS), men endast Partition bearbetning målet har stöd för Azure Analysis Services (AAS). Om du vill ansluta till SSAS kan du också behöva [konfigurera Windows-autentiseringsuppgifter i SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Förutom de här komponenterna förinstallerat Analysis Services köra DDL aktiviteten och aktiviteten Analysis Services-bearbetning av Data Mining frågan uppgiften också på Azure-SSIS IR Standard/Enterprise Edition. |
| Fuzzy gruppering och Fuzzy Lookup omvandlingar  | Omvandlingarna Fuzzy Grouping och Fuzzy Lookup är förinstallerat på Azure-SSIS IR Enterprise Edition. Komponenterna har stöd för både SQL Server och Azure SQL Database för att lagra referensdata. |
| Termen extrahering och termen sökning omvandlingar | Termen extrahering och termen sökning omvandlingarna förinstallerat på Azure-SSIS IR Enterprise Edition. Komponenterna har stöd för både SQL Server och Azure SQL Database för att lagra referensdata. |

## <a name="instructions"></a>Instruktioner

1.  Hämta och installera [Azure PowerShell (5.4 eller senare)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  När du etablerar eller konfigurera om Azure-SSIS-IR med PowerShell kör `Set-AzureRmDataFactoryV2IntegrationRuntime` med **Enterprise** som värde för den **Edition** parametern innan du startar Azure SSIS-IR. Här är ett exempelskript:

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

-   [Anpassad installation för Azure-SSIS-integrering runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Hur du utvecklar betald eller licensierad anpassade komponenter för Azure-SSIS-integrering runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
