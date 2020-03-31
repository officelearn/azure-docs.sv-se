---
title: Konfigurera en självvärd integrationskörning som en proxy för SSIS
description: Lär dig hur du konfigurerar en självvärd för integrationskörning som en proxy för en Azure-SSIS Integration Runtime.
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
ms.date: 03/27/2020
ms.openlocfilehash: 9a1923057bc318869f491791520aacb4d0d17591
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346626"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Konfigurera en självvärd iR som en proxy för en Azure-SSIS IR i Azure Data Factory

I den hÃ¤r artikeln beskrivs hur du kör SSIS-paket (SQL Server Integration Services) fÃ¤r en Azure-SSIS Integration Runtime (Azure-SSIS IR) i Azure Data Factory med en själv värd integrationskörning (iR fÃ¤rsvÃ¤ndet som en proxy. 

Med den här funktionen kan du komma åt data lokalt utan att behöva ansluta till [din Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Funktionen är användbar när företagets nätverk har en konfiguration som är för komplex eller en princip som är för restriktiv för att du ska kunna injicera din Azure-SSIS IR i det.

Den här funktionen delar upp alla SSIS-dataflödesaktiviteter som har en lokal datakälla i två mellanlagringsaktiviteter: 
* Den första uppgiften, som körs på din självvärdbaserade IR, flyttar först data från den lokala datakällan till ett mellanlagringsområde i din Azure Blob-lagring.
* Den andra uppgiften, som körs på din Azure-SSIS IR, flyttar sedan data från mellanlagringsområdet till det avsedda datamålet.

Andra fördelar och funktioner för den här funktionen gör att du till exempel kan konfigurera din självvärderade IR i regioner som ännu inte stöds av en Azure-SSIS IR och tillåta den offentliga statiska IP-adressen för din självvärdbaserade IR på brandväggen för dina datakällor.

## <a name="prepare-the-self-hosted-ir"></a>Förbered den självvärda IR

Om du vill använda den här funktionen skapar du först en datafabrik och konfigurerar en Azure-SSIS IR i den. Om du inte redan har gjort det följer du instruktionerna i [Konfigurera en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure).

Du konfigurerar sedan din självvärdbaserade IR i samma datafabrik där din Azure-SSIS IR har konfigurerats. Det gör du genom att se [Skapa en självvärd IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

Slutligen hämtar och installerar du den senaste versionen av den självvärdbaserade IR:et, liksom ytterligare drivrutiner och körning, på din lokala dator eller virtuella Azure-dator (VM), enligt följande:
- Ladda ner och installera den senaste versionen av den [självvärda IR](https://www.microsoft.com/download/details.aspx?id=39717).
- Om du använder OLEDB-anslutningar (Object Linking and Embedding Database) i dina paket hämtar och installerar du relevanta OLEDB-drivrutiner på samma dator där din självvärdbaserade IR är installerad, om du inte redan har gjort det.  

  Om du använder den tidigare versionen av OLEDB-drivrutinen för SQL Server (SQL Server Native Client [SQLNCLI]) [laddar du ned 64-bitarsversionen](https://www.microsoft.com/download/details.aspx?id=50402).  

  Om du använder den senaste versionen av OLEDB-drivrutinen för SQL Server (MSOLEDBSQL) [laddar du ned 64-bitarsversionen](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Om du använder OLEDB-drivrutiner för andra databassystem, till exempel PostgreSQL, MySQL, Oracle och så vidare, kan du ladda ner 64-bitarsversionerna från deras webbplatser.
- Om du inte redan har gjort det [kan du hämta och installera 64-bitarsversionen av Visual C++ (VC) på](https://www.microsoft.com/download/details.aspx?id=40784) samma dator där din självvärdbaserade IR är installerad.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Förbereda Azure Blob-lagringslänkad tjänst för mellanlagring

Om du inte redan har gjort det skapar du en Azure Blob-lagringslänkad tjänst i samma datafabrik där din Azure-SSIS IR har konfigurerats. Det gör du finns i [Skapa en Azure-datafabrikslänkad tjänst](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service). Var noga med att göra följande:
- För **datalagring**väljer du **Azure Blob Storage**.  
- För **Connect via integrationskörning**väljer du **AutoResolveIntegrationRuntime** (inte din Azure-SSIS IR eller din självvärda IR), eftersom vi använder standard azure IR för att hämta åtkomstbehörighet för din Azure Blob Storage  
- För **autentiseringsmetod**väljer du **Kontonyckel,** **SAS URI**eller **Tjänsthuvudnamn**.  

    >[!TIP]
    >Om du väljer metoden **Tjänsthuvudnamn** ger du tjänstens huvudnamn minst en *roll för storage blob data contributor.*  Mer information finns i [Azure Blob storage connector](connector-azure-blob-storage.md#linked-service-properties).

![Förbereda Azure Blob-lagringslänkad tjänst för mellanlagring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Konfigurera en Azure-SSIS IR med din självvärda IR som proxy

Efter att ha förberett din självvärderade IR- och Azure Blob-lagringslänkade tjänst för mellanlagring kan du nu konfigurera din nya eller befintliga Azure-SSIS IR med den självvärderade IR:et som en proxy i din datafabriksportal eller -app. Innan du gör det, men om din befintliga Azure-SSIS IR redan körs, stoppa den och starta sedan om den.

1. I **fönstret Inställningar för integreringskörning** hoppar du över avsnitten **Allmänna inställningar** och **SQL-inställningar** genom att välja **Nästa**. 

1. Gör följande i avsnittet **Avancerade inställningar:**

   1. Markera kryssrutan **Konfigurera självvärdförd integreringskörning som en proxy för din Azure-SSIS-integreringskörning.** 

   1. I listrutan **Självvärdförd integreringskörning** väljer du din befintliga självvärderade IR som proxy för Azure-SSIS IR.

   1. I listrutan **Förproduktionslagringslänkad tjänst** väljer du din befintliga Azure Blob-lagringslänkade tjänst eller skapar en ny för mellanlagring.

   1. I rutan **Mellanlagringssökväg** anger du en blob-behållare i det valda Azure Blob-lagringskontot eller lämnar den tom om du vill använda en standard för mellanlagring.

   1. Välj **Fortsätt**.

   ![Avancerade inställningar med en självvärd IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Du kan också konfigurera din nya eller befintliga Azure-SSIS IR med självvärdna IR som proxy med hjälp av PowerShell.

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

Genom att använda antingen det senaste SSDT-tillägget med SSIS-projekttillägg för `ConnectByProxy` Visual Studio eller en fristående installatör kan du hitta en ny egenskap som har lagts till i OLEDB- eller Flat File-anslutningshanterare.
* [Ladda ner SSDT med SSIS Projects-tillägget för Visual Studio](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Hämta den fristående installationsprogrammet](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

När du utformar nya paket som innehåller dataflödesuppgifter med OLEDB- eller Flat File-källor, som gör att du kan komma åt databaser eller filer lokalt, kan du aktivera den här egenskapen genom att ange den till *True* i **egenskapsfönstret** för relevanta anslutningshanterare.

![Aktivera egenskapen ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Du kan också aktivera den här egenskapen när du kör befintliga paket, utan att behöva ändra dem manuellt en efter en.  Det finns två alternativ:
- **Alternativ A:** Öppna, återskapa och distribuera om projektet som innehåller de paket som innehåller de senaste SSDT som ska köras på din Azure-SSIS IR. Du kan sedan aktivera egenskapen genom att ange den till *True* för relevanta anslutningshanterare. När de kör paket från SSMS visas dessa anslutningshanterare på fliken **Anslutningshanterare** i popup-fönstret **Kör paket.**

  ![Aktivera egenskapen ConnectByProxy2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Du kan också aktivera egenskapen genom att ange den till *True* för relevanta anslutningshanterare som visas på fliken **Anslutningshanterare** för [Kör SSIS-paketaktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när de kör paket i Data Factory-pipelines.
  
  ![Aktivera egenskapen ConnectByProxy3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Alternativ B:** Distribuera om projektet som innehåller de paket som ska köras på din SSIS IR. Du kan sedan aktivera egenskapen genom `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`att ange dess egenskapssökväg och ställa in den till *True* som en egenskapsöversidosättning på fliken **Avancerat** i popup-fönstret **Kör paket** när du kör paket från SSMS.

  ![Aktivera egenskapen ConnectByProxy4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Du kan också aktivera egenskapen genom `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`att ange dess egenskapssökväg och ange den till *True* som en **egenskapsöversidosättning** på fliken Egenskapsidosättning i [Kör SSIS-paketaktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när du kör paket i Data Factory-pipelines.
  
  ![Aktivera egenskapen ConnectByProxy5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Felsöka den första och andra mellanlagringen

På din självvärderade IR kan du hitta runtime-loggarna i mappen *C:\ProgramData\SSISTelemetry* och körningsloggarna för de första mellanlagringsuppgifterna i mappen *C:\ProgramData\SSISTelemetry\ExecutionLog.*  Du hittar körningsloggarna för andra mellanlagringsuppgifter i din SSISDB eller angivna loggningssökvägar, beroende på om du lagrar dina paket i SSISDB eller filsystem, filresurser eller Azure-filer. Du kan också hitta de unika ID:t för första mellanlagringsuppgifter i körningsloggarna för andra mellanlagringsuppgifter. 

![Unikt ID för den första mellanlagringsuppgiften](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Använda Windows-autentisering i mellanlagringsuppgifter

Om mellanlagringsuppgifterna på din självvärderade IR kräver Windows-autentisering [konfigurerar du SSIS-paketen så att de använder samma Windows-autentisering](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Dina mellanlagringsuppgifter anropas med det självvärderade IR-tjänstkontot *(NT SERVICE\DIAHostService*, som standard), och dina datalager kommer att nås med Windows-autentiseringskontot. Båda kontona kräver att vissa säkerhetsprinciper tilldelas dem. På den självvärdbaserade IR-datorn går du till**Tilldelning**av**lokala principer för lokala principer** > för **lokal säkerhet** > och gör sedan följande:

1. Tilldela *justera minneskvoter för en process* och *ersätt en tokenprincip för processnivå* till det självvärderade IR-tjänstkontot. Detta bör inträffa automatiskt när du installerar din självvärdbaserade IR med standardtjänstkontot. Om den inte gör det, tilldela dessa principer manuellt. Om du använder ett annat tjänstkonto tilldelar du samma principer till det.

1. Tilldela *inloggningsprincipen som en tjänstprincip* till Windows Authentication-kontot.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fakturering för den första och andra mellanlagringen

De första mellanlagringsuppgifterna som körs på din självvärdbaserade IR faktureras separat, precis som alla dataförflyttningsaktiviteter som körs på en självvärd ir faktureras. Detta anges i azure [data factory-datapipelinens prisartikel.](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

Den andra mellanlagringsuppgifterna som körs på din Azure-SSIS IR faktureras inte separat, men din körning av Azure-SSIS IR faktureras enligt vad som anges i [azure-SSIS IR-prisartikeln.](https://azure.microsoft.com/pricing/details/data-factory/ssis/)

## <a name="enabling-tls-12"></a>Aktivera TLS 1.2

Om du behöver använda starkt kryptografi/säkrare nätverksprotokoll (TLS 1.2) och inaktivera äldre SSL/TLS-versioner på din självvärdbaserade IR kan du hämta och köra *main.cmd-skriptet* som finns i *customsetupScript/UserScenarios/TLS 1.2-mappen* i vår offentliga förhandsgranskningsbehållare.  Med [Azure Storage Explorer](https://storageexplorer.com/)kan du ansluta till vår offentliga förhandsversionsbehållare genom att ange följande SAS URI:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Aktuella begränsningar

- Endast dataflödesuppgifter med ODBC-källor (Open Database Connectivity)/OLEDB/Flat File stöds för närvarande. 
- Endast Azure Blob-lagringslänkade tjänster som är konfigurerade med *kontonyckel*, *SAS-signatur (Shared Access Signature) eller* *Autentisering av tjänsthuvudnamn* stöds för närvarande.
- *ParameterMapping* i OLEDB-källa stöds inte ännu. Som en lösning använder du *SQL Command From Variable* som *AccessMode* och använder *Uttryck* för att infoga variabler/parametrar i ett SQL-kommando. Som en illustration, se *ParameterMappingSample.dtsx-paketet* som finns i mappen *SelfHostedIRProxy/Limitations* i vår offentliga förhandsversionsbehållare. Med Hjälp av Azure Storage Explorer kan du ansluta till vår offentliga förhandsversionsbehållare genom att ange ovanstående SAS URI.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat din självvärderade IR som en proxy för din Azure-SSIS IR kan du distribuera och köra dina paket för att komma åt data lokalt som Kör SSIS-paketaktiviteter i Data Factory-pipelines. Mer information finns i [Kör SSIS-paket som Kör SSIS-paketaktiviteter i Data Factory-pipelines](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
