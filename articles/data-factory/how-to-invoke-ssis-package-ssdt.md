---
title: Köra SSIS-paket i Azure Data Factory från SSDT | Microsoft Docs
description: Lär dig hur du kör SSIS-paket i Azure från SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9ddf427d7d749dff1af45b3f6f83d20a89e1aae0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678402"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Köra SSIS-paket i Azure från SSDT
I den här artikeln beskrivs funktionen för SSIS-projekt (Azure-aktiverade SQL Server Integration Services) på SQL Server Data Tools (SSDT), vilket gör att du kan köra paket på Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF).  Du kan använda den här funktionen för att testa dina befintliga SSIS-paket innan du lyft & Shift/migrera dem till Azure eller för att utveckla nya SSIS-paket som ska köras i Azure.

Med den här funktionen kan du skapa en ny Azure-SSIS IR eller koppla en befintlig till SSIS-projekt och sedan köra paketen på den.  Vi stöder körning av paket som ska distribueras till SSIS-katalogen (SSISDB) i projekt distributions modellen och de som ska distribueras till fil system/fil resurser/Azure Files i paket distributions modellen. 

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda den här funktionen laddar du ned och installerar de senaste SSDT med SSIS Projects-tillägget för Visual Studio [härifrån eller som](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) ett fristående installations program [härifrån.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure – aktivera SSIS-projekt
På SSDT kan du skapa nya Azure-aktiverade SSIS-projekt med hjälp av mallen för **Integration Services-projekt (Azure-aktiverad)** .

![Nytt Azure-aktiverat SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Du kan också Azure-aktivera dina befintliga SSIS-projekt genom att högerklicka på noden projekt i Solution Explorer panel i SSDT för att öppna en meny och sedan välja det **Azure-aktiverade** meny alternativet.

![Azure – aktivera befintligt SSIS-projekt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure-aktivera dina befintliga SSIS-projekt kräver att du anger att mål Server versionen ska vara den senaste versionen som stöds av Azure-SSIS IR, som för närvarande är **SQL Server 2017**, så om du inte redan har gjort det öppnas ett dialog fönster för att göra detta.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Ansluta Azure-aktiverade projekt till SSIS i Azure Data Factory
Genom att ansluta dina Azure-aktiverade projekt till SSIS i ADF kan du ladda upp dina paket till Azure Files och köra dem på Azure-SSIS IR.  Det gör du genom att följa dessa steg:

1. Högerklicka på noden projekt eller länkad **Azure-resurser** på Solution Explorers panel i SSDT för att öppna en meny och markera meny alternativet **Anslut till SSIS i Azure Data Factory** för att starta **SSIS i guiden för automatisk**upplyft-anslutning.

   ![Ansluta till SSIS i ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. På sidan **SSIS på ADF** -introduktionen granskar du introduktionen och klickar på knappen **Nästa** för att fortsätta.

   ![SSIS i ADF introduktion](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. På sidan **Välj SSIS IR på ADF** väljer du den befintliga ADF-filen och Azure-SSIS IR för att köra paket eller skapar nya om du inte har några.
   - Välj den befintliga Azure-SSIS IR genom att välja den aktuella Azure-prenumerationen och ADF först.
   - Om du väljer din befintliga ADF som inte har någon Azure-SSIS IR klickar du på knappen **skapa SSIS IR** för att skapa en ny på ADF-portalen/-appen.
   - Om du väljer din befintliga Azure-prenumeration som saknar ADF, klickar du på knappen **skapa SSIS IR** för att starta guiden skapa **integration runtime**, där du kan ange plats och prefix för oss för att automatiskt skapa en ny Azure Resurs grupp, Data Factory och SSIS IR för din räkning, med namnet i följande mönster: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Välj SSIS IR i ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. På sidan **välj Azure Storage** väljer du ditt befintliga Azure Storage-konto för att överföra paket till Azure Files eller skapa ett nytt om du inte har några.
   - Välj den aktuella Azure-prenumerationen först om du vill välja ditt befintliga Azure Storage-konto.
   - Om du väljer samma Azure-prenumeration som Azure-SSIS IR som inte har något Azure Storage konto klickar du på knappen **skapa Azure Storage** så att vi automatiskt skapar en ny för din räkning på samma plats som din Azure-SSIS IR, med namnet av kombinera ett prefix för ditt Azure-SSIS IR-namn och dess skapelse datum.
   - Om du väljer en annan Azure-prenumeration som inte har något Azure Storage konto klickar du på knappen **skapa Azure Storage** för att skapa en ny på Azure Portal.
   
   ![Välj Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Klicka på knappen **Anslut** för att slutföra anslutningen.  Vi kommer att visa det valda Azure-SSIS IR-och Azure Storage-kontot under noden **länkade Azure-resurser** på Solution Explorer panelen i SSDT.  Vi uppdaterar också statusen för din Azure-SSIS IR, medan du kan hantera den genom att högerklicka på noden för att öppna en meny och sedan välja **Start\Stop\Manage** -menyobjektet som leder dig till ADF-portalen/-appen.

## <a name="execute-ssis-packages-in-azure"></a>Köra SSIS-paket i Azure
### <a name="starting-package-executions"></a>Starta paket körningar
När du har anslutit dina projekt till SSIS i ADF kan du köra paket på Azure-SSIS IR.  Det finns två alternativ för att starta paket körningar:
-  Klicka på **Start** -knappen i SSDT-verktygsfältet för att ta bort en meny och välj meny alternativet **Kör i Azure** 

   ![Kör i Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Högerklicka på noden paket i Solution Explorer panel i SSDT för att öppna en meny och välj kommandot **Kör paket i Azure** -menyn.

   ![Kör paket i Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> För att kunna köra dina paket i Azure måste du ha en Azure-SSIS IR, så om din Azure-SSIS IR har stoppats visas ett fönster i dialog rutan där du kan starta det.  Om du exkluderar en anpassad konfigurations tid bör den här processen slutföras inom 5 minuter, men det kan ta cirka 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk.  När du har kört dina paket i Azure kan du stoppa Azure-SSIS IR för att hantera dess löpande kostnad genom att högerklicka på noden i Solution Explorer panel i SSDT för att öppna en meny och sedan välja **Start\Stop\Manage** -menyobjektet som tar dig till ADF-portalen/ app för att göra det.

### <a name="checking-package-execution-logs"></a>Kontrollerar paket körnings loggar
När du startar paket körningen kommer vi att formatera och visa loggen i förlopps fönstret för SSDT.  För ett långvarigt paket kommer vi regelbundet att uppdatera loggen med minuterna.  Du kan stoppa paket körningen genom att klicka på **stopp** knappen i SSDT-verktygsfältet som omedelbart avbryter det.  Du kan också tillfälligt hitta logg rå data i sin Universal Naming Convention (UNC): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`, men vi kommer att rensa upp den efter en dag.

### <a name="switching-package-protection-level"></a>Växlar paket skydds nivå
Körning av SSIS-paket i Azure stöder inte **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** -skydds nivåer.  Om dina paket har kon figurer ATS med dessa växlar vi tillfälligt till **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**med slumpmässigt genererade lösen ord när vi laddar upp din paket i Azure Files för körning på din Azure-SSIS IR.

> [!NOTE]
> Om dina paket innehåller uppgifter för att köra paket som refererar till andra paket som kon figurer ATS med **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** -skydds nivåer måste du manuellt konfigurera om de andra paketen till Använd **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**innan du kör dina paket.

Om dina paket redan har kon figurer ATS med **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** -skydds nivåer behåller vi dem oförändrade, men kommer fortfarande att använda slumpmässigt genererade lösen ord när vi laddar upp din paket i Azure Files för körning på din Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Använda paket konfigurations fil
Om du använder paketets konfigurationsfiler i paket distributions modellen för att ändra variabel värden vid körning kommer vi automatiskt att ladda upp dessa filer med dina paket till Azure Files för körning på din Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Använda uppgiften Kör paket
Om dina paket innehåller uppgifter för att köra paket som refererar till andra paket som lagras på lokala fil system, måste du utföra följande ytterligare inställningar:

1. Överför de andra paketen till Azure Files under samma Azure Storage-konto som är anslutet till dina projekt och hämta sin nya UNC-sökväg, t. ex.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Ersätt fil Sök vägen för de andra paketen i fil anslutnings hanteraren för utföra paket aktiviteter med sin nya UNC-sökväg
   - Om datorn som kör SSDT inte kan komma åt den nya UNC-sökvägen kan du ändra fil Sök vägen på egenskaps panelen i fil anslutnings hanteraren
   - Alternativt kan du använda en variabel för fil Sök vägen för att tilldela rätt värde vid körning

Om dina paket innehåller uppgifter för att köra paket som refererar till andra paket i samma projekt krävs ingen ytterligare konfiguration.

## <a name="next-steps"></a>Nästa steg
När du är nöjd med att köra dina paket i Azure från SSDT kan du distribuera och köra dem som kör SSIS-paket-aktiviteter i ADF-pipelines, se [köra SSIS-paket som EXECUTE SSIS-paket aktiviteter i ADF-pipeline](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
