---
title: Anpassad installation för Azure-SSIS-integrering runtime | Microsoft Docs
description: Den här artikeln beskriver hur du använder anpassad installation gränssnittet för Azure-SSIS-integrering runtime
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: ff47060ddfee458279c9fed0fd3fcafcf35229d2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Anpassad installation för Azure-SSIS-integrering runtime

Anpassad installation gränssnitt för Azure-SSIS-integrering körning kan du ändra det standardoperativsystem konfiguration eller miljö (exempelvis för att starta ytterligare Windows-tjänster) eller installera ytterligare komponenter (till exempel sammansättningar, drivrutiner, eller Extensions) på varje nod i Azure-SSIS-IR. I allmänhet tillhandahåller det ett gränssnitt för att lägga till egna konfigurationsstegen under etablering eller omkonfiguration av Azure-SSIS-IR.

Du kan konfigurera dina egna inställningar genom att förbereda ett skript och dess associerade filer och överför dem till en blobbbehållare i Azure Storage-konto. Du anger en delad signatur åtkomst (SAS) identifierare URI (Uniform Resource) för din behållaren när du etablerar eller konfigurera om Azure-SSIS-IR. Varje nod i Azure-SSIS-IR sedan hämtar skriptet och dess associerade filer från din behållare och kör din anpassad installation med förhöjda privilegier. När anpassade installationen är klar överförs varje nod standardutdata för körning och andra loggar till din behållare.

Du kan installera både ledigt eller olicensierad komponenter och betald eller licensierade komponenter. Om du är en ISV Se [hur du utvecklar betald eller licensierad komponenter för Azure-SSIS-IR](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Aktuella begränsningar

-   Om du vill använda `gacutil.exe` för att installera sammansättningar i den globala sammansättningscachen (GAC), du måste ange den som en del av din anpassade konfiguration, eller använda kopian som tillhandahålls i behållaren Public Preview.

-   Om du behöver ansluta till din Azure-SSIS-IR med anpassad installation till ett virtuellt nätverk stöds endast Azure Resource Manager VNet. Klassiska VNet stöds inte.

-   Administrativ resurs stöds inte för närvarande på Azure-SSIS-IR.

-   Om du vill mappa en filresurs på en enhet i din anpassade konfiguration av `net use` kommandot stöds inte för närvarande. Därför kan du inte använda ett kommando som `net use d: \\fileshareserver\sharename`. Använd i stället de `cmdkey` kommandot - exempelvis `cmdkey /add:fileshareserver /user:yyy /pass:zzz` – för att få åtkomst till den `\\fileshareserver\folder` direkt i dina paket.

## <a name="prerequisites"></a>Förutsättningar

För att anpassa Azure SSIS-IR, behöver du följande:

-   [Azure-prenumeration](https://azure.microsoft.com/)

-   [En Azure SQL Database eller hanteras instans server](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Etablera Azure SSIS-IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Ett Azure Storage-konto](https://azure.microsoft.com/services/storage/). För anpassad installation, ladda upp och lagra din anpassade installationsskriptet och dess associerade filer i en blob-behållare. Den anpassade installationen överför även dess körningsloggar till samma blob-behållaren.

## <a name="instructions"></a>Instruktioner

2.  Hämta och installera [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (version 5.4 eller senare).

3.  Förbered din anpassade installationsskriptet och dess associerade filer (till exempel .bat, .cmd, .exe, .dll, .msi eller .ps1-filer).

    1.  Du måste ha en skriptfil med namnet `main.cmd`, vilket är startpunkten för dina egna inställningar.

    2.  Om du vill ha ytterligare loggar som genereras av andra verktyg (till exempel `msiexec.exe`) för att överföras till din behållare, ange miljövariabeln fördefinierade `CUSTOM_SETUP_SCRIPT_LOG_DIR` som loggmappen i skript (exempelvis `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Hämta, installera och starta [Azure Lagringsutforskaren](http://storageexplorer.com/).

    1.  Under **(lokala och bifogad)** höger väljer **Lagringskonton** och välj **Anslut till Azure storage**.

       ![Anslut till Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Välj **använder ett lagringskontonamn och nyckel** och välj **nästa**.

       ![Använda lagringskontots namn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Ange ditt Azure Storage-kontonamnet och nyckeln, Välj **nästa**, och välj sedan **Anslut**.

       ![Ange store kontonamn och nyckel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  Under dina anslutna Azure Storage-konto, högerklicka på **Blobbbehållare**väljer **skapa Blob-behållaren**, och namnge den nya behållaren.

       ![Skapa en blobbehållare](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Välj den nya behållaren och överför din anpassade installationsskriptet och tillhörande filer. Kontrollera att du överför `main.cmd` på den översta nivån på behållaren, inte i en mapp. 

       ![Ladda upp filer till blob-behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Högerklicka på behållaren och välj **hämta signatur för delad åtkomst**.

       ![Hämta signatur för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Skapa SAS-URI för en behållare med en tillräckligt lång förfallotiden och Läs + skriva + listbehörigheter. Du måste ladda ned och köra din anpassade installationsskriptet och dess associerade filer när alla noder i Azure-SSIS-IR avbildade SAS-URI. Du behöver skrivbehörighet att överföra installationsloggarna för körning.

       ![Generera den signatur för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Kopiera och spara din behållarens SAS-URI.

       ![Kopiera och spara signatur för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  När du etablerar eller konfigurera om din Azure-SSIS-IR med PowerShell, innan du börjar din Azure-SSIS-IR kör den `Set-AzureRmDataFactoryV2IntegrationRuntime` med SAS-URI för behållaren som värde för nya `SetupScriptContainerSasUri` parameter. Exempel:

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  När din anpassade installationen är klar och Azure-SSIS-IR startar, kan du hitta standardutdata för `main.cmd` och andra körning loggar in på `main.cmd.log` för storage-behållare.

2.  Ansluta till behållaren förhandsversion med Azure Lagringsutforskaren andra exempel på anpassad installation visas.

    a.  Under **(lokala och bifogad)**, högerklickar du på **Lagringskonton**väljer **Anslut till Azure storage**väljer **använder en anslutningssträng eller en delad åtkomst signaturen URI**, och välj sedan **nästa**.

       ![Ansluta till Azure-lagring med signatur för delad åtkomst](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Välj **använder en SAS-URI** och ange följande SAS-URI för behållaren Public Preview. Välj **nästa**, och välj **Anslut**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Ange Signatur för delad åtkomst för behållaren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Markera den anslutna Public Preview-behållaren och dubbelklicka på den `CustomSetupScript` mapp. I den här mappen är följande:

       1. En `Sample` mapp som innehåller en anpassad installation om du vill installera en standardaktivitet på varje nod i Azure-SSIS-IR. Uppgiften ingenting men strömsparläge under några sekunder. Mappen innehåller en `gacutil` mapp som innehåller `gacutil.exe`.

       2. En `UserScenarios` mapp som innehåller flera anpassade inställningar för verkliga scenarier.

    ![Förhandsversion behållarens innehåll](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Dubbelklicka på den `UserScenarios` mapp. I den här mappen är följande:

       1. En `.NET FRAMEWORK 3.5` mapp som innehåller en anpassad installation för att installera en tidigare version av .NET Framework som kan krävas för anpassade komponenter på varje nod i Azure-SSIS-IR.

       2. En `BCP` mapp som innehåller en anpassad installation för att installera SQL Server-kommandoradsverktyg (`MsSqlCmdLnUtils.msi`), inklusive program för bulk copy program (`bcp`), på varje nod i Azure-SSIS-IR.

       3. En `EXCEL` mapp som innehåller en anpassad installation för att installera sammansättningar med öppen källkod (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, och `ExcelDataReader.dll`) på varje nod i Azure-SSIS-IR.

       4. En `MSDTC` mapp som innehåller en anpassad installation om du vill ändra nätverks- och konfigurationer för Microsoft Distributed Transaction Coordinator (MSDTC)-instans på varje nod i Azure-SSIS-IR.

       5. En `ORACLE ENTERPRISE` mapp som innehåller en anpassad installationsskriptet (`main.cmd`) och konfigurationsfilen för tyst installation (`client.rsp`) att installera drivrutinen för Oracle OCI på varje nod i din Azure-SSIS IR Enterprise Edition. Den här installationen kan du använda Anslutningshanteraren för Oracle, källa och mål. Först ladda ned den senaste klienten Oracle - exempelvis `winx64_12102_client.zip` - från [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) och sedan ladda upp den tillsammans med `main.cmd` och `client.rsp` till din behållare. Om du använder TNS för att ansluta till Oracle kan du också behöva hämta `tnsnames.ora`, redigera den och överföra den till din behållare, så den kan kopieras till Oracle-installationsmappen under installationen.

       6. En `ORACLE STANDARD` mapp som innehåller en anpassad installationsskriptet (`main.cmd`) att installera drivrutinen för Oracle ODP.NET på varje nod i Azure-SSIS-IR. Den här installationen kan du använda ADO.NET Anslutningshanteraren, källa och mål. Först hämta den senaste Oracle ODP.NET drivrutinen - exempelvis `ODP.NET_Managed_ODAC122cR1.zip` - från [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), och sedan ladda upp den tillsammans med `main.cmd` till din behållare.

       7. En `SAP BW` mapp som innehåller en anpassad installationsskriptet (`main.cmd`) att installera sammansättningen för SAP .NET-koppling (`librfc32.dll`) på varje nod i din Azure-SSIS IR Enterprise Edition. Den här installationen kan du använda Anslutningshanteraren för SAP BW, källa och mål. Först överföra 64-bitars eller 32-bitarsversionen av `librfc32.dll` från SAP-installationsmappen till din behållare, tillsammans med `main.cmd`. Skriptet kopieras SAP-sammansättningen i den `%windir%\SysWow64` eller `%windir%\System32` mappen under installationen.

       8. En `STORAGE` mapp som innehåller en anpassad installation för att installera Azure PowerShell på varje nod i Azure-SSIS-IR. Den här installationen kan du distribuera och köra SSIS-paket som kör [PowerShell-skript för att hantera dina Azure Storage-konto](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopiera `main.cmd`, ett exempel på en `AzurePowerShell.msi` (eller installera den senaste versionen) och `storage.ps1` till din behållare. Använd PowerShell.dtsx som en mall för dina paket. Mallen paketet kombinerar en [Azure Blob hämta uppgift](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), vilka uppdateringar som `storage.ps1` som ett PowerShell-skript som kan ändras och en [köra processaktiviteten](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) som kör skriptet på varje nod.

       9. En `TERADATA` mapp som innehåller en anpassad installationsskriptet (`main.cmd)`, dess associerade filen (`install.cmd`), och installer-paket (`.msi`). De här filerna installerar Teradata kopplingar, TPT-API och ODBC-drivrutinen på varje nod i din Azure-SSIS IR Enterprise Edition. Den här installationen kan du använda Anslutningshanteraren för Teradata, källa och mål. Först hämta filen Teradata verktyg och hjälpmedel (TTU) 15.x zip (till exempel `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) från [Teradata](http://partnerintelligence.teradata.com), och överföra den tillsammans med ovanstående `.cmd` och `.msi` filer till en behållare.

    ![Mappar i mappen scenarier](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Om du vill testa exemplen anpassad installation, kopiera och klistra in innehållet från den valda mappen till en behållare. När du etablerar eller konfigurera om din Azure-SSIS-IR med PowerShell kör den `Set-AzureRmDataFactoryV2IntegrationRuntime` med SAS-URI för behållaren som värde för nya `SetupScriptContainerSasUri` parameter.

## <a name="next-steps"></a>Nästa steg

-   [Enterprise-versionen av körningsmiljön Azure SSIS-integrering](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Hur du utvecklar betald eller licensierad anpassade komponenter för Azure-SSIS-integrering runtime](how-to-develop-azure-ssis-ir-licensed-components.md)