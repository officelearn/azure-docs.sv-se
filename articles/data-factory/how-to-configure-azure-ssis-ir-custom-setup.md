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
ms.date: 02/14/2020
ms.openlocfilehash: 9c084564fec3faf59317fe9e05f3e850a38454d6
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251982"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Anpassa installations programmet för en Azure-SSIS Integration Runtime

Den anpassade installationen för ett Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) tillhandahåller ett gränssnitt för att lägga till dina egna steg under installationen eller omkonfigurationen av dina Azure-SSIS IR. 

Med hjälp av den anpassade installationen kan du ändra standard operativ konfigurationen eller miljön till, till exempel starta ytterligare Windows-tjänster eller spara autentiseringsuppgifter för fil resurser. Du kan också installera ytterligare komponenter, till exempel sammansättningar, driv rutiner eller tillägg, på varje nod i Azure-SSIS IR.

Du kan göra anpassade inställningar på Azure-SSIS IR på något av två sätt: 
* **Express anpassad installation utan skript**: kör några vanliga system konfigurationer och Windows-kommandon eller installera några populära eller rekommenderade ytterligare komponenter utan att använda några skript.
* **Standard anpassad installation med ett skript**: Förbered ett skript och dess associerade filer och ladda upp dem tillsammans till en BLOB-behållare i ditt Azure Storage-konto. Du anger sedan en signatur för delad åtkomst (SAS) Uniform Resource Identifier (URI) för din behållare när du konfigurerar eller konfigurerar om Azure-SSIS IR. Varje nod i Azure-SSIS IR laddar sedan ned skriptet och dess tillhör ande filer från din behållare och kör den anpassade installationen med förhöjd behörighet. När den anpassade installationen är slutförd laddar varje nod standardutdata för körning och andra loggar till din behållare.

Du kan installera både kostnads fria, olicensierade komponenter och avgiftsbelagda komponenter med anpassade inställningar för Express och standard. Om du är en oberoende program varu leverantör (ISV) kan du läsa [mer i utveckla betalda eller licensierade komponenter för en Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Eftersom noder i v2-serien av en Azure-SSIS IR inte är lämpliga för anpassad installation använder du i stället v3-serie-noder. Om du redan använder noder i v2-serien växlar du till v3-serie-noder så snart som möjligt.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller endast för anpassade standardinställningar:

- Om du vill använda *Gacutil. exe* i skriptet för att installera sammansättningar i GAC (Global Assembly Cache) måste du ange *Gacutil. exe* som en del av din anpassade installation. Eller så kan du använda kopian som finns i vår *offentliga för hands versions* behållare, som beskrivs senare i avsnittet "instruktioner".

- Om du vill referera till en undermapp i skriptet, stöder inte *msiexec. exe* `.\` notation för att referera till rotmappen. Använd ett kommando som `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` i stället för `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Administrativa resurser eller dolda nätverks resurser som skapas automatiskt av Windows stöds för närvarande inte på Azure-SSIS IR.

- IBM iSeries Access ODBC-drivrutinen stöds inte på Azure-SSIS IR. Du kan se installations fel under den anpassade installationen. Kontakta IBM-supporten om du behöver hjälp.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill anpassa din Azure-SSIS IR behöver du följande objekt:

- [En Azure-prenumeration](https://azure.microsoft.com/)

- [Etablera Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Ett Azure Storage-konto](https://azure.microsoft.com/services/storage/). Krävs inte för Express-anpassade installationer. För standard anpassade installations program laddar du upp och lagrar det anpassade installations skriptet och dess associerade filer i en BLOB-behållare. Den anpassade installations processen överför även körnings loggarna till samma BLOB-behållare.

## <a name="instructions"></a>Instruktioner

1. Om du vill konfigurera eller konfigurera om Azure-SSIS IR med PowerShell kan du hämta och installera [Azure PowerShell](/powershell/azure/install-az-ps). För snabba anpassade installationer går du vidare till steg 4.

1. Förbered det anpassade installations skriptet och dess associerade filer (till exempel. bat,. cmd,. exe,. dll,. msi eller. ps1-filer).

   * Du måste ha en skript fil med namnet *main. cmd*, vilket är start punkten för den anpassade installationen.  
   * För att säkerställa att skriptet kan köras i bakgrunden rekommenderar vi att du testar det på din lokala dator först.  
   * Om du vill att fler loggar som genereras av andra verktyg (t *. ex...................................................... msiexec. exe* *CUSTOM_SETUP_SCRIPT_LOG_DIR*`CUSTOM_SETUP_SCRIPT_LOG_DIR`)

1. Ladda ned, installera och öppna [Azure Storage Explorer](https://storageexplorer.com/). Gör så här:

   a. Under **(lokal och ansluten)** högerklickar du på **lagrings konton**och väljer sedan **Anslut till Azure Storage**.

      ![Anslut till Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

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

   g. Skapa SAS-URI: n för din behållare med tillräckligt lång förfallo tid och med behörigheten Läs/skriv/lista. Du behöver SAS-URI: n för att ladda ned och köra det anpassade installations skriptet och dess associerade filer när en nod i Azure-SSIS IR återställs eller startas om. Du måste ha Skriv behörighet för att överföra konfigurations körnings loggar.

      > [!IMPORTANT]
      > Se till att SAS-URI: n inte upphör att gälla och att de anpassade installations resurserna alltid är tillgängliga under hela livs cykeln för din Azure-SSIS IR, från att skapas till borttagning, särskilt om du regelbundet stoppar och startar Azure-SSIS IR under den här perioden.

      ![Generera signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopiera och spara SAS-URI: n för din behållare.

      ![Kopiera och spara signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. När du konfigurerar eller konfigurerar om Azure-SSIS IR med ett Data Factory-gränssnitt kan du lägga till eller ta bort anpassade inställningar genom att markera kryss rutan **Anpassa din Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** i avsnittet **Avancerade inställningar** i installations fönstret för **integration runtime** . 

   Om du vill lägga till anpassade standard inställningar anger du SAS-URI: n för din behållare i rutan **SAS URI för anpassad installations behållare** . 
   
   Om du vill lägga till anpassade installations alternativ väljer du **ny** för att öppna fönstret **Lägg till Express anpassad installation** och väljer sedan en typ i list rutan **snabb Installations typ** :

   * Om du väljer **Kör cmdkey-kommandoraden** kan du spara autentiseringsuppgifter för dina fil resurser eller Azure Files resurser på Azure-SSIS IR genom att ange ditt mål dator namn eller domän namn, konto namn eller användar namn och konto nyckel eller lösen ord i rutorna **/Add**, **/User**och **/pass** . Detta påminner om att köra Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) -kommandot på den lokala datorn.
   
   * Om du väljer **miljö variabel typen Lägg** till kan du lägga till Windows-miljövariabler som ska användas i dina paket som körs på Azure-SSIS IR genom att ange ditt miljö variabel namn och värde i rutorna **variabel namn** och **variabel värde** . Detta påminner om att köra Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) -kommandot på den lokala datorn.

   * Om du väljer **Installera licensierad komponent** typ kan du sedan välja en integrerad komponent från våra ISV-partner i list rutan **komponent namn** :

     * Om du väljer **uppgifts fabriks komponenten SentryOne** kan du installera komponenterna i [aktivitets fabriken](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) från SentryOne på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **2019.4.3**.

     * Om du väljer **oh22's-Hedda. I/o** -komponenten kan du installera [Hedda. IO](https://hedda.io/ssis-component/) -datakvalitet/rengörings komponent från oh22 på din Azure-SSIS IR efter köpet av tjänsten. Den aktuella integrerade versionen är **1.0.13**.

     * Om du väljer komponenten **oh22's SQLPhonetics.net** kan du installera [SQLPhonetics.net](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) Data Quality/Matching-komponenten från oh22 på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **1.0.43**.

     * Om du väljer komponenten **SSIS Integration Toolkit för KingswaySoft** kan du installera [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) Suite för CRM/ERP/Marketing/Collaboration-appar, till exempel Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce marknadsförings moln osv. från KingswaySoft på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **2019,2**.

     * Om du väljer komponenten **SSIS produktivitets paket för KingswaySoft** kan du installera [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) Suite-komponenter från KingswaySoft på din Azure-SSIS IR genom att ange den produkt licens nyckel som du har köpt från dem i rutan **licens nyckel** . Den aktuella integrerade versionen är **10,0**.

   De anpassade installations program som har lagts till visas i avsnittet **Avancerade inställningar** . Om du vill ta bort dem markerar du kryss rutorna och väljer sedan **ta bort**.

   ![Avancerade inställningar med anpassade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. När du ställer in eller konfigurerar om Azure-SSIS IR med PowerShell kan du lägga till eller ta bort de anpassade inställningarna genom att köra `Set-AzDataFactoryV2IntegrationRuntime`-cmdleten innan du påbörjar din Azure-SSIS IR.
   
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
   
   När den anpassade standard installationen slutförs och din Azure-SSIS IR startas, kan du hitta standardutdata för *main. cmd* och andra körnings loggar i mappen *main. cmd. log* i din lagrings behållare.

1. Om du vill visa några exempel på vanliga anpassade installations program ansluter du till vår offentliga för hands versions behållare med hjälp av Azure Storage Explorer.

   a. Under **(lokal och ansluten)** högerklickar du på **lagrings konton**, väljer **Anslut till Azure Storage**, väljer **Använd en anslutnings sträng eller en signatur-URI för delad åtkomst**och välj sedan **Nästa**.

      ![Ansluta till Azure Storage med signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Välj **Använd en SAS-URI** och ange följande SAS-URI i rutan **URI** :

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Ange signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Välj **Nästa**och välj sedan **Anslut**.

   d. I den vänstra rutan väljer du den anslutna **publicpreview** -behållaren och dubbelklickar sedan på mappen *CustomSetupScript* . I den här mappen finns följande objekt:

      * En *exempel* mapp som innehåller en anpassad installation för att installera en grundläggande aktivitet på varje nod i Azure-SSIS IR. Aktiviteten gör ingenting men väntar i ett par sekunder. Mappen innehåller också en *Gacutil* -mapp vars hela innehåll (*Gacutil. exe*, *Gacutil. exe. config*och *1033 \ gacutlrc. dll*) kan kopieras som den är till din behållare.

      * En *UserScenarios* -mapp som innehåller flera anpassade installations exempel från verkliga användar scenarier.

        ![Innehåll i offentlig för hands versions behållare](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbelklicka på mappen *UserScenarios* för att hitta följande objekt:

      * En *.net FRAMEWORK 3,5* -mapp som innehåller en anpassad installation för att installera en tidigare version av .NET Framework som kan krävas för anpassade komponenter på varje nod i Azure-SSIS IR.

      * En *BCP* -mapp som innehåller en anpassad installation för att installera SQL Server kommando rads verktyg (*MsSqlCmdLnUtils. msi*), inklusive Mass kopierings programmet (*BCP*), på varje nod i Azure-SSIS IR.

      * En *Excel* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att C# installera sammansättningar och bibliotek som du kan använda i skript uppgifter för att dynamiskt läsa och skriva Excel-filer på varje nod i Azure-SSIS IR. 
      
        Först laddar du ned [*ExcelDataReader. dll*](https://www.nuget.org/packages/ExcelDataReader/) och [*DocumentFormat. OpenXml. dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)och laddar sedan upp dem tillsammans med *main. cmd* till din behållare. Alternativt, om du bara vill använda standard anslutnings hanteraren för Excel, Excel-källan och Excel-målet, är den nödvändiga åtkomsten för Access redan förinstallerad på din Azure-SSIS IR, så du behöver inte någon anpassad installation.
      
      * En *MySQL ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera MySQL ODBC-drivrutinerna på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren, källan och målet för att ansluta till MySQL-servern. 
     
        Börja [med att hämta de senaste 64-bitars-och 32-bitars versionerna av MySQL ODBC-drivrutinen installations](https://dev.mysql.com/downloads/connector/odbc/) program (till *exempel MySQL-Connector-ODBC-8.0.13-Winx64. msi* och *MySQL-Connector-ODBC-8.0.13-Win32. msi*) och ladda upp dem tillsammans med *main. cmd* till din behållare.

      * En *Oracle Enterprise* -mapp som innehåller ett anpassat installations skript (*main. cmd*) och tyst installations konfigurations fil (*client. rsp*) för att installera Oracle-kopplingarna och OCI-drivrutinen på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda anslutnings hanteraren för Oracle, källa och mål för att ansluta till Oracle-servern. 
      
        Börja med att ladda ned Microsoft Connectors v 5.0 för Oracle (*AttunitySSISOraAdaptersSetup. msi* och *AttunitySSISOraAdaptersSetup64. msi*) från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) och den senaste Oracle-klienten (till exempel *winx64_12102_client. zip*) från [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)och överför dem sedan tillsammans med *main. cmd* och *client. rsp* till din behållare. Om du använder TNS för att ansluta till Oracle måste du också hämta *Tnsnames. ora*, redigera den och ladda upp den till din behållare, så att den kan kopieras till mappen Oracle-installation under installationen.

      * En *Oracle STANDARD ADO.net* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle ODP.net-drivrutinen på varje nod i Azure-SSIS IR. Med den här installationen kan du använda anslutnings hanteraren för ADO.NET, källa och mål för att ansluta till Oracle-servern. 
      
        Hämta först [den senaste Oracle ODP.net-drivrutinen](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (till exempel *ODP. NET_Managed_ODAC122cR1. zip*) och ladda upp den tillsammans med *main. cmd* i din behållare.
       
      * En *Oracle standard-ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle ODBC-drivrutinen och konfigurera data källans namn (DSN) på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren, källa och mål eller Power Query anslutnings hanteraren och källa med ODBC-datakälla för att ansluta till Oracle-servern. 
      
        Börja med att hämta den senaste Oracle Instant-klienten (Basic-paket eller Basic lite-paket) och ODBC-paket och ladda sedan upp dem tillsammans med *main. cmd* till din behållare:
        * [Hämta 64-bitars paket](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-paket: *instantclient-Basic-Windows. x64-18.3.0.0.0 dbru. zip*; Basic lite-paket: *instantclient-basiclite-Windows. x64-18.3.0.0.0 dbru. zip*; ODBC-paket: *instantclient-ODBC-Windows. x64-18.3.0.0.0 dbru. zip*) 
        * [Hämta 32-bitars paket](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-paket: *instantclient-Basic-NT-18.3.0.0.0 dbru. zip*; Basic lite-paket: *instantclient-basiclite-NT-18.3.0.0.0 dbru. zip*; ODBC-paket: *instantclient-ODBC-NT-18.3.0.0.0 dbru. zip*)

      * En *Oracle standard OLEDB* -mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera Oracle OLEDB-drivrutinen på varje nod i Azure-SSIS IR. Med den här installationen kan du använda OLEDB anslutnings hanteraren, källa och mål för att ansluta till Oracle-servern. 
     
        Hämta först [den senaste Oracle OLEDB-drivrutinen](https://www.oracle.com/partners/campaign/index-090165.html) (till exempel *ODAC122010Xcopy_x64. zip*) och ladda sedan upp den tillsammans med *main. cmd* i din behållare.

      * En *POSTGRESQL ODBC-* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera POSTGRESQL ODBC-drivrutinerna på varje nod i Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren, källan och målet för att ansluta till PostgreSQL-servern. 
     
        Börja [med att hämta de senaste 64-bitars-och 32-bitars versionerna av POSTGRESQL ODBC-drivrutin installations](https://www.postgresql.org/ftp/odbc/versions/msi/) program (till exempel *psqlodbc_x64. msi* och *psqlodbc_x86. msi*) och överför dem sedan tillsammans med *main. cmd* till din behållare.

      * En *SAP BW* mapp som innehåller ett anpassat installations skript (*main. cmd*) för att installera SAP .net Connector-sammansättningen (*librfc32. dll*) på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda anslutnings hanteraren för SAP Business Warehouse (BW), källan och målet för att ansluta till den SAP BW servern. 
      
        Först överför du 64-bitars-eller 32-bitars versionen av *librfc32. dll* från SAP-installationsmappen tillsammans med *main. cmd* till din behållare. Skriptet kopierar sedan SAP-sammansättningen till mappen *%windir%\syswow64* eller mappen *%windir%\System32* under installationen.

      * En *lagringsmapp* som innehåller en anpassad installation för att installera Azure PowerShell på varje nod i Azure-SSIS IR. Med den här installationen kan du distribuera och köra SSIS-paket som kör [PowerShell-skript för att manipulera ditt Azure Storage-konto](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Kopiera *main. cmd*, ett exempel på *AzurePowerShell. msi* (eller Använd den senaste versionen) och *Storage. ps1* till din behållare. Använd *PowerShell. dtsx* som en mall för dina paket. Paket mal len kombinerar en [hämtnings uppgift för Azure-Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), som laddar ned *Storage. ps1* som ett ändrings Bart PowerShell-skript och en process för att [köra processer](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), som kör skriptet på varje nod.

      * En *TERADATA* -mapp som innehåller ett anpassat installations skript (*main. cmd*), dess associerade fil (*install. cmd*) och installations paket ( *. msi*). De här filerna installerar Teradata-kopplingarna, Teradata Parallel transporter (TPT) API och ODBC-drivrutinen på varje nod i Azure-SSIS IR Enterprise Edition. Med den här installationen kan du använda Teradata anslutnings hanteraren, källa och mål för att ansluta till Teradata-servern. 
      
        Först [laddar du ned Teradata-verktyg och-verktyg 15. x zip-filen](http://partnerintelligence.teradata.com) (till exempel *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00. zip*) och laddar sedan upp den tillsammans med de tidigare nämnda *. cmd* -och *MSI* -filerna till din behållare.

      * En *ZULU OPENJDK* -mapp som innehåller ett anpassat installations skript (*main. cmd*) och PowerShell-fil (*install_openjdk. ps1*) för att installera ZULU OPENJDK på varje nod i Azure-SSIS IR. Med den här installationen kan du använda Azure Data Lake Store och flexibla fil anslutningar för att bearbeta ORC-och Parquet-filer. Mer information finns i [Azure Feature Pack för integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Börja med [att hämta de senaste Zulu-openjdk](https://www.azul.com/downloads/zulu/zulu-windows/) (till *exempel Zulu 8.33.0.1-jdk 8.0.192-win_x64. zip*) och ladda upp det tillsammans med *main. cmd* och *install_openjdk. ps1* till din behållare.

        ![Mappar i mappen användar scenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Om du vill testa de här anpassade installations exemplen kopierar du innehållet från den valda mappen till din behållare.
   
      När du ställer in eller konfigurerar om din Azure-SSIS IR med hjälp av Data Factory användar gränssnitt, markerar du kryss rutan **Anpassa din Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** i avsnittet **Avancerade inställningar** och anger sedan SAS-URI för din behållare i rutan **SAS URI för anpassad installations behållare** .
   
      När du ställer in eller konfigurerar om Azure-SSIS IR med PowerShell kör du `Set-AzDataFactoryV2IntegrationRuntime`-cmdleten med SAS-URI: n för din behållare som värde för `SetupScriptContainerSasUri` parameter.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Enterprise-versionen av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Utveckla betalda eller licensierade anpassade komponenter för Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
