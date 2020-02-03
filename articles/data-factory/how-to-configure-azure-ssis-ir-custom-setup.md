---
title: Anpassa installations programmet för Azure-SSIS integration runtime
description: Den här artikeln beskriver hur du använder det anpassade installations gränssnittet för Azure-SSIS integration runtime för att installera ytterligare komponenter eller ändra inställningar
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
ms.date: 02/01/2020
ms.openlocfilehash: e85ef22542fc162648dbfc637892cf7e580c6aac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964557"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Anpassa installations programmet för Azure-SSIS integration runtime

Det anpassade installations gränssnittet för Azure-SSIS Integration Runtime tillhandahåller ett gränssnitt för att lägga till egna installations steg under etableringen eller omkonfigurationen av din Azure-SSIS IR. 

Med anpassad installation kan du ändra standard operativ konfigurationen eller miljön (till exempel för att starta ytterligare Windows-tjänster eller spara autentiseringsuppgifter för fil resurser) eller installera ytterligare komponenter (till exempel sammansättningar, driv rutiner eller tillägg) på varje nod i Azure-SSIS IR.

Det finns två sätt att utföra anpassade installationer på din Azure-SSIS IR: Express anpassade installationer utan skript och standard anpassade inställningar med skript.

Med anpassade installations alternativ kan du köra några vanliga system konfigurationer/Windows-kommandon eller installera några populära/rekommenderade ytterligare komponenter utan att använda några skript.  

Med standard anpassade installations program måste du förbereda ett skript och dess associerade filer och ladda upp dem tillsammans till en BLOB-behållare i ditt Azure Storage-konto. Du anger sedan en signatur för delad åtkomst (SAS) Uniform Resource Identifier (URI) för din behållare när du etablerar eller konfigurerar om Azure-SSIS IR. Varje nod i Azure-SSIS IR hämtar sedan skriptet och dess associerade filer från din behållare och kör anpassad installation med utökade privilegier. När den anpassade installationen är slutförd, kommer varje nod att överföra standardutdata för körning och andra loggar till din behållare.

Du kan installera både kostnads fria/olicensierade och betalda/licensierade komponenter med anpassade installations alternativ för Express/standard. Om du är en ISV, se vår dokumentation om [hur du utvecklar betalda eller licensierade komponenter för Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Noder i v2-serien av Azure-SSIS IR lämpar sig inte för anpassad installation, så Använd v3-serie-noderna i stället.  Om du redan använder noderna i v2-serien växlar du till att använda v3-seriens noder så snart som möjligt.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller endast för standard anpassade inställningar:

- Om du vill använda `gacutil.exe` i skriptet för att installera sammansättningar i GAC (Global Assembly Cache) måste du ange `gacutil.exe` som en del av din anpassade installation eller använda kopian som finns i vår offentliga för hands versions behållare nedan.

- Om du vill referera till en undermapp i skriptet stöder `msiexec.exe` inte `.\` notation för att referera till rotmappen. Använd ett kommando som `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` i stället för `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Administrativa resurser, d.v.s. dolda nätverks resurser som skapas automatiskt av Windows, stöds för närvarande inte på Azure-SSIS IR.

- IBM iSeries Access ODBC-drivrutinen stöds inte på Azure-SSIS IR. Du kan se installations fel under den anpassade installationen. Kontakta IBM-supporten om du behöver hjälp.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att anpassa din Azure-SSIS IR behöver du följande saker:

- [Azure-prenumeration](https://azure.microsoft.com/)

- [Etablering av Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Ett Azure Storage konto](https://azure.microsoft.com/services/storage/). Krävs inte för Express-anpassade installationer. För standard anpassade installations program laddar du upp och lagrar det anpassade installations skriptet och dess associerade filer i en BLOB-behållare. Den anpassade installations processen överför även körnings loggarna till samma BLOB-behållare.

## <a name="instructions"></a>Instruktioner

1. Om du vill etablera eller konfigurera om Azure-SSIS IR med PowerShell kan du hämta och installera [Azure PowerShell](/powershell/azure/install-az-ps). För snabba anpassade installationer går du vidare till steg 4.

1. Förbered det anpassade installations skriptet och dess associerade filer (till exempel. bat,. cmd,. exe,. dll,. msi eller. ps1-filer).

   1. Du måste ha en skript fil med namnet `main.cmd`, vilket är start punkten för den anpassade installationen.

   1. Du måste kontrol lera att skriptet kan köras i bakgrunden, men vi rekommenderar att du testar skriptet på den lokala datorn först.

   1. Om du vill att ytterligare loggar som genereras av andra verktyg (till exempel `msiexec.exe`) ska överföras till din behållare anger du den fördefinierade miljövariabeln `CUSTOM_SETUP_SCRIPT_LOG_DIR` som loggmappen i dina skript (till exempel `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Ladda ned, installera och starta [Azure Storage Explorer](https://storageexplorer.com/).

   1. Under **(lokal och ansluten)** , högerklickar du på **lagrings konton** och väljer **Anslut till Azure Storage**.

      ![Anslut till Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Välj **Använd ett lagrings konto namn och nyckel** och välj **Nästa**.

      ![Använda lagringskontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Ange ditt Azure Storage konto namn och nyckel, Välj **Nästa**och välj sedan **Anslut**.

      ![Ange namn och nyckel för lagrings konto](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Under ditt anslutna Azure Storage-konto högerklickar du på **BLOB-behållare**, väljer **skapa BLOB-behållare**och namnger den nya behållaren.

      ![Skapa en blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Välj den nya behållaren och ladda upp det anpassade installations skriptet och dess tillhör ande filer. Se till att ladda upp `main.cmd` på den högsta nivån i din behållare, inte i någon mapp. Kontrol lera också att din behållare bara innehåller de nödvändiga anpassade installationsfilerna, så att du kan ladda ned dem till din Azure-SSIS IR senare inte tar längre tid. Den maximala tids perioden för anpassad installation är för närvarande 45 minuter innan tids gränsen uppnåddes och detta omfattar tiden att ladda ned alla filer från behållaren och installera dem på Azure-SSIS IR. Om en längre period krävs kan du generera ett support ärende.

      ![Ladda upp filer till BLOB-behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Högerklicka på behållaren och välj **Hämta signatur för delad åtkomst**.

      ![Hämta signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Skapa SAS-URI: n för din behållare med tillräckligt lång förfallo tid och med behörigheterna Läs + skriva + lista. Du behöver SAS-URI: n för att ladda ned och köra det anpassade installations skriptet och dess associerade filer när en nod i Azure-SSIS IR återskapas/startas om. Du måste ha Skriv behörighet för att överföra konfigurations körnings loggar.

      > [!IMPORTANT]
      > Se till att SAS-URI: n inte upphör att gälla och att de anpassade installations resurserna alltid är tillgängliga under hela livs cykeln för din Azure-SSIS IR, från att skapas till borttagning, särskilt om du regelbundet stoppar och startar Azure-SSIS IR under den här perioden.

      ![Generera signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Kopiera och spara SAS-URI: n för din behållare.

      ![Kopiera och spara signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. När du etablerar eller konfigurerar om Azure-SSIS IR med Data Factory användar gränssnitt kan du lägga till/ta bort anpassade inställningar genom att markera kryss rutan **Anpassa din Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** i avsnittet **Avancerade inställningar** på integration runtime-installationsprogrammet. 

   Om du vill lägga till anpassade standard inställningar anger du SAS-URI: n för din behållare i fältet för den **anpassade SAS-URI: n för installations behållaren** . 
   
   Om du vill lägga till anpassade installations alternativ väljer du **ny** för att öppna panelen **Lägg till anpassad installation för Express** och väljer sedan någon typ under List menyn **snabb anpassad Installations typ** :

   1. Om du väljer **kommando typen kör cmdkey** kan du spara autentiseringsuppgifter för dina fil resurser/Azure Files på Azure-SSIS IR genom att ange din mål dator/domän namn, konto namn/användar namn och konto nyckel/lösen ord i fälten **/Add**, **/User**och **/pass** . Detta påminner om att köra Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) -kommando på den lokala datorn.
   
   1. Om du väljer **variabel typen Lägg till miljö** kan du lägga till Windows-miljövariabler som ska användas i dina paket som körs på Azure-SSIS IR genom att ange ditt miljö variabel namn och värde i fälten **variabel namn** och **variabel värde** . Detta liknar att köra Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) -kommando på den lokala datorn.

   1. Om du väljer alternativet **Installera licensierad komponent** kan du sedan välja integrerade komponenter från våra ISV-partners under List rutan **komponent namn** :

      1. Om du väljer **uppgifts fabriks komponenten SentryOne** kan du installera komponenterna i [aktivitets fabriken](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) från SentryOne på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i fältet **licens nyckel** . Den aktuella integrerade versionen är **2019.4.3**.

      1. Om du väljer **oh22's-Hedda. I/o** -komponenten kan du installera [Hedda. IO](https://hedda.io/ssis-component/) -datakvalitet/rengörings komponent från oh22 på din Azure-SSIS IR efter köpet av tjänsten. Den aktuella integrerade versionen är **1.0.13**.

      1. Om du väljer komponenten **oh22's SQLPhonetics.net** kan du installera [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) Data Quality/Matching-komponenten från oh22 på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i fältet **licens nyckel** . Den aktuella integrerade versionen är **1.0.43**.
   
   De anpassade installations program som har lagts till visas i avsnittet **Avancerade inställningar** . Du kan ta bort dem genom att markera kryss rutorna och sedan **ta bort**.

   ![Avancerade inställningar med anpassade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. När du etablerar eller konfigurerar om din Azure-SSIS IR med PowerShell kan du lägga till/ta bort anpassade inställningar genom att köra `Set-AzDataFactoryV2IntegrationRuntime`-cmdleten innan du påbörjar din Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
   
   När den anpassade standard installationen slutförs och din Azure-SSIS IR startas, kan du hitta standardutdata för `main.cmd` och andra körnings loggar i `main.cmd.log`-mappen i lagrings behållaren.

1. Om du vill se några exempel på vanliga anpassade installations program ansluter du till vår offentliga för hands versions behållare med Azure Storage Explorer.

   1. Under **(lokal och ansluten)** högerklickar du på **lagrings konton**, väljer **Anslut till Azure Storage**, väljer **Använd en anslutnings sträng eller en signatur-URI för delad åtkomst**och välj sedan **Nästa**.

      ![Ansluta till Azure Storage med signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Välj **Använd en SAS-URI** och ange följande SAS-URI för den offentliga för hands versions behållaren. Välj **Nästa**och välj sedan **Anslut**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Ange signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Välj den anslutna offentliga för hands versions behållaren och dubbelklicka på mappen `CustomSetupScript`. I den här mappen finns följande objekt:

      1. En `Sample` mapp som innehåller en anpassad installation för att installera en grundläggande aktivitet på varje nod i Azure-SSIS IR. Aktiviteten gör ingenting men väntar i ett par sekunder. Mappen innehåller också en `gacutil` mapp, hela innehållet i vilken (`gacutil.exe`, `gacutil.exe.config`och `1033\gacutlrc.dll`) kan kopieras som finns i din behållare.

      1. En `UserScenarios` mapp som innehåller flera anpassade installations exempel från verkliga användar scenarier.

      ![Innehåll i offentlig för hands versions behållare](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. Dubbelklicka på mappen `UserScenarios` för att hitta följande objekt:

      1. En `.NET FRAMEWORK 3.5` mapp som innehåller en anpassad installation för att installera en tidigare version av .NET Framework som kan krävas för anpassade komponenter på varje nod i din Azure-SSIS IR.

      1. En `BCP` mapp som innehåller en anpassad installation för att installera SQL Server kommando rads verktyg (`MsSqlCmdLnUtils.msi`), inklusive Mass kopierings programmet (`bcp`), på varje nod i Azure-SSIS IR.

      1. En `EXCEL` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera C# sammansättningar/bibliotek som du kan använda i skript uppgifter för att dynamiskt läsa/skriva Excel-filer på varje nod i Azure-SSIS IR. Börja [med att](https://www.nuget.org/packages/DocumentFormat.OpenXml/)Hämta `ExcelDataReader.dll` [härifrån och `DocumentFormat.OpenXml.dll` härifrån och](https://www.nuget.org/packages/ExcelDataReader/) överför dem sedan tillsammans med `main.cmd` till din behållare. Alternativt, om du bara vill använda standard anslutnings hanteraren för Excel eller källa/mål, är den nödvändiga åtkomsten redan förinstallerad på Azure-SSIS IR, så du behöver inte någon anpassad installation.
      
      1. En `MYSQL ODBC` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera MySQL ODBC-drivrutinerna på varje nod i din Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutningssträngen/källan/målet för att ansluta till MySQL-servern. Börja med att hämta de senaste 64-bitars-och 32-bitars versionerna av MySQL ODBC-drivrutin installations program, till exempel `mysql-connector-odbc-8.0.13-winx64.msi` och `mysql-connector-odbc-8.0.13-win32.msi`-från [MySQL](https://dev.mysql.com/downloads/connector/odbc/), och överför dem sedan tillsammans med `main.cmd` till din behållare.

      1. En `ORACLE ENTERPRISE` mapp som innehåller ett anpassat installations skript (`main.cmd`) och tyst installations konfigurations fil (`client.rsp`) för att installera Oracle-kopplingarna och OCI-drivrutinen på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda anslutnings hanteraren för Oracle/källa/mål för att ansluta till Oracle-servern. Först laddar du ned Microsoft Connectors v 5.0 för Oracle (`AttunitySSISOraAdaptersSetup.msi` och `AttunitySSISOraAdaptersSetup64.msi`) från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) och den senaste Oracle-klienten, till exempel `winx64_12102_client.zip`-från [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), och överför dem sedan tillsammans med `main.cmd` och `client.rsp` till din behållare. Om du använder TNS för att ansluta till Oracle måste du också hämta `tnsnames.ora`, redigera den och ladda upp den i din behållare, så att den kan kopieras till mappen Oracle-installation under installationen.

      1. En `ORACLE STANDARD ADO.NET` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera Oracle ODP.NET-drivrutinen på varje nod i din Azure-SSIS IR. Med den här installationen kan du använda anslutnings hanteraren för ADO.NET/källan/målet för att ansluta till Oracle-servern. Börja med att ladda ned den senaste Oracle ODP.NET-drivrutinen, till exempel `ODP.NET_Managed_ODAC122cR1.zip` från [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), och överför den sedan tillsammans med `main.cmd` till din behållare.
       
      1. En `ORACLE STANDARD ODBC` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera Oracle ODBC-drivrutinen och konfigurera DSN på varje nod i din Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren/källa/mål eller Power Query anslutnings hanterare/källa med ODBC-datakälla för att ansluta till Oracle-servern. Börja med att hämta den senaste Oracle Instant-klienten (Basic-paket eller Basic lite-paket) och ODBC-paket – till exempel paketen 64-bit från [här](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-paket: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, Basic lite-paket: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC-paket: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) eller 32-bitars [paket (basic](https://www.oracle.com/technetwork/topics/winsoft-085727.html) Package: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, Basic Lite-paket: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC-paket: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) och överför dem sedan tillsammans med `main.cmd` till din behållare.

      1. En `ORACLE STANDARD OLEDB` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera Oracle OLEDB-drivrutinen på varje nod i din Azure-SSIS IR. Med den här installationen kan du använda OLEDB anslutnings hanteraren/källa/mål för att ansluta till Oracle-servern. Hämta först den senaste Oracle OLEDB-drivrutinen, till exempel `ODAC122010Xcopy_x64.zip` från [Oracle](https://www.oracle.com/partners/campaign/index-090165.html), och ladda sedan upp den tillsammans med `main.cmd` i din behållare.

      1. En `POSTGRESQL ODBC` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera PostgreSQL ODBC-drivrutinerna på varje nod i din Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren/källa/mål för att ansluta till PostgreSQL-servern. Börja med att hämta de senaste 64-bitars-och 32-bitars versionerna av PostgreSQL ODBC-drivrutiner, till exempel `psqlodbc_x64.msi` och `psqlodbc_x86.msi`-från [postgresql](https://www.postgresql.org/ftp/odbc/versions/msi/), och överför dem sedan tillsammans med `main.cmd` till din behållare.

      1. En `SAP BW` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera SAP .NET Connector-sammansättningen (`librfc32.dll`) på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda SAP BW anslutnings hanteraren/källa/mål för att ansluta till SAP BW Server. Först laddar du upp 64-bitars-eller 32-bitars versionen av `librfc32.dll` från SAP-installationsmappen tillsammans med `main.cmd` till din behållare. Skriptet kopierar sedan SAP-sammansättningen till mappen `%windir%\SysWow64` eller `%windir%\System32` under installationen.

      1. En `STORAGE` mapp som innehåller en anpassad installation för att installera Azure PowerShell på varje nod i din Azure-SSIS IR. Med den här installationen kan du distribuera och köra SSIS-paket som kör [PowerShell-skript för att manipulera ditt Azure Storage-konto](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopiera `main.cmd`, ett exempel `AzurePowerShell.msi` (eller Använd den senaste versionen) och `storage.ps1` till din behållare. Använd PowerShell. dtsx som en mall för dina paket. Paket mal len kombinerar en [hämtnings uppgift för Azure-Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), som laddar ned `storage.ps1` som ett ändrings Bart PowerShell-skript och en process för att [köra processer](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) som kör skriptet på varje nod.

      1. En `TERADATA` mapp som innehåller ett anpassat installations skript (`main.cmd`), dess associerade fil (`install.cmd`) och installations paket (`.msi`). De här filerna installerar Teradata-kopplingarna, TPT-API: et och ODBC-drivrutinen på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda Teradata-anslutnings hanteraren/källa/mål för att ansluta till Teradata-servern. Börja med att hämta Teradata-verktyg och-verktyg 15. x zip-filen (till exempel `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) från [Teradata](http://partnerintelligence.teradata.com)och överför den sedan tillsammans med ovanstående `.cmd` och `.msi` filer till din behållare.

      1. En `ZULU OPENJDK` mapp som innehåller ett anpassat installations skript (`main.cmd`) och PowerShell-fil (`install_openjdk.ps1`) för att installera Zulu OpenJDK på varje nod i Azure-SSIS IR. Med den här installationen kan du använda Azure Data Lake Store/flexibla fil kontakter för att bearbeta ORC/Parquet-filer, se [här](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) för mer information. Börja med att ladda ned den senaste Zulu-OpenJDKen, till exempel `zulu8.33.0.1-jdk8.0.192-win_x64.zip` – [härifrån, och](https://www.azul.com/downloads/zulu/zulu-windows/)överför den sedan tillsammans med `main.cmd` och `install_openjdk.ps1` till din behållare.

      ![Mappar i mappen användar scenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Om du vill testa de här anpassade installations exemplen kopierar du och klistrar in innehållet från den valda mappen i din behållare.
   
      När du etablerar eller omkonfigurerar din Azure-SSIS IR med Data Factory användar gränssnitt, markerar du kryss rutan **Anpassa din Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** i avsnittet **Avancerade inställningar** och anger din behållares SAS-URI i fältet för den **anpassade SAS-URI: n för konfigurations behållare** .
   
      När du etablerar eller konfigurerar om Azure-SSIS IR med PowerShell kör du `Set-AzDataFactoryV2IntegrationRuntime`-cmdlet med SAS-URI: n för din behållare som värde för `SetupScriptContainerSasUri` parameter.

## <a name="next-steps"></a>Nästa steg

-   [Enterprise-versionen av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Utveckla betalda eller licensierade anpassade komponenter för integrerings körningen av Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
