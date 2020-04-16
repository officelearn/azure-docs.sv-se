---
title: Anpassa inställningarna för en Azure-SSIS Integration Runtime
description: I den hÃ¤r artikeln beskrivs hur du anã¤nder det anpassade installationsgränssnittet fÃ¶r en Azure-SSIS Integration Runtime fÃ¶r att installera ytterligare komponenter eller ändra inställningar
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
ms.date: 04/15/2020
ms.openlocfilehash: 3aabf68b6763e5815a03021cb02683d509c26190
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415056"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Anpassa inställningarna för en Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den anpassade installationen för en Azure-SQL Server Integration Services Integration Runtime (Azure-SSIS IR) ger ett gränssnitt för att lägga till egna steg under installationen eller omkonfigureringen av din Azure-SSIS IR. 

Genom att använda den anpassade installationen kan du ändra standardkonfigurationen eller standardmiljön för att till exempel starta ytterligare Windows-tjänster, spara åtkomstautentiseringsuppgifter för filresurser eller använda ett starkt kryptografi/säkrare nätverksprotokoll (TLS 1.2). Du kan också installera ytterligare komponenter, till exempel sammansättningar, drivrutiner eller tillägg, på varje nod i din Azure-SSIS IR.

Du kan göra anpassade inställningar på din Azure-SSIS IR på något av två sätt: 
* **Express anpassad installation utan skript:** Kör några vanliga systemkonfigurationer och Windows-kommandon eller installera några populära eller rekommenderade ytterligare komponenter utan att använda några skript.
* **Standardanpassad konfiguration med ett skript:** Förbered ett skript och dess associerade filer och ladda upp dem alla tillsammans till en blob-behållare i ditt Azure-lagringskonto. Du tillhandahåller sedan en SAS-signatur (Shared Access Signature) för din behållare när du konfigurerar eller konfigurerar om din Azure-SSIS IR. Varje nod i din Azure-SSIS IR hämtar sedan skriptet och dess associerade filer från din behållare och kör din anpassade konfiguration med förhöjda behörigheter. När den anpassade konfigurationen är klar överför varje nod standardutdata för körning och andra loggar till behållaren.

Du kan installera både kostnadsfria, olicensierade komponenter och betalda, licensierade komponenter med express- och standardanpassade inställningar. Om du är en oberoende programvaruleverantör (ISV) läser du [Utveckla betalda eller licensierade komponenter för en Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> För att dra nytta av framtida förbättringar rekommenderar vi att du använder v3 eller senare serie noder för din Azure-SSIS IR med anpassad konfiguration.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller endast för anpassade standardinställningar:

- Om du vill använda *gacutil.exe* i skriptet för att installera sammansättningar i den globala sammansättningscachen (GAC) måste du ange *gacutil.exe* som en del av din anpassade konfiguration. Du kan också använda kopian som finns i vår behållare *för offentlig förhandsversion,* som beskrivs senare i avsnittet "Instruktioner".

- Om du vill referera till en undermapp i skriptet stöder *msiexec.exe* inte notationen `.\` för att referera till rotmappen. Använd ett kommando, till exempel `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` i stället för `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Administrativa resurser eller dolda nätverksresurser som skapas automatiskt av Windows stöds för närvarande inte på Azure-SSIS IR.

- IBM iSeries Access ODBC-drivrutinen stöds inte på Azure-SSIS IR. Du kan se installationsfel under den anpassade installationen. Om du gör det kontaktar du IBM-supporten för att få hjälp.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill anpassa din Azure-SSIS IR behöver du följande:

- [En Azure-prenumeration](https://azure.microsoft.com/)

- [Etablera din Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Ett Azure-lagringskonto](https://azure.microsoft.com/services/storage/). Krävs inte för express anpassade inställningar. För anpassade standardinställningar laddar du upp och lagrar ditt anpassade installationsskript och tillhörande filer i en blob-behållare. Den anpassade installationsprocessen överför också sina körningsloggar till samma blob-behållare.

## <a name="instructions"></a>Instruktioner

1. Om du vill konfigurera eller konfigurera om din Azure-SSIS IR med PowerShell kan du hämta och installera [Azure PowerShell](/powershell/azure/install-az-ps). För anpassade expressinställningar går du till steg 4.

1. Förbered det anpassade inställningsskriptet och dess associerade filer (till exempel .bat-, .cmd-, .exe-, .dll-, MSI- eller PS1-filer).

   * Du måste ha en skriptfil med namnet *main.cmd*, som är startpunkten för den anpassade konfigurationen.  
   * För att säkerställa att skriptet kan köras tyst rekommenderar vi att du testar det på din lokala dator först.  
   * Om du vill att ytterligare loggar som genereras av andra verktyg (till exempel *msiexec.exe)* ska överföras till behållaren anger du den fördefinierade miljövariabeln, `CUSTOM_SETUP_SCRIPT_LOG_DIR`som loggmappen i skripten (till exempel *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Ladda ned, installera och öppna [Azure Storage Explorer](https://storageexplorer.com/). Gör så här:

   a. Högerklicka på **Lagringskonton**under **(Lokal och Bifogad)** och välj sedan **Anslut till Azure-lagring**.

      ![Ansluta till Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Välj **Använd ett namn och en lagringskontonyckel**och välj sedan **Nästa**.

      ![Använda lagringskontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Ange namn och nyckel för ditt Azure-lagringskonto, välj **Nästa**och välj sedan **Anslut**.

      ![Ange namn och nyckel för lagringskonto](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Högerklicka på **Blob**Containers under ditt anslutna Azure-lagringskonto, välj **Skapa blob-behållare**och namnge den nya behållaren.

      ![Skapa en blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Välj den nya behållaren och ladda upp det anpassade installationsskriptet och dess associerade filer. Se till att du laddar upp *main.cmd* på den översta nivån i behållaren, inte i någon mapp. Se också till att din behållare bara innehåller nödvändiga anpassade installationsfiler, så att det inte tar lång tid att hämta dem till din Azure-SSIS IR senare. Den maximala varaktigheten för en anpassad installation är för närvarande inställd på 45 minuter innan den time out. Detta inkluderar tid att hämta alla filer från din behållare och installera dem på Azure-SSIS IR. Om installationen kräver mer tid höjer du en supportbiljett.

      ![Ladda upp filer till blob-behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Högerklicka på behållaren och välj sedan **Hämta signatur för delad åtkomst**.

      ![Hämta signature för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Skapa SAS URI för din behållare med tillräckligt lång utgångstid och med läs-/skriv-/listbehörighet. Du behöver SAS URI för att hämta och köra ditt anpassade installationsskript och tillhörande filer när någon nod i din Azure-SSIS IR återskapas eller startas om. Du behöver skrivbehörighet för att ladda upp installationskörningsloggar.

      > [!IMPORTANT]
      > Se till att SAS URI inte upphör att gälla och att anpassade installationsresurser alltid är tillgängliga under hela livscykeln för din Azure-SSIS IR, från skapande till borttagning, särskilt om du regelbundet stoppar och startar din Azure-SSIS IR under den här perioden.

      ![Generera signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopiera och spara SAS URI-urin för din behållare.

      ![Kopiera och spara signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. När du konfigurerar eller konfigurerar om din Azure-SSIS IR med ett datafabriksgränssnitt kan du lägga till eller ta bort anpassade inställningar genom att välja kryssrutan **Anpassa din Azure-SSIS-integreringskörning med ytterligare systemkonfigurationer/komponentinstallationer** i avsnittet **Avancerade inställningar** i **inställningsfönstret för integrering.** 

   Om du vill lägga till anpassade standardinställningar anger du SAS URI-för din behållare i **SAS URI-rutan Anpassad inställningsbehållare.** 
   
   Om du vill lägga till anpassade standardinställningar för express väljer du **Ny** för att öppna fönstret Lägg till **expressanpassade inställningar** och väljer sedan en typ i listrutan **Anpassad inställningstyp** för Express:

   * Om du väljer **kommandotypen Kör cmdkey** kan du spara åtkomstuppgifter för dina filresurser eller Azure Files-resurser på Azure-SSIS IR genom att ange ditt målnamn eller domännamn, kontonamn eller användarnamn och kontonyckel eller lösenord i rutorna **/Add**, **/User**och **/Pass.** Detta liknar att köra kommandot Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) på den lokala datorn.
   
   * Om du väljer **variabeltypen Lägg till miljö** kan du lägga till Windows-miljövariabler som ska användas i dina paket som körs på Azure-SSIS IR genom att ange miljövariabelns namn och värde i **rutorna Variabelnamn** och **Variabelvärde.** Detta liknar att köra kommandot Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) på den lokala datorn.

   * Om du väljer **komponenttypen Installera licensierad** kan du välja en integrerad komponent från våra ISV-partner i listrutan **Komponentnamn:**

     * Om du väljer **Komponenten SentryOnes Aktivitetsfabrik** kan du installera [Task Factory-paketet](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) med komponenter från SentryOne på din Azure-SSIS IR genom att ange produktlicensnyckeln som du köpte från dem i **rutan Licensnyckel.** Den nuvarande integrerade versionen är **2019.4.3**.

     * Om du väljer **oh22's HEDDA. IO-komponenten** kan du installera [HEDDA. ](https://hedda.io/ssis-component/)IO-datakvalitet/rensningskomponent från oh22 på din Azure-SSIS IR efter att ha köpt deras tjänst. Den nuvarande integrerade versionen är **1.0.13**.

     * Om du väljer **oh22:s SQLPhonetics.NET-komponent** kan du installera [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) datakvalitet/matchningskomponent från oh22 på din Azure-SSIS IR genom att ange produktlicensnyckeln som du köpte från dem i rutan **Licensnyckel.** Den nuvarande integrerade versionen är **1.0.43**.

     * Om du väljer **KingswaySofts SSIS Integration Toolkit-komponent** kan du installera [SSIS Integration Toolkit-paketet](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) med kopplingar för CRM/ERP/marknadsföring/samarbetsappar, till exempel Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud, etc. från KingswaySoft på din Azure-SSIS IR genom att ange produktlicensnyckeln som du köpte från dem i **nyckelrutan Licensnyckel.** Den nuvarande integrerade versionen är **2019.2**.

     * Om du väljer **KingswaySofts SSIS Productivity Pack-komponent** kan du installera [SSIS Productivity Pack-paketet](https://www.kingswaysoft.com/products/ssis-productivity-pack) med komponenter från KingswaySoft på din Azure-SSIS IR genom att ange produktlicensnyckeln som du köpte från dem i **rutan Licensnyckel.** Den nuvarande integrerade versionen är **10,0**.

     * Om du väljer **Theobald Softwares Xtract IS-komponent** kan du installera [Xtract IS-paketet](https://theobald-software.com/en/xtract-is/) med kopplingar för SAP-system (ERP, S/4HANA, BW) från Theobald Software på din Azure-SSIS IR genom att dra & släppa/ladda upp produktlicensfilen som du köpte från dem till **licensfilrutan.** Den nuvarande integrerade versionen är **6.1.1.3**.

   De tillagda anpassade standardinställningarna visas i avsnittet **Avancerade inställningar.** Om du vill ta bort dem markerar du deras kryssrutor och väljer sedan **Ta bort**.

   ![Avancerade inställningar med anpassade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. När du konfigurerar eller konfigurerar om din Azure-SSIS IR med PowerShell kan du `Set-AzDataFactoryV2IntegrationRuntime` lägga till eller ta bort de anpassade installationerna genom att köra cmdleten innan du startar din Azure-SSIS IR.
   
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
   
   När din anpassade standardkonfiguration är klar och din Azure-SSIS IR startar kan du hitta standardutdata för *main.cmd* och andra körningsloggar i *huvud.cmd.log-mappen* i lagringsbehållaren.

1. Om du vill visa några exempel på anpassade standardinställningar ansluter du till vår offentliga förhandsversionsbehållare med hjälp av Azure Storage Explorer.

   a. Under **(Lokal och Bifogad)** högerklickar du på **Lagringskonton,** väljer **Anslut till Azure-lagring,** väljer Använd en **anslutningssträng eller en signatur-URI**för delad åtkomst och välj sedan **Nästa**.

      ![Ansluta till Azure-lagring med signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Välj **Använd en SAS-URI** och ange sedan följande SAS URI i **rutan URI:**

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Ange signature för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Välj **Nästa**och välj sedan **Anslut**.

   d. Markera den anslutna **behållaren för offentlig förvy** i den vänstra rutan och dubbelklicka sedan på *customsetupscript-mappen.* I den här mappen finns följande objekt:

      * En *exempelmapp* som innehåller en anpassad installation för att installera en grundläggande uppgift på varje nod i din Azure-SSIS IR. Uppgiften gör ingenting annat än sömn i några sekunder. Mappen innehåller också en *gacutil* mapp, vars hela innehåll *(gacutil.exe*, *gacutil.exe.config*, och *1033\gacutlrc.dll)* kan kopieras som är till din behållare.

      * En *UserScenarios-mapp,* som innehåller flera anpassade installationsexempel från verkliga användarscenarier.

        ![Innehållet i den offentliga förhandsgranskningsbehållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbelklicka på mappen *UserScenarios* för att hitta följande objekt:

      * En *.NET FRAMEWORK 3.5-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera en tidigare version av .NET Framework som kan krävas för anpassade komponenter på varje nod i din Azure-SSIS IR.

      * En *BCP-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera KOMMANDORADSVERKTYG för SQL Server (*MsSqlCmdLnUtils.msi*), inklusive masskopieringsprogrammet (*bcp*), på varje nod i din Azure-SSIS IR.

      * En *EXCEL-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera C#-sammansättningar och bibliotek som du kan använda i skriptuppgifter för att dynamiskt läsa och skriva Excel-filer på varje nod i din Azure-SSIS IR. 
      
        Hämta först [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) och [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)och ladda sedan upp dem alla tillsammans med *main.cmd* till din behållare. Om du bara vill använda standardmålet För Excel-anslutningshanteraren, Excel-källa och Excel, är den nödvändiga åtkomsten återinstallerad redan förinstallerad på din Azure-SSIS IR, så du behöver ingen anpassad konfiguration.
      
      * En *MYSQL ODBC-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera MySQL ODBC-drivrutinerna på varje nod i din Azure-SSIS IR. Med den här inställningen kan du använda ODBC Connection Manager, källa och mål för att ansluta till MySQL-servern. 
     
        Först [ladda ner de senaste 64-bitars och 32-bitars versionerna av MySQL ODBC-drivrutinsinstallationsapparna](https://dev.mysql.com/downloads/connector/odbc/) (till exempel *mysql-connector-odbc-8.0.13-winx64.msi* och *mysql-connector-odbc-8.0.13-win32.msi*) och ladda sedan upp dem alla tillsammans med *main.cmd* till din behållare.

      * En *ORACLE ENTERPRISE-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* och tyst installationskonfigurationsfil *(client.rsp)* för att installera Oracle-kopplingarna och OCI-drivrutinen på varje nod i din Azure-SSIS IR Enterprise Edition. Med den här inställningen kan du använda Oracles anslutningshanterare, källa och mål för att ansluta till Oracle-servern. 
      
        Först ladda ner Microsoft Connectors v5.0 för Oracle *(AttunitySSISOraAdaptersSetup.msi* och *AttunitySSISOraAdaptersSetup64.msi*) från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) och den senaste Oracle-klienten (till exempel *winx64_12102_client.zip*) från [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), och sedan ladda upp dem alla tillsammans med *main.cmd* och *client.rsp* till din behållare. Om du använder TNS för att ansluta till Oracle måste du också hämta *tnsnames.ora*, redigera den och ladda upp den till din behållare, så att den kan kopieras till Oracle-installationsmappen under installationen.

      * En *ORACLE STANDARD-ADO.NET* mapp, som innehåller ett anpassat installationsskript *(main.cmd)* för att installera Oracle-ODP.NET-drivrutinen på varje nod i din Azure-SSIS IR. Med den här konfigurationen kan du använda ADO.NET Anslutningshanteraren, källan och målet för att ansluta till Oracle-servern. 
      
        Först [ladda ner den senaste Oracle ODP.NET-drivrutinen](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (till exempel *ODP.NET_Managed_ODAC122cR1.zip)* och ladda sedan upp den tillsammans med *main.cmd* till din behållare.
       
      * En *ORACLE STANDARD ODBC-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera Oracle ODBC-drivrutinen och konfigurera datakällnamnet (DSN) på varje nod i din Azure-SSIS IR. Med den här inställningen kan du använda ODBC Connection Manager, källa och mål eller Power Query Connection Manager och källa med ODBC-datakälltypen för att ansluta till Oracle-servern. 
      
        Först ladda ner den senaste Oracle Instant Client (Basic Package eller Basic Lite Package) och ODBC Package, och sedan ladda upp dem alla tillsammans med *main.cmd* till din behållare:
        * [Ladda ner 64-bitars paket](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic Package: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Grundläggande Lite Paket: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC-paket: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Ladda ner 32-bitars paket](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic Package: *instantclient-basic-nt-18.3.0.0.0dbru.zip;* Grundläggande Lite Paket: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC Paket: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * En *ORACLE STANDARD OLEDB-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera Oracle OLEDB-drivrutinen på varje nod i din Azure-SSIS IR. Med den här inställningen kan du använda OLEDB-anslutningshanteraren, källan och målet för att ansluta till Oracle-servern. 
     
        Först [ladda ner den senaste Oracle OLEDB-drivrutinen](https://www.oracle.com/partners/campaign/index-090165.html) (till exempel *ODAC122010Xcopy_x64.zip)* och ladda sedan upp den tillsammans med *main.cmd* till din behållare.

      * En *POSTGRESQL ODBC-mapp,* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera PostgreSQL ODBC-drivrutinerna på varje nod i din Azure-SSIS IR. Med den här inställningen kan du använda ODBC Connection Manager, källa och mål för att ansluta till PostgreSQL-servern. 
     
        Först [ladda ner de senaste 64-bitars och 32-bitars versionerna av PostgreSQL ODBC-drivrutinsinstallationsprogram](https://www.postgresql.org/ftp/odbc/versions/msi/) (till exempel *psqlodbc_x64.msi* och *psqlodbc_x86.msi*) och ladda sedan upp dem alla tillsammans med *main.cmd* till din behållare.

      * En *SAP BW-mapp* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera SAP .NET-anslutningsuppsättningen *(librfc32.dll)* på varje nod i din Azure-SSIS IR Enterprise Edition. Med den här inställningen kan du använda SAP Business Warehouse (BW) Connection Manager, källa och mål för att ansluta till SAP BW-servern. 
      
        Ladda först upp 64-bitarsversionen eller 32-bitarsversionen av *librfc32.dll* från SAP-installationsmappen tillsammans med *main.cmd* till din behållare. Skriptet kopierar sedan SAP-sammansättningen till mappen *%windir%\SysWow64* eller *%windir%\System32* under installationen.

      * En *STORAGE-mapp* som innehåller ett anpassat installationsskript *(main.cmd)* för att installera Azure PowerShell på varje nod i din Azure-SSIS IR. Med den här konfigurationen kan du distribuera och köra SSIS-paket som kör [PowerShell-skript för att manipulera ditt Azure-lagringskonto](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). 
      
        Kopiera *main.cmd*, ett exempel på *AzurePowerShell.msi* (eller använd den senaste versionen) och *storage.ps1* till din behållare. Använd *PowerShell.dtsx* som mall för dina paket. Paketmallen kombinerar en [Azure Blob-hämtningsuppgift](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), som hämtar *storage.ps1* som ett ändringsbart PowerShell-skript och en [körningsprocessuppgift](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)som kör skriptet på varje nod.

      * En *TERADATA-mapp,* som innehåller ett anpassat inställningsskript (*main.cmd*), tillhörande fil (*install.cmd*) och installationspaket (*.msi*). Dessa filer installerar Teradata-kopplingarna, TPT-API:et (TPT) och ODBC-drivrutinen på varje nod i Din Azure-SSIS IR Enterprise Edition. Med den här inställningen kan du använda Teradatas anslutningshanteraren, källan och målet för att ansluta till Teradata-servern. 
      
        Först [ladda ner Teradata Verktyg och verktyg 15.x zip-fil](http://partnerintelligence.teradata.com) (till exempel *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*), och sedan ladda upp den tillsammans med de tidigare nämnda *.cmd* och *MSI-filer* till din behållare.

      * En *TLS 1.2-mapp* som innehåller ett anpassat installationsskript *(main.cmd)* för att använda ett starkt kryptografi/säkrare nätverksprotokoll (TLS 1.2) och inaktivera äldre SSL/TLS-versioner på varje nod i Din Azure-SSIS IR.

      * En *ZULU OPENJDK-mapp* som innehåller ett anpassat installationsskript *(main.cmd*) och PowerShell-fil (*install_openjdk.ps1*) för att installera Zulu OpenJDK på varje nod i din Azure-SSIS IR. Med den här inställningen kan du använda Azure Data Lake Store och flexibla filkopplingar för att bearbeta ORC- och Parkettfiler. Mer information finns i [Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        Först [ladda ner den senaste Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (till exempel *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), och sedan ladda upp den tillsammans med *main.cmd* och *install_openjdk.ps1* till din behållare.

        ![Mappar i mappen användarscenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Om du vill prova de här anpassade installationsexemplen kopierar du innehållet från den markerade mappen till behållaren.
   
      När du konfigurerar eller konfigurerar om din Azure-SSIS IR med hjälp av datafabrikens användargränssnitt markerar du kryssrutan **Anpassa din Azure-SSIS-integreringskörningstid med ytterligare systemkonfigurationer/komponentinstallationer** i avsnittet **Avancerade inställningar** och anger sedan SAS URI för behållaren i **sas uri-behållaren Anpassad konfigurationsbehållare.**
   
      När du konfigurerar eller konfigurerar om din Azure-SSIS IR med PowerShell kör du cmdleten `Set-AzDataFactoryV2IntegrationRuntime` `SetupScriptContainerSasUri` med SAS URI för din behållare som värde för parametern.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Enterprise Edition av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Utveckla betalda eller licensierade anpassade komponenter för Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
