---
title: Kör SSIS-paket från SSDT
description: Lär dig hur du kör SSIS-paket i Azure från SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 5f21623af9b89bbb020063dfb72f7b60e65a6ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927707"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Köra SSIS-paket i Azure från SSDT
I den här artikeln beskrivs funktionen för Azure-aktiverade SSIS-projekt (SQL Server Integration Services) på SQL Server Data Tools (SSDT), som gör att du kan köra paket på Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF).  Du kan använda den här funktionen för att testa dina befintliga SSIS-paket innan du lyfter & flytta/migrera dem till Azure eller för att utveckla nya SSIS-paket som ska köras i Azure.

Med den här funktionen kan du skapa en ny Azure-SSIS IR eller bifoga en befintlig till SSIS-projekt och sedan köra dina paket på den.  Vi stöder att du kör paket som ska distribueras till SSIS-katalog (SSISDB) i Projektdistributionsmodellen och de som ska distribueras till filsystem/filresurser/Azure-filer i package deployment Model. 

## <a name="prerequisites"></a>Krav
Om du vill använda den här funktionen kan du hämta och installera det senaste SSDT med SSIS Projects-tillägget för Visual Studio [härifrån](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) eller som ett fristående installationsprogram [härifrån.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-aktivera SSIS-projekt
På SSDT kan du skapa nya Azure-aktiverade SSIS-projekt med mallen **Integration Services Project (Azure-Enabled).**

![Nytt Azure-aktiverat SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Alternativt kan du Azure-aktivera dina befintliga SSIS-projekt genom att högerklicka på projektnoden på panelen Lösningsutforskaren i SSDT för att visa en meny och sedan välja menyalternativet **Azure-enabled.**

![Azure-Aktivera befintliga SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure-aktivera dina befintliga SSIS-projekt kräver att du ställer in deras målserverversion som den senaste som stöds av Azure-SSIS IR, som för närvarande är **SQL Server 2017**, så om du inte redan har gjort det kommer ett dialogfönster att dyka upp för att göra detta.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Ansluta Azure-aktiverade projekt till SSIS i Azure Data Factory
Genom att ansluta dina Azure-aktiverade projekt till SSIS i ADF kan du ladda upp dina paket till Azure-filer och köra dem på Azure-SSIS IR.  Så här gör du så här:

1. Högerklicka på projekt- eller Linked Azure Resources-noden på panelen Lösningsutforskaren i SSDT för att visa en meny och välj menyalternativet Anslut till **SSIS i Azure Data Factory** för att starta **Linked Azure Resources** **SSIS i anslutningsguiden för ADF**.

   ![Anslut till SSIS i ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. På **SSIS i ADF Introduktion** sida, granska introduktionen och klicka på **nästa** knappen för att fortsätta.

   ![SSIS i ADF Introduktion](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. På sidan **Välj SSIS IR på ADF** väljer du din befintliga ADF och Azure-SSIS IR för att köra paket eller skapa nya om du inte har några.
   - Om du vill välja din befintliga Azure-SSIS IR väljer du den relevanta Azure-prenumerationen och ADF först.
   - Om du väljer din befintliga ADF som inte har någon Azure-SSIS IR klickar du på knappen **Skapa SSIS IR** för att skapa en ny på ADF-portalen/appen.
   - Om du väljer din befintliga Azure-prenumeration som inte har någon ADF klickar du på knappen **Skapa SSIS IR** för att starta **guiden Återskapa integrationskörningstid**, där du kan ange platsen och prefixet för att vi automatiskt ska skapa en ny Azure Resource Group, Data Factory och SSIS IR för din räkning, med namnet i följande mönster: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Välj SSIS IR i ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. På sidan **Välj Azure Storage** väljer du ditt befintliga Azure Storage-konto för att ladda upp paket till Azure-filer eller skapa ett nytt om du inte har några.
   - Om du vill välja ditt befintliga Azure Storage-konto väljer du den relevanta Azure-prenumerationen först.
   - Om du väljer samma Azure-prenumeration som din Azure-SSIS IR som inte har något Azure Storage-konto klickar du på knappen **Skapa Azure Storage** för att vi automatiskt ska kunna skapa en ny för din räkning på samma plats som din Azure-SSIS IR, namngiven genom att kombinera ett prefix med ditt Azure-SSIS IR-namn och dess skapandedatum.
   - Om du väljer en annan Azure-prenumeration som inte har något Azure Storage-konto klickar du på knappen **Skapa Azure Storage** för att skapa en ny på Azure-portalen.
   
   ![Välj Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Klicka på **knappen Anslut** för att slutföra anslutningen.  Vi visar ditt valda Azure-SSIS IR- och Azure Storage-konto under **noden Länkade Azure Resources** på panelen Solution Explorer på SSDT.  Vi uppdaterar också statusen för din Azure-SSIS IR, medan du kan hantera den genom att högerklicka på noden för att visa en meny och sedan välja menyalternativet **Start\Stop\Manage** som tar dig till ADF-portalen/appen för att göra det.

## <a name="execute-ssis-packages-in-azure"></a>Köra SSIS-paket i Azure
### <a name="starting-package-executions"></a>Starta paketkörningar
När du har anslutit dina projekt till SSIS i ADF kan du köra paket på Azure-SSIS IR.  Du har två alternativ för att starta paketkörningar:
-  Klicka på **Start-knappen** i verktygsfältet SSDT för att rullgardinsmenyn ska väljas för **kör i Azure-menyalternativet** 

   ![Körs i Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Högerklicka på paketnoden på panelen Solution Explorer i SSDT för att visa en meny och välj det kör paketet i Azure-menyalternativet. **Execute Package in Azure**

   ![Kör paket i Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Köra dina paket i Azure kräver att du har en kör Azure-SSIS IR, så om din Azure-SSIS IR stoppas, kommer ett dialogfönster att dyka upp för att starta den.  Exklusive en anpassad inställningstid bör den här processen slutföras inom 5 minuter, men det kan ta cirka 20 - 30 minuter för Azure-SSIS IR som ansluter till ett virtuellt nätverk.  När du har kört dina paket i Azure kan du stoppa din Azure-SSIS IR för att hantera dess löpande kostnad genom att högerklicka på noden i Solution Explorer-panelen i SSDT för att visa en meny och sedan välja menyalternativet **Start\Stop\Manage** som tar dig till ADF-portalen/appen för att göra det.

### <a name="checking-package-execution-logs"></a>Kontrollera paketkörningsloggar
När du startar paketkörningen formaterar och visar vi dess inloggning i förloppsfönstret i SSDT.  För ett tidskrävande paket uppdaterar vi regelbundet loggen via protokollet.  Du kan stoppa paketkörningen genom att klicka på **stoppknappen** i verktygsfältet SSDT som omedelbart avbryter den.  Du kan också tillfälligt hitta loggråvaran i dess unc-sökväg (Universal Naming Convention): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, men vi rensar upp efter en dag.

### <a name="switching-package-protection-level"></a>Skyddsnivå för växling av paket
Att köra SSIS-paket i Azure stöder inte **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey-skyddsnivåer.**  Följaktligen, om dina paket är konfigurerade med dessa, kommer vi tillfälligt att växla dem till **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, respektive, med slumpmässigt genererade lösenord när vi laddar upp dina paket till Azure Files för körning på din Azure-SSIS IR.

> [!NOTE]
> Om dina paket innehåller Kör paketuppgifter som refererar till andra paket som konfigurerats med **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey-skyddsnivåer,** måste du konfigurera om de andra paketen manuellt för att använda **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, respektive innan du kör dina paket.

Om dina paket redan är konfigurerade med **EncryptSensitiveWithPassword**/**EncryptAllWithPassword-skyddsnivåer** kommer vi att hålla dem oförändrade, men kommer fortfarande att använda slumpmässigt genererade lösenord när vi laddar upp dina paket till Azure Files för körning på din Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Använda paketkonfigurationsfil
Om du använder paketkonfigurationsfiler i paketdistributionsmodellen för att ändra variabla värden vid körning, överför vi automatiskt dessa filer med dina paket till Azure-filer för körning på din Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Använda körningspaketuppgift
Om dina paket innehåller Kör paketuppgifter som refererar till andra paket som lagras på lokala filsystem måste du utföra följande ytterligare inställningar:

1. Ladda upp de andra paketen till Azure-filer under samma Azure Storage-konto som är kopplat till dina projekt och få deras nya UNC-sökväg, t.ex.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Ersätt filsökvägen för de andra paketen i filanslutningshanteraren för att köra paketuppgifter med den nya UNC-sökvägen
   - Om datorn som kör SSDT inte kan komma åt den nya UNC-sökvägen kan du ändra filsökvägen på egenskapspanelen i Filanslutningshanteraren
   - Du kan också använda en variabel för filsökvägen för att tilldela rätt värde vid körning

Om dina paket innehåller Kör paketuppgifter som refererar till andra paket i samma projekt behövs ingen ytterligare installation.

## <a name="next-steps"></a>Nästa steg
När du är nöjd med att köra dina paket i Azure från SSDT kan du distribuera och köra dem som Kör SSIS-paketaktiviteter i ADF-pipelines, se [Kör SSIS-paket som Kör SSIS-paketaktiviteter i ADF-pipelines](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
