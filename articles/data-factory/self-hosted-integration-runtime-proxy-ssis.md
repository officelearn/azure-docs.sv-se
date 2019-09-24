---
title: Konfigurera integration runtime med egen värd som proxy för SSIS i Azure Data Factory | Microsoft Docs
description: Lär dig hur du konfigurerar Integration Runtime med egen värd som proxy för Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2ade270011ad5c1e1e5f5940ca305687e52bba86
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200311"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Konfigurera IR med egen värd som proxy för Azure-SSIS IR i ADF
Den här artikeln beskriver hur du kör SQL Server Integration Services-paket (SSIS) på Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF) med egen värd-IR som kon figurer ATS som en proxy.  Med den här funktionen kan du komma åt data lokalt utan att behöva [ansluta till Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Detta är användbart när företags nätverket har en alltför komplex konfiguration/restriktiv princip som du kan använda för att mata in din Azure-SSIS IR.

Med den här funktionen delas ditt paket med en data flödes aktivitet med en lokal data källa till två mellanlagrings uppgifter: den första som körs på din egen värd-IR flyttar först data från den lokala data källan till ett mellanlagringsområde i Azure-Blob Storage, medan den andra som körs på Azure-SSIS IR flyttar sedan data från mellanlagringsområdet till det avsedda data målet.

Den här funktionen ger också andra förmåner/funktioner när du gör det möjligt att tillhandahålla IR i egen värd i regioner som ännu inte stöds av Azure-SSIS IR, tillåta den offentliga statiska IP-adressen för din egen värd-IR i brand väggen för dina data källor osv.

## <a name="prepare-self-hosted-ir"></a>Förbered IR med egen värd
För att kunna använda den här funktionen måste du först skapa en ADF och etablera Azure-SSIS IR under den, om du inte redan har gjort det, genom att följa artikeln så här [etablerar du en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Du måste sedan etablera din egen värd-IR under samma ADF där din Azure-SSIS IR etableras genom att följa artikeln så här skapar du [en IR-artikel med egen värd](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Slutligen måste du ladda ned och installera den senaste versionen av IR med egen värd, samt ytterligare driv rutiner och körning, på din lokala dator/virtuella Azure-dator (VM) enligt följande:
- Hämta och installera den senaste versionen av [IR med egen värd.](https://www.microsoft.com/download/details.aspx?id=39717)
- Om du använder OLEDB-kopplingar i dina paket laddar du ned och installerar relevanta OLEDB-drivrutiner på samma dator där IR för egen värd installeras om du inte redan har gjort det.  Om du använder den tidigare versionen av OLEDB-drivrutinen för SQL Server (SQLNCLI) kan du hämta 64-bitars versionen [här](https://www.microsoft.com/download/details.aspx?id=50402).  Om du använder den senaste versionen av OLEDB-drivrutinen för SQL Server (MSOLEDBSQL) kan du hämta 64-bitars versionen [här](https://www.microsoft.com/download/details.aspx?id=56730).  Om du använder OLEDB-drivrutiner för andra databas system, till exempel PostgreSQL, MySQL, Oracle osv., kan du hämta 64-bitars versionen från deras respektive webbplatser.
- Ladda ned och installera Visual C++ (VC) runtime på samma dator där IR för egen värd installeras om du inte redan har gjort det.  Du kan hämta 64-bitars versionen [här](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Förbered Azure Blob Storage länkade tjänsten för mellanlagring
Skapa en Azure Blob Storage-länkad tjänst under samma ADF där ditt Azure-SSIS IR etableras, om du inte redan har gjort det, genom att följa artikeln [så här skapar du en länkad service](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) artikel i ADF.  Kontrol lera följande:
- **Azure Blob Storage** har valts för **data lager**
- **AutoResolveIntegrationRuntime** har valts för **anslutning via integration runtime**
- Antingen är **konto nyckelns**/**SAS URI**/ **-tjänstens huvud namn** valt för **autentiseringsmetoden**

![Förbered Azure Blob Storage länkade tjänsten för mellanlagring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Konfigurera Azure-SSIS IR med egen värd-IR som proxy
När du har för berett den lokala IR-och Azure-Blob Storage länkade tjänsten för mellanlagring kan du nu konfigurera nya/befintliga Azure-SSIS IR med IR med egen värd som en proxy på ADF Portal/app.  Om din befintliga Azure-SSIS IR körs stoppar du den innan du gör detta och startar sedan om den senare.

På sidan **Avancerade inställningar** markerar du kryss rutan konfigurera den lokala **integration runtime som en proxy för din Azure-SSIS integration runtime** , väljer den lokala IR-och Azure-Blob Storage länkade tjänsten för mellanlagring och anger en BLOB container namn för **mellanlagringsplatsen** om du vill.

![Konfigurera Azure-SSIS IR med egen värd-IR som proxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Aktivera SSIS-paket för att ansluta via proxy
Med hjälp av de senaste SSDT med SSIS-projekts tillägg för Visual Studio som kan hämtas härifrån eller som ett fristående installations program som kan [](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer) [hämtas härifrån,](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) kan du hitta en ny **ConnectByProxy** -egenskap som har lagts till i OLEDB/ Flat fil anslutnings hanterare.  

När du skapar nya paket som innehåller data flödes aktiviteter med OLEDB/flata fil källor för att komma åt databaser/filer lokalt kan du aktivera den här egenskapen **genom att ställa in den på panelen** egenskaper för relevanta anslutnings hanterare.

![Aktivera egenskapen ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Du kan också aktivera den här egenskapen när du kör befintliga paket utan att behöva ändra dem manuellt en i taget.  Det finns två alternativ:
- Att öppna, återskapa och omdistribuera projektet som innehåller de paketen med de senaste SSDTerna att köra på din Azure-SSIS IR: Egenskapen kan sedan aktive ras genom att ställa in den på **True** för relevanta anslutnings hanterare som visas på fliken **anslutnings hanterare** i fönstret kör paket när du kör paket från SSMS.

  ![Aktivera ConnectByProxy Egenskap2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Egenskapen kan också aktive ras genom att ställa in den på **True** för relevanta anslutnings hanterare som visas på fliken **anslutnings hanterare** i [kör SSIS-paket aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när du kör paket i ADF-pipeline.
  
  ![Aktivera ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Omdistribuera projektet som innehåller de paket som ska köras på din SSIS-IR: Egenskapen kan sedan aktive ras genom att ange dess egenskaps `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`Sök väg, och ange den till **True** som en åsidosättande egenskap på fliken **Avancerat** i popup-fönstret kör paket när du kör paket från SSMS.

  ![Aktivera ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Du kan också aktivera egenskapen genom att ange dess egenskaps Sök `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`väg, och ange den till **True** som en åsidosättande egenskap på fliken **åsidosättningar** för att [köra SSIS-paket](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) när du kör paket i ADF-pipeliner.
  
  ![Aktivera ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Felsöka första och andra mellanlagrings aktiviteter
På din egen värd-IR kan du hitta körnings loggarna i `C:\ProgramData\SSISTelemetry` mappen och körnings loggarna för första mellanlagrings aktiviteter i `C:\ProgramData\SSISTelemetry\ExecutionLog` mappen.  Körnings loggarna för andra mellanlagrings aktiviteter finns i dina SSISDB eller angivna loggnings Sök vägar, beroende på om du lagrar dina paket i SSISDB eller fil system/fil resurser/Azure Files respektive.  Du kan också hitta unika ID: n för första mellanlagrings aktiviteter i körnings loggarna för andra mellanlagrings aktiviteter, t. ex. 

![Unikt ID för den första mellanlagrings uppgiften](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Fakturering för första och andra mellanlagrings aktiviteter
De första mellanlagrings aktiviteterna som körs på din egen värd-IR faktureras separat på samma sätt som alla data förflyttnings aktiviteter som körs på lokal IR-överföring faktureras som anges i pris artikeln för [ADF-datapipeline](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De andra mellanlagrings aktiviteterna som körs på din Azure-SSIS IR debiteras inte separat, utan att Azure-SSIS IR som körs faktureras enligt vad som anges i artikeln [Azure-SSIS IR prissättning](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Aktuella begränsningar

- Endast OLEDB/flata fil anslutnings hanterare och OLEDB/flata fil källor stöds för närvarande. 
- Det finns för närvarande stöd för Azure Blob Storage länkade tjänster som kon figurer ATS med **konto nyckel**/**SAS URI**/-autentisering med**huvud namn** .
- Det finns för närvarande stöd för att tillhandahålla IR-installation med egen värd under samma ADF där Azure-SSIS IR har tillhandahållits.
- Det går inte att använda SSIS-parametrar/variabler i egenskaperna för OLEDB/flata fil källor och anslutnings hanterare.

## <a name="next-steps"></a>Nästa steg
När du har konfigurerat din egen värd-IR som en proxy för din Azure-SSIS IR, kan du distribuera och köra dina paket för att komma åt data lokalt som kör SSIS-paket aktiviteter i ADF-pipeline, se [köra SSIS-paket som EXECUTE SSIS Package-aktiviteter i ADF-pipeline ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).