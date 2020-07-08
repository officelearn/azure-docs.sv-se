---
title: Anpassa installations programmet för en Azure-SSIS Integration Runtime
description: Den här artikeln beskriver hur du använder det anpassade installations gränssnittet för en Azure-SSIS Integration Runtime för att installera ytterligare komponenter eller ändra inställningar
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 06/03/2020
ms.openlocfilehash: 576861265771977f7e13140dd595f47bf556e585
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331907"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Anpassa installations programmet för en Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den anpassade installationen för en Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) i Azure Data Factory (ADF) tillhandahåller ett gränssnitt för att lägga till dina egna steg under etableringen eller omkonfigurationen av dina Azure-SSIS IR. 

Med hjälp av den anpassade installationen kan du ändra standard konfiguration eller miljö till, till exempel starta ytterligare Windows-tjänster, bevara autentiseringsuppgifter för fil resurser eller använda starkt kryptografiskt/säkrare nätverks protokoll (TLS 1,2). Eller så kan du installera ytterligare komponenter för anpassad/tredje part, till exempel sammansättningar, driv rutiner eller tillägg, på varje nod i Azure-SSIS IR. Mer information om inbyggda/förinstallerade komponenter finns i [inbyggda/förinstallerade komponenter på Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime).

Du kan göra anpassade inställningar på Azure-SSIS IR på något av två sätt: 
* **Standard anpassad installation med ett skript**: Förbered ett skript och dess associerade filer och ladda upp dem tillsammans till en BLOB-behållare i ditt Azure Storage-konto. Du anger sedan en signatur för delad åtkomst (SAS) Uniform Resource Identifier (URI) för din behållare när du konfigurerar eller konfigurerar om Azure-SSIS IR. Varje nod i Azure-SSIS IR laddar sedan ned skriptet och dess tillhör ande filer från din behållare och kör den anpassade installationen med förhöjd behörighet. När den anpassade installationen är slutförd laddar varje nod standardutdata för körning och andra loggar till din behållare.
* **Express anpassad installation utan skript**: kör några vanliga system konfigurationer och Windows-kommandon eller installera några populära eller rekommenderade ytterligare komponenter utan att använda några skript.

Du kan installera både kostnads fria, icke-licensierade komponenter och betalda komponenter med standard-och Express-anpassade installationer. Om du är en oberoende program varu leverantör (ISV) kan du läsa mer i [utveckla betalda eller licensierade komponenter för Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> För att kunna dra nytta av framtida förbättringar rekommenderar vi att du använder v3 eller en senare serie av noder för Azure-SSIS IR med anpassad installation.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller endast för anpassade standardinställningar:

- Om du vill använda *gacutil.exe* i skriptet för att installera sammansättningar i GAC (Global Assembly Cache) måste du ange *gacutil.exe* som en del av din anpassade installation. Eller så kan du använda kopian som finns i vår *offentliga för hands versions* behållare, som beskrivs senare i avsnittet "instruktioner".

- Om du vill referera till en undermapp i skriptet kan *msiexec.exe* inte använda `.\` notationen för att referera till rotmappen. Använd ett kommando som `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` i stället för `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Administrativa resurser eller dolda nätverks resurser som skapas automatiskt av Windows stöds för närvarande inte på Azure-SSIS IR.

- IBM iSeries Access ODBC-drivrutinen stöds inte på Azure-SSIS IR. Du kan se installations fel under den anpassade installationen. Kontakta IBM-supporten om du behöver hjälp.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill anpassa din Azure-SSIS IR behöver du följande objekt:

- [En Azure-prenumeration](https://azure.microsoft.com/)

- [Etablera Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Ett Azure Storage-konto](https://azure.microsoft.com/services/storage/). Krävs inte för Express-anpassade installationer. För standard anpassade installations program laddar du upp och lagrar det anpassade installations skriptet och dess associerade filer i en BLOB-behållare. Den anpassade installations processen överför även körnings loggarna till samma BLOB-behållare.

## <a name="instructions"></a>Instruktioner

Du kan etablera eller konfigurera om Azure-SSIS IR med anpassade inställningar i ADF UI. Om du vill göra samma användning av PowerShell kan du hämta och installera [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Anpassad standard installation

Utför följande steg för att etablera eller konfigurera om Azure-SSIS IR med anpassade standard installations program.

1. Förbered det anpassade installations skriptet och dess associerade filer (till exempel. bat,. cmd,. exe,. dll,. msi eller. ps1-filer).

   * Du måste ha en skript fil med namnet *main. cmd*, vilket är start punkten för den anpassade installationen.  
   * För att säkerställa att skriptet kan köras i bakgrunden rekommenderar vi att du testar det på din lokala dator först.  
   * Om du vill att ytterligare loggar som genereras av andra verktyg (till exempel *msiexec.exe*) ska överföras till din behållare anger du den fördefinierade miljövariabeln, `CUSTOM_SETUP_SCRIPT_LOG_DIR` som loggmappen i dina skript (till exempel *msiexec/i xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Ladda ned, installera och öppna [Azure Storage Explorer](https://storageexplorer.com/).

   a. Under **(lokal och ansluten)** högerklickar du på **lagrings konton**och väljer sedan **Anslut till Azure Storage**.

      ![Ansluta till Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Välj **Använd ett lagrings konto namn och nyckel**och välj sedan **Nästa**.

      ![Använda lagringskontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Ange namnet och nyckeln för ditt Azure Storage-konto, Välj **Nästa**och välj sedan **Anslut**.

      ![Ange lagrings kontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Under ditt anslutna Azure Storage-konto högerklickar du på **BLOB-behållare**, väljer **skapa BLOB-behållare**och namnger den nya behållaren.

      ![Skapa en blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Välj den nya behållaren och ladda upp det anpassade installations skriptet och dess tillhör ande filer. Se till att ladda upp *main. cmd* på den översta nivån i din behållare, inte i någon mapp. Se också till att din behållare bara innehåller de nödvändiga anpassade installationsfilerna, så att det tar lång tid att ladda ned dem till Azure-SSIS IR senare. Maximal varaktighet för en anpassad installation är för närvarande 45 minuter innan tids gränsen uppnåddes. Detta inkluderar tiden för att ladda ned alla filer från behållaren och installera dem på Azure-SSIS IR. Om installations programmet kräver mer tid kan du generera ett support ärende.

      ![Ladda upp filer till BLOB-behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Högerklicka på behållaren och välj sedan **Hämta signatur för delad åtkomst**.

      ![Hämta signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   ex. Skapa SAS-URI: n för din behållare med tillräckligt lång förfallo tid och med behörigheten Läs/skriv/lista. Du behöver SAS-URI: n för att ladda ned och köra det anpassade installations skriptet och dess associerade filer när en nod i Azure-SSIS IR återställs eller startas om. Du måste ha Skriv behörighet för att överföra konfigurations körnings loggar.

      > [!IMPORTANT]
      > Se till att SAS-URI: n inte upphör att gälla och att de anpassade installations resurserna alltid är tillgängliga under hela livs cykeln för din Azure-SSIS IR, från att skapas till borttagning, särskilt om du regelbundet stoppar och startar Azure-SSIS IR under den här perioden.

      ![Generera signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopiera och spara SAS-URI: n för din behållare.

      ![Kopiera och spara signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. När du etablerar eller omkonfigurerar ditt Azure-SSIS IR på ADF-gränssnittet, markerar du kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** på sidan **Avancerade inställningar** i installations fönstret för **integration runtime** och anger din behållares SAS-URI i rutan **SAS URI för den anpassade installations behållaren** .

   ![Avancerade inställningar med anpassade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

### <a name="express-custom-setup"></a>Express anpassad installation

Följ stegen nedan om du vill etablera eller konfigurera om Azure-SSIS IR med anpassade installations alternativ för Express.

1. När du etablerar eller omkonfigurerar ditt Azure-SSIS IR på ADF-gränssnittet, markerar du kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** på sidan **Avancerade inställningar** i installations fönstret för **integration runtime** . 

1. Välj **nytt** för att öppna fönstret **Lägg till Express anpassad installation** och välj sedan en typ i list rutan **snabb starts typ** :

   * Om du väljer **Kör cmdkey-kommandoraden** kan du spara autentiseringsuppgifter för dina fil resurser eller Azure Files resurser på Azure-SSIS IR genom att ange ditt mål dator namn eller domän namn, konto namn eller användar namn och konto nyckel eller lösen ord i rutorna **/Add**, **/User**och **/pass** . Detta påminner om att köra Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) -kommandot på den lokala datorn.
   
   * Om du väljer **miljö variabel typen Lägg** till kan du lägga till Windows-miljövariabler som ska användas i dina paket som körs på Azure-SSIS IR genom att ange ditt miljö variabel namn och värde i rutorna **variabel namn** och **variabel värde** . Detta påminner om att köra Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) -kommandot på den lokala datorn.

   * Om du väljer **Installera licensierad komponent** typ kan du sedan välja en integrerad komponent från våra ISV-partner i list rutan **komponent namn** :

     * Om du väljer **uppgifts fabriks komponenten SentryOne** kan du installera komponenterna i [aktivitets fabriken](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) från SentryOne på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **2019.4.3**.

     * Om du väljer **oh22's-Hedda. I/o** -komponenten kan du installera [Hedda. IO](https://hedda.io/ssis-component/) -datakvalitet/rengörings komponent från oh22 på din Azure-SSIS IR efter köpet av tjänsten. Den aktuella integrerade versionen är **1.0.13**.

     * Om du väljer komponenten **oh22's SQLPhonetics.net** kan du installera [SQLPhonetics.net](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) Data Quality/Matching-komponenten från oh22 på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **1.0.43**.

     * Om du väljer komponenten **SSIS Integration Toolkit för KingswaySoft** kan du installera [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) Suite för CRM/ERP/Marketing/Collaboration-appar, till exempel Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce marknadsförings moln osv. från KingswaySoft på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **2019,2**.

     * Om du väljer komponenten **SSIS produktivitets paket för KingswaySoft** kan du installera [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) Suite-komponenter från KingswaySoft på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **10,0**.

     * Om du väljer den **Theobald program varans xtract** -komponent kan du installera [xtract](https://theobald-software.com/en/xtract-is/) -serien för SAP-system (ERP, s/4HANA, BW) från Theobald program vara på din Azure-SSIS IR genom att dra & släppa/Ladda upp den produkt licens fil som du har köpt från dem i rutan **licens fil** . Den aktuella integrerade versionen är **6.1.1.3**.

Dina anpassade installations alternativ kommer att visas på sidan **Avancerade inställningar** . Om du vill ta bort dem markerar du kryss rutorna och väljer sedan **ta bort**.

### <a name="azure-powershell"></a>Azure PowerShell

Utför följande steg för att etablera eller konfigurera om Azure-SSIS IR med anpassade inställningar med Azure PowerShell.

1. Om din Azure-SSIS IR redan har startat/körs stoppar du det först.

1. Du kan sedan lägga till eller ta bort anpassade inställningar genom att köra `Set-AzDataFactoryV2IntegrationRuntime` cmdleten innan du startar Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

1. När den anpassade standard installationen slutförs och din Azure-SSIS IR startas, kan du hitta standardutdata för *main. cmd* och andra körnings loggar i mappen *main. cmd. log* i din behållare.

### <a name="standard-custom-setup-samples"></a>Vanliga anpassade installations exempel

Utför följande steg om du vill visa och återanvända några exempel på vanliga anpassade inställningar.

1. Anslut till vår offentliga för hands versions behållare med hjälp av Azure Storage Explorer.

   a. Under **(lokal och ansluten)** högerklickar du på **lagrings konton**, väljer **Anslut till Azure Storage**, väljer **Använd en anslutnings sträng eller en signatur-URI för delad åtkomst**och välj sedan **Nästa**.

      ![Ansluta till Azure Storage med signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Välj **Använd en SAS-URI** och ange följande SAS-URI i rutan **URI** :

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Ange signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Välj **Nästa**och välj sedan **Anslut**.

   d. I den vänstra rutan väljer du den anslutna **publicpreview** -behållaren och dubbelklickar sedan på mappen *CustomSetupScript* . I den här mappen finns följande objekt:

      * En *exempel* mapp som innehåller en anpassad installation för att installera en grundläggande aktivitet på varje nod i Azure-SSIS IR. Aktiviteten gör ingenting men väntar i ett par sekunder. Mappen innehåller också en *Gacutil* -mapp vars hela innehåll (*gacutil.exe*, *gacutil.exe.config*och *1033\gacutlrc.dll*) kan kopieras som är till din behållare.

      * En *UserScenarios* -mapp som innehåller flera anpassade installations exempel från verkliga användar scenarier.

        ![Innehåll i offentlig för hands versions behållare](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbelklicka på mappen *UserScenarios* för att hitta följande objekt:

      * En *.net FRAMEWORK 3,5* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera en tidigare version av .NET Framework som kan krävas för anpassade komponenter på varje nod i Azure-SSIS IR.

      * En *BCP* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera SQL Server kommando rads verktyg (*MsSqlCmdLnUtils.msi*), inklusive Mass kopierings programmet (*BCP*) på varje nod i Azure-SSIS IR.

      * En *Excel* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera C#-sammansättningar och-bibliotek som du kan använda i skript uppgifter för att dynamiskt läsa och skriva Excel-filer på varje nod i Azure-SSIS IR. 
      
        Först laddar du ned [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) och [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)och laddar sedan upp dem tillsammans med *main. cmd* till din behållare. Alternativt, om du bara vill använda standard anslutnings hanteraren för Excel, Excel-källan och Excel-målet, är den nödvändiga åtkomsten för Access redan förinstallerad på din Azure-SSIS IR, så du behöver inte någon anpassad installation.
      
      * En *MySQL ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera MySQL ODBC-drivrutinerna på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren, källan och målet för att ansluta till MySQL-servern. 
     
        Börja [med att hämta de senaste 64-bitars-och 32-bitars versionerna av installations program för MySQL ODBC-drivrutinen](https://dev.mysql.com/downloads/connector/odbc/) (till exempel *mysql-connector-odbc-8.0.13-winx64.msi* och *mysql-connector-odbc-8.0.13-win32.msi*) och överför dem sedan tillsammans med *main. cmd* till din behållare.

      * En *Oracle Enterprise* -mapp som innehåller ett anpassat installations skript (*main. cmd*) och tyst installations konfigurations fil (*client. rsp*) för att installera Oracle-kopplingarna och OCI-drivrutinen på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda anslutnings hanteraren för Oracle, källa och mål för att ansluta till Oracle-servern. 
      
        Först laddar du ned Microsoft Connector v 5.0 för Oracle (*AttunitySSISOraAdaptersSetup.msi* och *AttunitySSISOraAdaptersSetup64.msi*) från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) och den senaste Oracle-klienten (till exempel *winx64_12102_client.zip*) från [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)och överför dem sedan tillsammans med *main. cmd* och *client. rsp* till din behållare. Om du använder TNS för att ansluta till Oracle måste du också hämta *Tnsnames. ora*, redigera den och ladda upp den till din behållare, så att den kan kopieras till mappen Oracle-installation under installationen.

      * En *Oracle STANDARD ADO.net* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle ODP.net-drivrutinen på varje nod i Azure-SSIS IR. Med den här installationen kan du använda anslutnings hanteraren för ADO.NET, källa och mål för att ansluta till Oracle-servern. 
      
        Först [laddar du ned den senaste Oracle ODP.net-drivrutinen](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (till exempel *ODP.NET_Managed_ODAC122cR1.zip*) och laddar sedan upp den tillsammans med *main. cmd* i din behållare.
       
      * En *Oracle standard-ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle ODBC-drivrutinen och konfigurera data källans namn (DSN) på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren, källa och mål eller Power Query anslutnings hanteraren och källa med ODBC-datakälla för att ansluta till Oracle-servern. 
      
        Börja med att hämta den senaste Oracle Instant-klienten (Basic-paket eller Basic lite-paket) och ODBC-paket och ladda sedan upp dem tillsammans med *main. cmd* till din behållare:
        * [Hämta 64-bitars paket](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-paket: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic lite-paket: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC-paket: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Hämta 32-bitars paket](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-paket: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic lite-paket: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC-paket: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * En *Oracle standard OLEDB* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle OLEDB-drivrutinen på varje nod i Azure-SSIS IR. Med den här installationen kan du använda OLEDB anslutnings hanteraren, källa och mål för att ansluta till Oracle-servern. 
     
        Först [laddar du ned den senaste Oracle OLEDB-drivrutinen](https://www.oracle.com/partners/campaign/index-090165.html) (till exempel *ODAC122010Xcopy_x64.zip*) och laddar sedan upp den tillsammans med *main. cmd* i din behållare.

      * En *POSTGRESQL ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera POSTGRESQL ODBC-drivrutinerna på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren, källan och målet för att ansluta till PostgreSQL-servern. 
     
        Börja [med att hämta de senaste 64-bitars-och 32-bitars versionerna av POSTGRESQL ODBC-drivrutin installations](https://www.postgresql.org/ftp/odbc/versions/msi/) program (till exempel *psqlodbc_x64.msi* och *psqlodbc_x86.msi*) och ladda upp dem tillsammans med *main. cmd* till din behållare.

      * En *SAP BW* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera SAP .net connector-sammansättningen (*librfc32.dll*) på varje nod i Azure-SSIS IR Enterprise Edition. Med den här installationen kan du använda anslutnings hanteraren för SAP Business Warehouse (BW), källan och målet för att ansluta till den SAP BW servern. 
      
        Först överför du 64-bitars-eller 32-bitars versionen av *librfc32.dll* från SAP-installationsmappen tillsammans med *main. cmd* till din behållare. Skriptet kopierar sedan SAP-sammansättningen till mappen *%windir%\syswow64* eller mappen *%windir%\System32* under installationen.

      * En *lagringsmapp* som innehåller ett anpassat installations skript (*main. cmd*) för att installera Azure PowerShell på varje nod i Azure-SSIS IR. Med den här installationen kan du distribuera och köra SSIS-paket som kör [PowerShell-skript för att manipulera ditt Azure Storage-konto](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Kopiera *main. cmd*, ett exempel *AzurePowerShell.msi* (eller Använd den senaste versionen) och *storage.ps1* till din behållare. Använd *PowerShell. dtsx* som en mall för dina paket. Paket mal len kombinerar en [hämtnings uppgift för Azure-Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), som laddar ned *storage.ps1* som ett ändrings Bart PowerShell-skript och en process för att [köra processer](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), som kör skriptet på varje nod.

      * En *TERADATA* -mapp som innehåller ett anpassat installations skript (*main. cmd*), dess associerade fil (*install. cmd*) och installations paket (*. msi*). De här filerna installerar Teradata-kopplingarna, Teradata Parallel transporter (TPT) API och ODBC-drivrutinen på varje nod i Azure-SSIS IR Enterprise Edition. Med den här installationen kan du använda Teradata anslutnings hanteraren, källa och mål för att ansluta till Teradata-servern. 
      
        Först [laddar du ned Teradata-verktyg och-verktyg 15. x zip-filen](http://partnerintelligence.teradata.com) (till exempel *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) och laddar sedan upp den tillsammans med de tidigare nämnda *. cmd* -och *MSI* -filerna till din behållare.

      * En *TLS 1,2* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att använda starkt kryptografiskt/säkrare nätverks protokoll (TLS 1,2) och inaktivera äldre SSL/TLS-versioner på varje nod i Azure-SSIS IR.

      * En *ZULU OPENJDK* -mapp som innehåller ett anpassat installations skript (*main. cmd*) och PowerShell-fil (*install_openjdk.ps1*) för att installera ZULU-OPENJDK på varje nod i Azure-SSIS IR. Med den här installationen kan du använda Azure Data Lake Store och flexibla fil anslutningar för att bearbeta ORC-och Parquet-filer. Mer information finns i [Azure Feature Pack för integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Hämta först [de senaste Zulu-openjdk](https://www.azul.com/downloads/zulu/zulu-windows/) (till exempel *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) och ladda upp den tillsammans med *main. cmd* och *install_openjdk.ps1* till din behållare.

        ![Mappar i mappen användar scenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Om du vill återanvända de här standard anpassade installations exemplen kopierar du innehållet i den valda mappen till din behållare.

1. När du etablerar eller omkonfigurerar ditt Azure-SSIS IR på ADF-gränssnittet, markerar du kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** på sidan **Avancerade inställningar** i installations fönstret för **integration runtime** och anger din behållares SAS-URI i rutan **SAS URI för den anpassade installations behållaren** .
   
1. När du etablerar eller konfigurerar om din Azure-SSIS IR att använda Azure PowerShell stoppar du den om den redan har startats/körs, kör `Set-AzDataFactoryV2IntegrationRuntime` cmdleten med SAS-URI: n för din behållare som `SetupScriptContainerSasUri` parameter värde och startar sedan Azure-SSIS IR.

1. När den anpassade standard installationen slutförs och din Azure-SSIS IR startas, kan du hitta standardutdata för *main. cmd* och andra körnings loggar i mappen *main. cmd. log* i din behållare.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Enterprise-versionen av Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Utveckla betalda eller licensierade komponenter för Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
