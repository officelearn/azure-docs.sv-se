---
title: Anpassa installations programmet för Azure-SSIS integration runtime | Microsoft Docs
description: Den här artikeln beskriver hur du använder det anpassade installations gränssnittet för Azure-SSIS integration runtime för att installera ytterligare komponenter eller ändra inställningar
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f7b09dcbd474debc08b79599e9e2dfaaca52285a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754692"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Anpassa installations programmet för Azure-SSIS integration runtime

Det anpassade installations gränssnittet för Azure-SSIS Integration Runtime tillhandahåller ett gränssnitt för att lägga till egna installations steg under etableringen eller omkonfigurationen av din Azure-SSIS IR. Med anpassad installation kan du ändra standard operativ konfigurationen eller miljön (till exempel för att starta ytterligare Windows-tjänster eller spara autentiseringsuppgifter för fil resurser) eller installera ytterligare komponenter (till exempel sammansättningar, driv rutiner eller tillägg) på varje nod i Azure-SSIS IR.

Du konfigurerar den anpassade konfigurationen genom att förbereda ett skript och dess associerade filer och ladda upp dem till en BLOB-behållare i ditt Azure Storage-konto. Du anger en signatur för delad åtkomst (SAS) Uniform Resource Identifier (URI) för din behållare när du etablerar eller konfigurerar om Azure-SSIS IR. Varje nod i Azure-SSIS IR laddar sedan ned skriptet och dess tillhör ande filer från din behållare och kör anpassad installation med utökade privilegier. När den anpassade installationen är slutförd laddar varje nod standardutdata för körning och andra loggar till din behållare.

Du kan installera både kostnads fria eller olicensierade komponenter, samt betalade eller licensierade komponenter. Om du är en ISV, se [hur du utvecklar betalda eller licensierade komponenter för Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Noder i v2-serien av Azure-SSIS IR lämpar sig inte för anpassad installation, så Använd v3-serie-noderna i stället.  Om du redan använder noderna i v2-serien växlar du till att använda v3-seriens noder så snart som möjligt.

## <a name="current-limitations"></a>Aktuella begränsningar

-   Om du vill använda `gacutil.exe` för att installera sammansättningar i GAC (Global Assembly Cache) måste du ange `gacutil.exe` som en del av din anpassade installation eller använda den kopia som finns i den offentliga för hands versionen av behållaren.

-   Om du vill referera till en undermapp i skriptet stöder `msiexec.exe` inte `.\` notation för att referera till rotmappen. Använd ett kommando som `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` i stället för `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Om du behöver ansluta till din Azure-SSIS IR med anpassad konfiguration till ett virtuellt nätverk stöds endast Azure Resource Manager virtuella nätverk. Det klassiska virtuella nätverket stöds inte.

-   Administrativ resurs stöds inte för närvarande på Azure-SSIS IR.

-   IBM iSeries Access ODBC-drivrutinen stöds inte på Azure-SSIS IR. Du kan se installations fel under den anpassade installationen. Kontakta IBM-supporten om du behöver hjälp.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att anpassa din Azure-SSIS IR behöver du följande saker:

-   [Azure-prenumeration](https://azure.microsoft.com/)

-   [En Azure SQL Database eller hanterad instans Server](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Etablera Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Ett Azure Storage konto](https://azure.microsoft.com/services/storage/). Vid anpassad installation laddar du upp och lagrar det anpassade installations skriptet och dess tillhör ande filer i en BLOB-behållare. Den anpassade installations processen överför även körnings loggarna till samma BLOB-behållare.

## <a name="instructions"></a>Instruktioner

1. Hämta och installera [Azure PowerShell](/powershell/azure/install-az-ps).

1. Förbered det anpassade installations skriptet och dess associerade filer (till exempel. bat,. cmd,. exe,. dll,. msi eller. ps1-filer).

   1.  Du måste ha en skript fil med namnet `main.cmd`, vilket är start punkten för den anpassade installationen.

   1.  Du måste kontrol lera att skriptet kan köras i bakgrunden, men vi rekommenderar att du testar skriptet på den lokala datorn först.

   1.  Om du vill att ytterligare loggar som genereras av andra verktyg (till exempel `msiexec.exe`) ska överföras till din behållare anger du den fördefinierade miljövariabeln `CUSTOM_SETUP_SCRIPT_LOG_DIR` som loggmappen i dina skript (till exempel `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

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

   1. När du etablerar eller omkonfigurerar din Azure-SSIS IR med Data Factory användar gränssnitt, innan du startar din Azure-SSIS IR, anger du din behållares SAS-URI i lämpligt fält i panelen **Avancerade inställningar** :

      ![Ange signaturen för delad åtkomst](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      När du etablerar eller konfigurerar om din Azure-SSIS IR med PowerShell måste du köra `Set-AzDataFactoryV2IntegrationRuntime`-cmdleten med SAS-URI: n för din behållare som värde för ny `SetupScriptContainerSasUri` parameter innan du börjar Azure-SSIS IR. Exempel:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. När den anpassade installationen har slutförts och din Azure-SSIS IR startas, kan du hitta standardutdata för `main.cmd` och andra körnings loggar i mappen `main.cmd.log` i lagrings behållaren.

1. Om du vill se andra anpassade installations exempel ansluter du till den offentliga förhands gransknings behållaren med Azure Storage Explorer.

   a.  Under **(lokal och ansluten)** högerklickar du på **lagrings konton**, väljer **Anslut till Azure Storage**, väljer **Använd en anslutnings sträng eller en signatur-URI för delad åtkomst**och välj sedan **Nästa**.

      ![Ansluta till Azure Storage med signaturen för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Välj **Använd en SAS-URI** och ange följande SAS-URI för den offentliga för hands versions behållaren. Välj **Nästa**och välj **Anslut**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Ange signaturen för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Välj den anslutna offentliga för hands versions behållaren och dubbelklicka på mappen `CustomSetupScript`. I den här mappen finns följande objekt:

      1. En `Sample` mapp som innehåller en anpassad installation för att installera en grundläggande aktivitet på varje nod i Azure-SSIS IR. Aktiviteten gör ingenting men väntar i ett par sekunder. Mappen innehåller också en `gacutil` mapp, hela innehållet i vilken (`gacutil.exe`, `gacutil.exe.config` och `1033\gacutlrc.dll`) kan kopieras som finns i din behållare. Dessutom innehåller `main.cmd` kommentarer för att bevara autentiseringsuppgifter för fil resurser.

      1. En `UserScenarios` mapp som innehåller flera anpassade inställningar för verkliga användar scenarier.

   ![Innehåll i offentlig för hands versions behållare](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Dubbelklicka på mappen `UserScenarios`. I den här mappen finns följande objekt:

      1. En `.NET FRAMEWORK 3.5` mapp som innehåller en anpassad installation för att installera en tidigare version av .NET Framework som kan krävas för anpassade komponenter på varje nod i din Azure-SSIS IR.

      1. En `BCP` mapp som innehåller en anpassad installation för att installera SQL Server kommando rads verktyg (`MsSqlCmdLnUtils.msi`), inklusive Mass kopierings programmet (`bcp`), på varje nod i Azure-SSIS IR.

      1. En `EXCEL` mapp som innehåller en anpassad installation för att installera sammansättningar med öppen källkod (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` och `ExcelDataReader.dll`) på varje nod i Azure-SSIS IR.

      1. En `ORACLE ENTERPRISE` mapp som innehåller ett anpassat installations skript (`main.cmd`) och tyst installations konfigurations fil (`client.rsp`) för att installera Oracle-kopplingarna och OCI-drivrutinen på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda anslutnings hanteraren för Oracle, källa och mål. Först laddar du ned Microsoft Connector v 5.0 för Oracle (`AttunitySSISOraAdaptersSetup.msi` och `AttunitySSISOraAdaptersSetup64.msi`) från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) och den senaste Oracle-klienten, till exempel `winx64_12102_client.zip`-från [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)och laddar sedan upp dem tillsammans med `main.cmd` och `client.rsp` i fönster. Om du använder TNS för att ansluta till Oracle måste du också hämta `tnsnames.ora`, redigera den och ladda upp den i din behållare, så att den kan kopieras till mappen Oracle-installation under installationen.

      1. En `ORACLE STANDARD ADO.NET` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera Oracle ODP.NET-drivrutinen på varje nod i din Azure-SSIS IR. Med den här installationen kan du använda anslutnings hanteraren för ADO.NET, källa och mål. Hämta först den senaste Oracle ODP.NET-drivrutinen, till exempel `ODP.NET_Managed_ODAC122cR1.zip`-från [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), och ladda sedan upp den tillsammans med `main.cmd` i din behållare.
       
      1. En `ORACLE STANDARD ODBC` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera Oracle ODBC-drivrutinen och konfigurera DSN på varje nod i din Azure-SSIS IR. Med den här installationen kan du använda ODBC-anslutnings hanteraren/källa/mål eller Power Query anslutnings hanterare/källa med ODBC-datakälla för att ansluta till Oracle-servern. Börja med att ladda ned den senaste Oracle Instant-klienten (Basic-paket eller Basic lite-paket) och ODBC-paket – till exempel 64-bitars paketen [här](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic package: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, Basic lite-paket: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC-paket: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) eller 32-bitars paket Härifrån [(Basic](https://www.oracle.com/technetwork/topics/winsoft-085727.html) package: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, Basic lite-paket: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC-paket: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) och ladda upp dem tillsammans med `main.cmd` i din behållare.

      1. En `SAP BW` mapp som innehåller ett anpassat installations skript (`main.cmd`) för att installera SAP .NET Connector-sammansättningen (`librfc32.dll`) på varje nod i Azure-SSIS IR Enterprise-versionen. Med den här installationen kan du använda SAP BW anslutnings hanteraren, källa och mål. Först laddar du upp 64-bitars-eller 32-bitars versionen av `librfc32.dll` från SAP-installationsmappen till din behållare, tillsammans med `main.cmd`. Skriptet kopierar sedan SAP-sammansättningen till mappen `%windir%\SysWow64` eller `%windir%\System32` under installationen.

      1. En `STORAGE` mapp som innehåller en anpassad installation för att installera Azure PowerShell på varje nod i din Azure-SSIS IR. Med den här installationen kan du distribuera och köra SSIS-paket som kör [PowerShell-skript för att manipulera ditt Azure Storage-konto](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopiera `main.cmd`, ett exempel `AzurePowerShell.msi` (eller installera den senaste versionen) och `storage.ps1` till din behållare. Använd PowerShell. dtsx som en mall för dina paket. Paket mal len kombinerar en [hämtnings uppgift för Azure-Blob](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), som laddar ned `storage.ps1` som ett ändrings Bart PowerShell-skript och en process för att [köra processer](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) som kör skriptet på varje nod.

      1. En `TERADATA` mapp som innehåller ett anpassat installations skript (`main.cmd`), dess associerade fil (`install.cmd`) och installations paket (`.msi`). De här filerna installerar Teradata-kopplingar, TPT-API: et och ODBC-drivrutinen på varje nod i Azure-SSIS IR Enterprise Edition. Med den här installationen kan du använda Teradata anslutnings hanteraren, källa och mål. Först hämtar du Teradata-verktyg och-verktyg (TTU) 15. x zip-filen (till exempel `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) från [Teradata](http://partnerintelligence.teradata.com)och laddar sedan upp den tillsammans med ovanstående `.cmd` och `.msi` filer till din behållare.

   ![Mappar i mappen användar scenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Om du vill testa de här anpassade installations exemplen kopierar du och klistrar in innehållet från den valda mappen i din behållare. När du etablerar eller konfigurerar om Azure-SSIS IR med PowerShell kör du `Set-AzDataFactoryV2IntegrationRuntime`-cmdleten med SAS-URI: n för din behållare som värdet för ny `SetupScriptContainerSasUri` parameter.

## <a name="next-steps"></a>Nästa steg

-   [Enterprise-versionen av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Utveckla betalda eller licensierade anpassade komponenter för integrerings körningen av Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
