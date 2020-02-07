---
title: Konfigurera integration runtime med egen värd som proxy för SSIS
description: Lär dig hur du konfigurerar Integration Runtime med egen värd som proxy för Azure-SSIS Integration Runtime.
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
ms.date: 02/06/2020
ms.openlocfilehash: b20a615691d95c04574e2909f69b5a83a97f9d14
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048954"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Konfigurera IR med egen värd som proxy för Azure-SSIS IR i ADF

Den här artikeln beskriver hur du kör SQL Server Integration Services-paket (SSIS) på Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) med egen värd-IR som kon figurer ATS som en proxy.  Med den här funktionen kan du komma åt data lokalt utan att behöva [ansluta till Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Det är användbart när företags nätverket har en alltför komplex konfiguration/restriktiv princip som du kan använda för att mata in din Azure-SSIS IR.

Med den här funktionen delas ditt paket med en data flödes aktivitet med en lokal data källa till två mellanlagrings uppgifter: den första som körs på din egen värd-IR flyttar först data från den lokala data källan till ett mellanlagringsområde i Azure-Blob Storage, medan den andra som körs på Azure-SSIS IR flyttar sedan data från mellanlagringsområdet till det avsedda data målet.

Den här funktionen ger också andra förmåner/funktioner när du gör det möjligt att tillhandahålla IR i egen värd i regioner som ännu inte stöds av Azure-SSIS IR, tillåta den offentliga statiska IP-adressen för din egen värd-IR i brand väggen för dina data källor osv.

## <a name="prepare-self-hosted-ir"></a>Förbered IR med egen värd

För att kunna använda den här funktionen måste du först skapa en ADF och etablera Azure-SSIS IR under den, om du inte redan har gjort det, genom att följa artikeln så här [etablerar du en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Du måste sedan etablera din egen värd-IR under samma ADF där din Azure-SSIS IR etableras genom att följa artikeln så här skapar du [en IR-artikel med egen värd](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Slutligen måste du ladda ned och installera den senaste versionen av IR med egen värd, samt ytterligare driv rutiner och körning, på din lokala dator/virtuella Azure-dator (VM) enligt följande:
- Hämta och installera den senaste versionen av [IR med egen värd.](https://www.microsoft.com/download/details.aspx?id=39717)
- Om du använder OLEDB-kopplingar i dina paket kan du hämta och installera relevanta OLEDB-drivrutiner på samma dator där IR för egen värd installeras om du inte redan har gjort det.  Om du använder den tidigare versionen av OLEDB-drivrutinen för SQL Server (SQLNCLI) kan du hämta 64-bitars versionen [här](https://www.microsoft.com/download/details.aspx?id=50402).  Om du använder den senaste versionen av OLEDB-drivrutinen för SQL Server (MSOLEDBSQL) kan du hämta 64-bitars versionen [här](https://www.microsoft.com/download/details.aspx?id=56730).  Om du använder OLEDB-drivrutiner för andra databas system, till exempel PostgreSQL, MySQL, Oracle osv., kan du hämta 64-bitars versionen från deras respektive webbplatser.
- Ladda ned och installera C++ Visual (VC) runtime på samma dator där IR för egen värd installeras om du inte redan har gjort det.  Du kan hämta 64-bitars versionen [här](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Förbered Azure Blob Storage länkade tjänsten för mellanlagring

Skapa en Azure Blob Storage-länkad tjänst under samma ADF där Azure-SSIS IR etableras, om du inte redan har gjort det, genom att följa artikeln [så här skapar du en länkad ADM-tjänst](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Kontrol lera följande:
- **Azure Blob Storage** har valts för **data lager**
- **AutoResolveIntegrationRuntime** har valts för **anslutning via integration runtime**
- Antingen **konto nyckeln**/**SAS-URI**/**tjänstens huvud namn** har valts för **autentiseringsmetoden**

>[!TIP]
>När **tjänstens huvud namn** har valts ger du **rollen som minst Storage BLOB data Contributor**. Mer information finns i [Azure Blob Storage Connector](connector-azure-blob-storage.md#linked-service-properties).

![Förbered Azure Blob Storage länkade tjänsten för mellanlagring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Konfigurera Azure-SSIS IR med egen värd-IR som proxy

När du har för berett den lokala IR-och Azure-Blob Storage länkade tjänsten för mellanlagring kan du nu konfigurera nya/befintliga Azure-SSIS IR med IR med egen värd som en proxy på ADF Portal/app.  Om din befintliga Azure-SSIS IR körs stoppar du den innan du gör detta och startar sedan om den senare.

1. Gå igenom avsnitten **allmänna inställningar** och **SQL-inställningar** på sidan för integration runtime-installationen genom att välja knappen **Nästa** . 

1. I avsnittet **Avancerade inställningar** :

   1. Markera kryss rutan **Konfigurera egen värd integration runtime som en proxy för din Azure-SSIS integration runtime** . 

   1. För **integration runtime med egen värd**väljer du din befintliga IR som en proxy för Azure-SSIS IR.

   1. För den **länkade lagrings tjänsten för lagring**väljer du den befintliga Azure Blob Storage-länkade tjänsten eller skapar en ny för mellanlagring.

   1. För **mellanlagringsplatsen**anger du en BLOB-behållare i det valda Azure Blob Storage-kontot eller låter det vara tomt om du vill använda en standard för mellanlagring.

   1. Välj **Fortsätt**.

   ![Avancerade inställningar med en egen värd-IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Du kan också konfigurera nya/befintliga Azure-SSIS IR med egen värd-IR som en proxy med PowerShell.

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

Med hjälp av de senaste SSDT med SSIS-projekts tillägg för Visual Studio som kan hämtas härifrån eller som ett fristående installations program som kan [](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer) [hämtas härifrån kan](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) du hitta en ny **ConnectByProxy** -egenskap som har lagts till i OLEDB/flat fil anslutnings hanterare.  

När du skapar nya paket som innehåller data flödes aktiviteter med OLEDB/flata fil källor för att komma åt databaser/filer lokalt kan du aktivera den här egenskapen **genom att ställa in den på panelen** egenskaper för relevanta anslutnings hanterare.

![Aktivera egenskapen ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Du kan också aktivera den här egenskapen när du kör befintliga paket utan att behöva ändra dem manuellt en i taget.  Det finns två alternativ:
- Öppna, återskapa och distribuera om projektet som innehåller de paketen med de senaste SSDT som ska köras på din Azure-SSIS IR: egenskapen kan sedan aktive ras genom att ställa in den på **True** för de relevanta anslutnings hanterare som visas på fliken **anslutnings hanterare** i popup-fönstret för kör paket när du kör paket från SSMS.

  ![Aktivera ConnectByProxy Egenskap2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Egenskapen kan också aktive ras genom att ställa in den på **True** för relevanta anslutnings hanterare som visas på fliken **anslutnings hanterare** i [kör SSIS-paket aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när du kör paket i ADF-pipeline.
  
  ![Aktivera ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Omdistribuera projektet som innehåller de paket som ska köras på din SSIS IR: egenskapen kan sedan aktive ras genom att ange dess egenskaps Sök väg, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`och ange **den som en** åsidosättande egenskap på fliken **Avancerat** i popup-fönstret kör paket när du kör paket från SSMS.

  ![Aktivera ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Du kan också aktivera egenskapen genom att ange dess egenskaps Sök väg, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`och ange den till **True** som en åsidosättande egenskap på fliken **åsidosättningar** för att [köra SSIS-paket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när du kör paket i ADF-pipeliner.
  
  ![Aktivera ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Felsöka första och andra mellanlagrings aktiviteter

På din egen värd-IR kan du hitta körnings loggarna i `C:\ProgramData\SSISTelemetry` mapp och körnings loggarna för första mellanlagrings aktiviteter i `C:\ProgramData\SSISTelemetry\ExecutionLog` mapp.  Körnings loggarna för andra mellanlagrings aktiviteter finns i dina SSISDB eller angivna loggnings Sök vägar, beroende på om du lagrar dina paket i SSISDB eller fil system/fil resurser/Azure Files respektive.  Du kan också hitta unika ID: n för första mellanlagrings aktiviteter i körnings loggarna för andra mellanlagrings aktiviteter, t. ex. 

![Unikt ID för den första mellanlagrings uppgiften](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Använda Windows-autentisering i mellanlagrings aktiviteter

Om dina mellanlagrings aktiviteter för IR kräver Windows-autentisering måste du [Konfigurera dina SSIS-paket så att de använder samma Windows-autentisering](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Dina mellanlagrings uppgifter kommer att anropas med det egna IR-tjänstkontot (`NT SERVICE\DIAHostService` som standard) och dina data lager kommer att få åtkomst till Windows Authentication-kontot. Båda kontona kräver att vissa säkerhets principer tilldelas till dem. På den lokala IR-datorn öppnar du därför `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` och utför följande steg.
- Tilldela de **Justera minnes kvoterna för en process** och **Ersätt en token för processnivå** till det egna IR-tjänstkontot. Detta bör utföras automatiskt när du installerar din egen värd-IR med standard tjänst kontot. Om du använder ett annat tjänst konto tilldelar du samma principer.
- Tilldela Windows Authentication **-kontot inloggningen logga in som en tjänst** princip.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fakturering för första och andra mellanlagrings aktiviteter

De första mellanlagrings aktiviteterna som körs på din egen värd-IR faktureras separat på samma sätt som alla data förflyttnings aktiviteter som körs på lokal IR-överföring faktureras som anges i pris artikeln för [ADF-datapipeline](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De andra mellanlagrings aktiviteterna som körs på din Azure-SSIS IR debiteras inte separat, utan att Azure-SSIS IR som körs faktureras enligt vad som anges i artikeln [Azure-SSIS IR prissättning](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Aktuella begränsningar

- För närvarande stöds endast data flödes aktiviteter med ODBC/OLEDB/Flated File Connection Manager och ODBC/OLEDB/Flated File sources eller OLEDB destination. 
- Endast Azure Blob Storage länkade tjänster som kon figurer ATS med **konto nyckeln**/**SAS-URI**/**tjänstens huvud namns** autentisering stöds för närvarande.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din egen värd-IR som en proxy för din Azure-SSIS IR, kan du distribuera och köra dina paket för att komma åt data lokalt som kör SSIS-paket aktiviteter i ADF-pipeline, se [kör SSIS-paket som EXECUTE SSIS Package-aktiviteter i ADF-pipeline](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
