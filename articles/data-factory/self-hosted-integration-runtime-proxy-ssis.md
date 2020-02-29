---
title: Konfigurera en integration runtime med egen värd som proxy för SSIS
description: Lär dig hur du konfigurerar en integration runtime med egen värd som en proxy för en Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/28/2020
ms.openlocfilehash: 701e279e349bdc3b92d5b5c2dc50e23199f9d766
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160819"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Konfigurera en egen värd-IR som en proxy för en Azure-SSIS IR i Azure Data Factory

Den här artikeln beskriver hur du kör SQL Server Integration Services-paket (SSIS) på en Azure-SSIS Integration Runtime (Azure-SSIS IR) i Azure Data Factory med en egen värd för integration Runtime (lokal IR) som kon figurer ATS som proxy. 

Med den här funktionen kan du komma åt data lokalt utan att behöva [ansluta dina Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Funktionen är användbar när företags nätverket har en konfiguration för komplex eller en princip som är för begränsad så att du kan mata in Azure-SSIS IR i den.

Den här funktionen delar upp paket som innehåller en data flödes aktivitet med en lokal data källa till två mellanlagrings aktiviteter: 
* Den första aktiviteten, som körs på din egen värd-IR, flyttar först data från den lokala data källan till ett mellanlagringsområde i Azure Blob Storage.
* Den andra aktiviteten, som körs på Azure-SSIS IR, flyttar data från mellanlagringsområdet till det avsedda data målet.

Andra förmåner och funktioner för den här funktionen gör att du kan till exempel konfigurera din egen värd-IR i regioner som ännu inte stöds av en Azure-SSIS IR och tillåta den offentliga statiska IP-adressen för din egen värd-IR i brand väggen för dina data källor.

## <a name="prepare-the-self-hosted-ir"></a>Förbered IR med egen värd

Om du vill använda den här funktionen skapar du först en data fabrik och konfigurerar en Azure-SSIS IR. Om du inte redan har gjort det följer du anvisningarna i [Konfigurera en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Sedan konfigurerar du din egen värd-IR i samma data fabrik där Azure-SSIS IR har kon figurer ATS. För att göra det, se [skapa en IR med egen värd](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Slutligen kan du hämta och installera den senaste versionen av den lokala IR-versionen, samt ytterligare driv rutiner och körnings program, på din lokala dator eller virtuella Azure-dator (VM) enligt följande:
- Hämta och installera den senaste versionen av [IR med egen värd](https://www.microsoft.com/download/details.aspx?id=39717).
- Om du använder objekt Länknings-och Inbäddnings databas (OLEDB) i dina paket kan du hämta och installera relevanta OLEDB-drivrutiner på samma dator där din egen värd-IR installeras, om du inte redan har gjort det.  

  Om du använder den tidigare versionen av OLEDB-drivrutinen för SQL Server (SQL Server Native Client [SQLNCLI]) [hämtar du 64-bitars versionen](https://www.microsoft.com/download/details.aspx?id=50402).  

  Om du använder den senaste versionen av OLEDB-drivrutinen för SQL Server (MSOLEDBSQL) [hämtar du 64-bitars versionen](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Om du använder OLEDB-drivrutiner för andra databas system, till exempel PostgreSQL, MySQL, Oracle och så vidare, kan du hämta 64-bitars versionerna från sina webbplatser.
- Om du inte redan har gjort det kan du [Hämta och installera 64-bitars versionen av C++ Visual (VC) runtime](https://www.microsoft.com/download/details.aspx?id=40784) på samma dator där din egen värd för IR är installerad.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Förbereda Azure Blob Storage-länkad tjänst för mellanlagring

Om du inte redan har gjort det skapar du en Azure Blob Storage-länkad tjänst i samma data fabrik där Azure-SSIS IR har kon figurer ATS. Det gör du i [skapa en Azure Data Factory-länkad tjänst](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Se till att göra följande:
- För **data lager**väljer du **Azure Blob Storage**.  
- För **Anslut via integration runtime**väljer du **AutoResolveIntegrationRuntime**.  
- För **autentiseringsmetod**väljer du **konto nyckel**, **SAS-URI**eller **tjänstens huvud namn**.  

    >[!TIP]
    >Om du väljer **tjänstens huvud namn**, beviljar du minst rollen *Storage BLOB-data* . Mer information finns i [Azure Blob Storage Connector](connector-azure-blob-storage.md#linked-service-properties).

![Förbereda Azure Blob Storage-länkad tjänst för mellanlagring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurera en Azure-SSIS IR med din egen värd-IR som proxy

När du har för berett din egen värd för IR och Azure Blob Storage-länkad tjänst för mellanlagring kan du nu konfigurera nya eller befintliga Azure-SSIS IR med den infraröda IR som en proxy i Data Factory-portalen eller-appen. Innan du gör det, om det befintliga Azure-SSIS IR redan körs, stoppar du det och startar sedan om det.

1. I installations fönstret för **integration runtime** hoppar du över avsnitten **allmänna inställningar** och **SQL-inställningar** genom att välja **Nästa**. 

1. I avsnittet **Avancerade inställningar** gör du följande:

   1. Markera kryss rutan **Konfigurera egen värd integration runtime som en proxy för din Azure-SSIS integration runtime** . 

   1. I list rutan **egen värd integration runtime** väljer du den befintliga IR-filen med egen värd som proxy för Azure-SSIS IR.

   1. I list rutan för den **länkade tjänsten för mellanlagring** väljer du din befintliga Azure Blob Storage-länkade tjänst eller skapar en ny för mellanlagring.

   1. I rutan **mellanlagringsplats** anger du en BLOB-behållare i det valda Azure Blob Storage-kontot eller låter det vara tomt om du vill använda en standard för mellanlagring.

   1. Välj **Fortsätt**.

   ![Avancerade inställningar med en egen värd-IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Du kan också konfigurera din nya eller befintliga Azure-SSIS IR med den egen värdbaserade IR-filen som en proxy med PowerShell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Aktivera SSIS-paket för att ansluta via proxy

Genom att använda antingen det senaste SSDT med SSIS-projekts tillägg för Visual Studio eller ett fristående installations program kan du hitta en ny `ConnectByProxy`-egenskap som har lagts till i OLEDB-eller flat fil anslutnings hanterare.
* [Hämta SSDT med SSIS-projekt-tillägget för Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Hämta det fristående installations programmet](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

När du skapar nya paket som innehåller data flödes aktiviteter med OLEDB eller flata fil källor, som gör att du kan komma åt databaser eller filer lokalt, kan du aktivera den här egenskapen genom att ange den som *True* i rutan **Egenskaper** för relevanta anslutnings hanterare.

![Aktivera egenskapen ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Du kan också aktivera den här egenskapen när du kör befintliga paket, utan att behöva ändra dem manuellt en i taget.  Det finns två alternativ:
- **Alternativ A**: öppna, återskapa och distribuera om projektet som innehåller paketen med de senaste SSDT som ska köras på din Azure-SSIS IR. Du kan sedan aktivera egenskapen genom att ange den som *True* för relevanta anslutnings hanterare. När de kör paket från SSMS visas dessa anslutnings hanterare på fliken **anslutnings hanterare** i popup-fönstret **Kör paket** .

  ![Aktivera ConnectByProxy Egenskap2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Du kan också aktivera egenskapen genom att ställa in den på *Sant* för relevanta anslutnings hanterare som visas på fliken **anslutnings hanterare** i [kör SSIS-paket aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när de kör paket i Data Factory pipelines.
  
  ![Aktivera ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Alternativ B:** Distribuera om projektet som innehåller de paket som ska köras på din SSIS IR. Du kan sedan aktivera egenskapen genom att ange dess egenskaps Sök väg, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`och ange den till *True* som en åsidosättande egenskap på fliken **Avancerat** i popup-fönstret **Kör paket** när du kör paket från SSMS.

  ![Aktivera ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Du kan också aktivera egenskapen genom att ange dess egenskaps Sök väg, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`och ange den till *True* som en åsidosättande egenskap på fliken **åsidosättningar** för att [köra SSIS-paket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när du kör paket i Data Factory pipeliner.
  
  ![Aktivera ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Felsöka första och andra mellanlagrings aktiviteter

På din egen värd-IR kan du hitta körnings loggarna i mappen *C:\ProgramData\SSISTelemetry* och körnings loggarna för första mellanlagrings aktiviteter i mappen *C:\ProgramData\SSISTelemetry\ExecutionLog* .  Du kan hitta körnings loggarna för andra mellanlagrings aktiviteter i dina SSISDB eller angivna loggnings Sök vägar, beroende på om du lagrar dina paket i SSISDB eller fil system, fil resurser eller Azure Files. Du kan också hitta unika ID: n för första mellanlagrings aktiviteter i körnings loggarna för andra mellanlagrings aktiviteter. 

![Unikt ID för den första mellanlagrings uppgiften](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Använd Windows-autentisering i mellanlagrings aktiviteter

Om mellanlagrings uppgifterna på din egen värd-IR kräver Windows-autentisering [konfigurerar du SSIS-paketen så att de använder samma Windows-autentisering](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Dina mellanlagrings uppgifter kommer att anropas med det fristående IR-tjänstkontot (*NT SERVICE\DIAHostService*, som standard) och dina data lager kommer att få åtkomst till Windows Authentication-kontot. Båda kontona kräver att vissa säkerhets principer tilldelas till dem. På den lokala IR-datorn går du till **lokal säkerhets princip** > **lokala principer** > **tilldelning av användar rättigheter**och gör sedan följande:

1. Tilldela de *Justera minnes kvoterna för en process* och *Ersätt en token för processnivå* till det egna IR-tjänstkontot. Detta bör ske automatiskt när du installerar din egen värd-IR med standard tjänst kontot. Om du använder ett annat tjänst konto tilldelar du samma principer.

1. Tilldela Windows Authentication *-kontot inloggningen logga in som en tjänst* princip.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fakturering för första och andra mellanlagrings aktiviteter

De första mellanlagrings aktiviteter som körs på din lokala IR-överföring faktureras separat, precis som alla data förflyttnings aktiviteter som körs på en IR med egen värd debiteras. Detta anges i artikeln om [prissättning för Azure Data Factory data pipelinen](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De andra mellanlagrings aktiviteterna som körs på Azure-SSIS IR faktureras inte separat, utan att Azure-SSIS IR som körs faktureras enligt vad som anges i artikeln [Azure-SSIS IR prissättning](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Aktuella begränsningar

- Det finns för närvarande stöd för data flödes aktiviteter med Open Database Connectivity (ODBC), OLEDB eller Flate File Connection Manager och ODBC, OLEDB eller flata fil källor. 
- Det finns för närvarande stöd för Azure Blob Storage – länkade tjänster som är konfigurerade med *konto nyckel*, *signatur för delad åtkomst (SAS)* eller *tjänstens huvud namn* .

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din egen värd-IR som proxy för din Azure-SSIS IR kan du distribuera och köra dina paket för att komma åt data lokalt som kör SSIS-paket aktiviteter i Data Factory pipelines. Mer information finns i [köra SSIS-paket som kör SSIS-paket aktiviteter i Data Factory pipelines](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
