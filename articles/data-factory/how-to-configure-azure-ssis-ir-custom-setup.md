---
title: Anpassa installationsprogrammet för Azure-SSIS integration runtime | Microsoft Docs
description: Den här artikeln beskriver hur du använder gränssnittet anpassad installation för den Azure-SSIS integration runtime för att installera ytterligare komponenter eller ändra inställningar
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
ms.openlocfilehash: cfa9d6a1a287281bec91facf04c73506db81f84a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711570"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Anpassa installationsprogrammet för Azure-SSIS integration runtime

Anpassad installation gränssnitt för Azure-SSIS Integration Runtime tillhandahåller ett gränssnitt för att lägga till egna genomgången under etablering eller omkonfiguration av din Azure-SSIS IR. Anpassad installation kan du ändra det standardoperativsystem konfiguration eller miljön (till exempel att starta ytterligare Windows-tjänster eller spara autentiseringsuppgifter för filresurser) eller installera ytterligare komponenter (till exempel sammansättningar, drivrutiner eller tillägg) på varje nod i din Azure-SSIS IR.

Du kan konfigurera din anpassade konfiguration genom att förbereda ett skript och dess associerade filer och överför dem till en blobbehållare i Azure Storage-kontot. Du anger en signatur för delad åtkomst (SAS) identifierare URI (Uniform Resource) för behållaren när du etablerar eller konfigurera om din Azure-SSIS IR. Varje nod i din Azure-SSIS IR sedan ladda ned skriptet och dess associerade filer från din behållare och kör din anpassade installationsprogrammet med förhöjd behörighet. När anpassade installationen är klar, överför varje nod standardutdata för körning och andra loggar till din behållare.

Du kan installera både kostnadsfria eller olicensierad komponenter och betalda eller licensierade komponenter. Om du är en oberoende Programvaruleverantör, se [hur du utvecklar betald eller licensierade komponenter för Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Noderna v2-serien i Azure-SSIS IR är inte lämpligt för anpassad installation, så Använd v3-serien noderna istället.  Om du redan använder noder v2-serien, växla för att använda noder v3-serien så snart som möjligt.

## <a name="current-limitations"></a>Aktuella begränsningar

-   Om du vill använda `gacutil.exe` för att installera sammansättningar i Global Assembly Cache (GAC), måste du ange `gacutil.exe` som en del av din anpassad installation, eller Använd kopian som tillhandahålls i offentlig förhandsversion behållaren.

-   Om du vill referera till en undermapp i ditt skript `msiexec.exe` stöder inte den `.\` notation att referera till rotmappen. Använder du ett kommando som `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` i stället för `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Om du vill ansluta till din Azure-SSIS IR med installationen av anpassad till ett virtuellt nätverk stöds endast Azure Resource Manager-nätverk. Klassiskt virtuellt nätverk stöds inte.

-   Administrativ resurs stöds inte för närvarande på Azure-SSIS IR.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att anpassa din Azure-SSIS IR, behöver du följande:

-   [Azure-prenumeration](https://azure.microsoft.com/)

-   [En Azure SQL Database eller hanterad instans-server](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Etablera din Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Ett Azure Storage-konto](https://azure.microsoft.com/services/storage/). För anpassad installation överföra och lagra dina anpassade installationsskriptet och dess associerade filer i en blob-behållare. Anpassade installationen överför även dess skrivkörning loggas till samma blob-behållaren.

## <a name="instructions"></a>Instruktioner

1. Ladda ned och installera [Azure PowerShell](/powershell/azure/install-az-ps).

1. Förbered din anpassade installationsskriptet och dess associerade filer (till exempel .bat, .cmd, .exe, .dll, .msi eller .ps1-filer).

   1.  Du måste ha en skriptfil med namnet `main.cmd`, vilket är startpunkten för din anpassade konfiguration.

   1.  Om du vill ha ytterligare loggar som genereras av andra verktyg (till exempel `msiexec.exe`) för att överföras till din behållare, ange miljövariabeln fördefinierade `CUSTOM_SETUP_SCRIPT_LOG_DIR` som loggmapp i dina skript (exempelvis `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Hämta, installera och starta [Azure Storage Explorer](https://storageexplorer.com/).

   1. Under **(lokala och anslutna)**, höger-Välj **Lagringskonton** och välj **Anslut till Azure storage**.

      ![Anslut till Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Välj **använder en lagringskontonamnet och nyckeln** och välj **nästa**.

      ![Använda lagringskontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Ange ditt Azure Storage-kontonamn och nyckel, Välj **nästa**, och välj sedan **Connect**.

      ![Ange store-kontonamnet och nyckeln](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Högerklicka på ditt Azure Storage-konto som anslutna **Blob-behållare**väljer **skapa Blobbehållare**, och namnge den nya behållaren.

      ![Skapa en blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Välj den nya behållaren och ladda upp din egen installationsskriptet och dess associerade filer. Se till att du laddar upp `main.cmd` på den översta nivån i din behållare, inte i valfri mapp. Kontrollera också att din behållare innehåller endast de anpassa nödvändiga filerna, så hämta dem till din Azure-SSIS IR inte tar lång tid. Den längsta tid för anpassad installation är för närvarande inställd på 45 minuter innan tidsgränsen uppnås, vilket omfattar tiden för att ladda ned alla filer från din behållare och installera dem på Azure-SSIS IR. Om en längre period, skapar du ett supportärende.

      ![Ladda upp filer till blob-behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Högerklicka på behållaren och välj **hämta signatur för delad åtkomst**.

      ![Hämta signatur för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Skapa SAS-URI för behållaren med en tillräckligt lång förfallotid och läsa + skriva + lista behörigheter. Du behöver SAS-URI att hämta och köra din anpassade installationsskriptet och dess associerade filer varje gång en nod för din Azure-SSIS IR är avbildning/startas om. Du måste skriva behörighet att ladda upp loggar för körningen av installationsprogrammet.

      > [!IMPORTANT]
      > Kontrollera att SAS-URI inte upphör att gälla och anpassade Installationsresurser alltid är tillgängliga under hela livscykeln för din Azure-SSIS IR från skapandet tas bort, särskilt om du regelbundet stoppa och starta din Azure-SSIS IR under denna period.

      ![Generera den signatur för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Kopiera och spara din behållare SAS-URI.

      ![Kopiera och spara signatur för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. När du etablerar eller konfigurera om din Azure-SSIS IR med Användargränssnittet för Data Factory innan du börjar din Azure-SSIS IR, ange din behållare i fältet lämplig SAS-URI på **avancerade inställningar** panelen:

      ![Ange Signatur för delad åtkomst](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      När du etablerar eller konfigurera om din Azure-SSIS IR med PowerShell, innan du börjar din Azure-SSIS IR måste köra den `Set-AzDataFactoryV2IntegrationRuntime` cmdlet med SAS-URI behållarens som värde för nya `SetupScriptContainerSasUri` parametern. Exempel:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. När din anpassade installationen har slutförts och din Azure-SSIS IR börjar, du kan hitta standardutdata för `main.cmd` och andra körning loggar den `main.cmd.log` mappen storage-behållare.

1. Om du vill se andra exempel på anpassad installation, ansluter du till behållaren offentlig förhandsversion med Azure Storage Explorer.

   a.  Under **(lokala och anslutna)**, högerklicka på **Lagringskonton**väljer **Anslut till Azure storage**väljer **använda en anslutningssträng eller en delad åtkomst signatur-URI för**, och välj sedan **nästa**.

      ![Anslut till Azure storage med signatur för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Välj **använder en SAS-URI** och ange följande SAS-URI för behållaren som offentlig förhandsversion. Välj **nästa**, och klicka sedan **Connect**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Ange Signatur för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Välj behållaren anslutna offentlig förhandsversion och dubbelklicka på den `CustomSetupScript` mapp. I den här mappen är följande:

      1. En `Sample` mappen som innehåller en anpassad installation om du vill installera en grundläggande aktivitet på varje nod i din Azure-SSIS IR. Uppgiften gör ingenting men strömsparläge under några sekunder. Mappen innehåller också en `gacutil` mapp, hela innehåll (`gacutil.exe`, `gacutil.exe.config`, och `1033\gacutlrc.dll`) kan kopieras som är i din behållare. Dessutom `main.cmd` innehåller kommentarer för att spara autentiseringsuppgifter för filresurser.

      1. En `UserScenarios` mappen som innehåller flera anpassade inställningar för verkliga scenarier.

   ![Offentlig förhandsversion behållarens innehåll](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Dubbelklicka på den `UserScenarios` mapp. I den här mappen är följande:

      1. En `.NET FRAMEWORK 3.5` mappen som innehåller en anpassad installation om du vill installera en tidigare version av .NET Framework som kan krävas för anpassade komponenter på varje nod i din Azure-SSIS IR.

      1. En `BCP` mappen som innehåller en anpassad installation för att installera SQL Server kommandoradsverktyg (`MsSqlCmdLnUtils.msi`), inklusive program bulk copy program (`bcp`), på varje nod i din Azure-SSIS IR.

      1. En `EXCEL` mappen som innehåller en anpassad installation för att installera open source-sammansättningar (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, och `ExcelDataReader.dll`) på varje nod i din Azure-SSIS IR.

      1. En `ORACLE ENTERPRISE` mappen som innehåller en anpassad installationsskriptet (`main.cmd`) och konfigurationsfilen för tyst installation (`client.rsp`) att installera Oracle-kopplingar och OCI drivrutinen på varje nod i din Azure-SSIS IR Enterprise Edition. Den här konfigurationen kan du använda Anslutningshanteraren för Oracle-, käll- och målservrarna. Först ladda ned Microsoft Connectors v5.0 för Oracle (`AttunitySSISOraAdaptersSetup.msi` och `AttunitySSISOraAdaptersSetup64.msi`) från [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) och den senaste Oracle-klienten – till exempel `winx64_12102_client.zip` – från [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), sedan överföra dem alla tillsammans med `main.cmd` och `client.rsp` i din behållare. Om du använder TNS för att ansluta till Oracle kan du också behöva hämta `tnsnames.ora`, redigera den och överför den till din behållare, så det kan kopieras till Oracle-installationsmappen under installationen.

      1. En `ORACLE STANDARD ADO.NET` mappen som innehåller en anpassad installationsskriptet (`main.cmd`) att installera Oracle ODP.NET-drivrutin på varje nod i din Azure-SSIS IR. Den här konfigurationen kan du använda Anslutningshanteraren för ADO.NET, källa och mål. Först laddar du ned den senaste Oracle ODP.NET-drivrutinen – till exempel `ODP.NET_Managed_ODAC122cR1.zip` – från [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), och sedan ladda upp den tillsammans med `main.cmd` i din behållare.
       
      1. En `ORACLE STANDARD ODBC` mappen som innehåller en anpassad installationsskriptet (`main.cmd`) att installera Oracle ODBC-drivrutinen och konfigurera DSN på varje nod i din Azure-SSIS IR. Den här konfigurationen kan du använda ODBC Connection Manager/källa/mål- eller Power Query Manager/anslutningskälla med ODBC-typ av datakälla för att ansluta till Oracle-servern. Först laddar du ned de senaste Oracle omedelbar klienten (Baspaket eller Lite Baspaket) och ODBC-paket – till exempel 64-bitars paket från [här](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Baspaket: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, Lite Baspaket: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC-paketet : `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) eller 32-bitars paket från [här](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Baspaket: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, Lite Baspaket: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC-paketet: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), och sedan ladda upp dem tillsammans med `main.cmd` i din behållare.

      1. En `SAP BW` mappen som innehåller en anpassad installationsskriptet (`main.cmd`) att installera SAP .NET connector sammansättningen (`librfc32.dll`) på varje nod i din Azure-SSIS IR Enterprise Edition. Den här konfigurationen kan du använda Anslutningshanteraren för SAP BW, källa och mål. Börja ladda upp 64-bitars eller 32-bitarsversionen av `librfc32.dll` från SAP-installationsmappen till behållaren, tillsammans med `main.cmd`. Skriptet kopierar sedan SAP-sammansättningen i den `%windir%\SysWow64` eller `%windir%\System32` mapp under installationen.

      1. En `STORAGE` mappen som innehåller en anpassad installation för att installera Azure PowerShell på varje nod i din Azure-SSIS IR. Den här konfigurationen kan du distribuera och köra SSIS-paket som kör [PowerShell-skript för att ändra ditt Azure Storage-konto](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopiera `main.cmd`, ett exempel `AzurePowerShell.msi` (eller installera den senaste versionen) och `storage.ps1` till behållaren. Använd PowerShell.dtsx som en mall för dina paket. Mallen paketet kombinerar en [Azure Blob hämta uppgift](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), vilka nedladdningar `storage.ps1` som en ändringsbar PowerShell-skript och en [köra processaktiviteten](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) som körs skriptet på varje nod.

      1. En `TERADATA` mappen som innehåller en anpassad installationsskriptet (`main.cmd`), dess associerade filen (`install.cmd`), och installationspaket (`.msi`). De här filerna installera Teradata kopplingar, TPT-API och ODBC-drivrutinen på varje nod i din Azure-SSIS IR Enterprise Edition. Den här konfigurationen kan du använda Anslutningshanteraren för Teradata, källa och mål. Först laddar du ned den 15.x Teradata-verktyg och hjälpmedel (TTU) zip-filen (till exempel `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) från [Teradata](http://partnerintelligence.teradata.com), och sedan ladda upp den tillsammans med ovanstående `.cmd` och `.msi` filer i behållaren.

   ![Mappar i mappen scenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Om du vill prova de här exemplen för anpassad installation, kopiera och klistra in innehållet från den valda mappen i din behållare. När du etablerar eller konfigurera om din Azure-SSIS IR med PowerShell, kör den `Set-AzDataFactoryV2IntegrationRuntime` cmdlet med SAS-URI behållarens som värde för nya `SetupScriptContainerSasUri` parametern.

## <a name="next-steps"></a>Nästa steg

-   [Enterprise-versionen av Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Hur du utvecklar betald eller licensierad anpassade komponenter för Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
