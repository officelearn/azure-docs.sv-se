---
title: Felsökning i SSIS integration runtime-paketkörning | Microsoft Docs
description: Den här artikeln innehåller felsökningsinformation för körning av SSIS-paket i SSIS-Integreringskörning
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: f17c364d258ef356a98180c9903603d92a6a9245
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078526"
---
# <a name="troubleshooting-package-execution-in-ssis-integration-runtime"></a>Felsökning av körning av paket i SSIS-integreringskörning

Den här artikeln innehåller de vanligaste felen som du kan orsaka vid körning av SSIS-paket i SSIS Integration Runtime, möjliga orsaker och åtgärder att lösa felen.

## <a name="where-can-i-find-logs-for-troubleshoot"></a>Var hittar jag loggar för felsökning

* Den ADF-portalen kan användas för att kontrollera resultatet av SSIS-paket Körningsaktivitet inklusive Körningsresultat, felmeddelanden och åtgärds-ID. Information finns på [övervaka pipelinen](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)

* SSIS-katalogen (SSISDB) kan användas för att kontrollera detalj loggar för körningen. Information finns på [övervakaren som kör paket och andra åtgärder](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)

## <a name="common-errors-causes-and-solution"></a>Vanliga fel, orsaker och lösning

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Felmeddelande: `"Connection Timeout Expired."` eller `"The service has encountered an error processing your request. Please try again."`

* Möjlig orsak & rekommenderad åtgärd:
  * Data källan/målet är överbelastad. Kontrollera belastningen på dina Data källa/mål och se om det finns tillräckligt med kapacitet. Till exempel om Azure SQL används, rekommenderas att överväga att skala upp om databasen är troligt att tidsgränsen nåddes.
  * Nätverket mellan SSIS Integration Runtime och Data källan/målet är instabil, särskilt när anslutningen är över flera regioner eller mellan lokala och azure. Vi rekommenderar för att tillämpa återförsöksmönstret i SSIS-paket genom att följa steg:
    * Se till att dina SSIS-paket kan köra vid fel utan sidoeffekt (till exempel. dataförlust, data dup...)
    * Konfigurera den **försök** och **återförsöksintervallet** av köra SSIS-paket aktivitet på fliken Allmänt ![ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * För ADO.NET och OLEDB källa/mål-komponent kan ConnectRetryCount och ConnectRetryInterval anges i Anslutningshanteraren i SSIS-paket eller SSIS-aktivitet

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Felmeddelande: `"ADO NET Source has failed to acquire the connection '...' with the following error message: "A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible."`

* Möjlig orsak & rekommenderad åtgärd:
  * Det här problemet vanligtvis innebär att Data källan/målet är inte tillgänglig från SSIS Integration Runtime som kan orsakas av olika skäl:
    * Kontrollera att du låter den Data källa/mål namn/IP-Adressen på rätt sätt
    * Kontrollera att brandväggen är korrekt
    * Kontrollera att ditt virtuella nätverk har konfigurerats korrekt om dina Data källa/mål finns i en lokal.
      * Du kan kontrollera om problemet kommer från konfiguration av virtuellt nätverk genom att etablera en virtuell Azure-dator i samma virtuella nätverk. Och sedan kontrollera om Data källan/målet kan nås från Azure VM
      * Du hittar mer information om hur du använder vNet med SSIS-Integreringskörning på [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-the-following-error-message-could-not-create-a-managed-connection-manager"></a>Felmeddelande ”:`ADO NET Source has failed to acquire the connection '...' with the following error message: "Could not create a managed connection manager.`”

* Möjlig orsak & rekommenderad åtgärd:
  * ADO.NET-provider som används i paketet har inte installerats i SSIS-Integreringskörning. Du kan installera providern med hjälp av anpassad installation. Mer information om anpassad installation finns i [Anpassa installationsprogrammet för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

### <a name="error-message-the-connection--is-not-found"></a>Felmeddelande ”:`The connection '...' is not found`”

* Möjlig orsak & rekommenderad åtgärd:
  * Det här felet kan bero på att ett känt problem i den gamla versionen av SSMS. Om paketet innehåller en anpassad komponent (till exempel SSIS Azure Feature Pack eller 3 part komponenter) som inte är installerad på datorn där SSMS används för att göra distributionen, komponenten kommer att tas bort med hjälp av SSMS och orsakar felet. Uppgradera [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) till den senaste versionen som har problemet har åtgärdats.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Felmeddelande: ”Det finns inte tillräckligt med utrymme på disken”

* Möjlig orsak & rekommenderad åtgärd:
  * Detta fel innebär att den lokala disken används i SSIS Integration Runtime-noden. Kontrollera om ditt paket eller en anpassad installation förbrukar många disk blanksteg.
    * Om disken används av ditt paket, ska det frigöras när paketet körningen har slutförts.
    * Om disken används av anpassad installation, måste du stoppa SSIS Integration Runtime, ändra skriptet och starta SSIS Integration Runtime igen. Hela Azure-Blobbehållaren som du angav för anpassad installation ska kopieras över till SSIS IR-nod, så kontrollera om det finns några onödigt innehåll under behållaren.

### <a name="error-message-cannot-open-file-"></a>Felmeddelande: ”Det går inte att öppna filen”... ””

* Möjlig orsak & rekommenderad åtgärd:
  * Det här felet uppstår vid körning av paket inte kan hitta filen i lokal disk i SSIS-Integreringskörning.
    * Vi rekommenderar inte att du använder en absolut sökväg i paketet som körs i SSIS Integration Runtime. Använd aktuella körning arbetskatalogen (.) eller temp-mappen (% TEMP %) i stället.
    * Om det behövs att spara vissa filer på SSIS Integration Runtime-noder, vi rekommenderar för att förbereda filer via [anpassa installationen](how-to-configure-azure-ssis-ir-custom-setup.md). Alla filer i arbetskatalogen körning rensas när körningen är klar.
    * Ett annat alternativ är att använda Azure File i stället för att spara filen i SSIS Integration Runtime-noden. Mer information finns på [ https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Felmeddelande: ”Databas” SSISDB' har nått kvotgränsen ”

* Möjlig orsak & rekommenderad åtgärd:
  * SSISDB som skapats i Azure SQL- eller hanterad instans när du skapar SSIS Integration Runtime har uppnått sin kvot.
    * Överväg att öka DTU för din databas för att lösa problemet. Information finns i [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)
    * Kontrollera om ditt paket skulle generera många loggar. I så, fall kan Elastiskt jobb konfigureras för att rensa loggarna. Referera till [Rensa SSISDB loggar med Azure elastiska Databasjobb](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md) information.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Felmeddelande: ”Begäran gränsen för databasen är... och har uppnåtts ”.

* Möjlig orsak & rekommenderad åtgärd:
  * Om många paket körs parallellt i SSIS Integration Runtime kan kan det här felet inträffa eftersom begäran begränsningen av SSISDB uppnås. Överväg att öka DTC för din SSISDB att lösa problemet. Information finns i [https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Felmeddelande: ”SSIS-åtgärden misslyckades med oväntat Åtgärdsstatus:...”

* Möjlig orsak & rekommenderad åtgärd:
  * Felet orsakas vanligtvis av ett tillfälligt fel, så försök att köra paketkörning. Vi rekommenderar för att tillämpa återförsöksmönstret i SSIS-paket genom att följa steg:
    * Se till att dina SSIS-paket kan köra vid fel utan sidoeffekt (till exempel förlust av data, data dup...)
    * Konfigurera den **försök** och **återförsöksintervallet** av köra SSIS-paket aktivitet på fliken Allmänt ![ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
    * För ADO.NET och OLEDB källa/mål-komponent kan ConnectRetryCount och ConnectRetryInterval anges i Anslutningshanteraren i SSIS-paket eller SSIS-aktivitet

### <a name="error-message-there-is-no-active-worker"></a>Felmeddelande: ”Det finns ingen aktiv arbetare”.

* Möjlig orsak & rekommenderad åtgärd:
  * Detta fel innebär vanligtvis SSIS Integration Runtime är i en skadad status. Kontrollera Azure portal för status och information om felen: [https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Felmeddelande: ”Din integration runtime kan inte uppgraderas och ska upphöra att fungera, eftersom vi inte kan komma åt Azure Blob-behållare som du angav för anpassad installation”.

* Det här felet uppstår när SSIS Integration Runtime har inte åtkomst till lagring som konfigurerats för anpassad installation. Kontrollera att SAS-Uri som du angav är giltigt och inte har gått ut.

### <a name="package-takes-unexpected-long-time-to-execute"></a>Paketet tar oväntat lång tid att köra

* Möjlig orsak & rekommenderad åtgärd:
  * För många paket körningar har schemalagts på SSIS-Integreringskörning. Alla körningar kommer i så fall väntar i en kö för att köra.
    * Maximalt antal parallella körningar per IR = Nodantal * Max parallell körning per nod
    * Referera till [skapa Azure-SSIS Integration Runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md) att ange antalet noder och Max parallellkörning per nod.
  * SSIS Integration Runtime har stoppats eller i en skadad status. Kontrollera [Azure SSIS-integreringskörning](monitor-integration-runtime.md#azure-ssis-integration-runtime) att kontrollera status för SSIS-Integreringskörning och fel.
  * Vi rekommenderar att du ange tidsgränsen för om du är säker körning av paket ska slutföras i en viss tid: ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Sämre prestanda i körning av paket

* Möjlig orsak & rekommenderad åtgärd:

  * Kontrollera om SSIS Integration Runtime är i samma region som källa och mål.

  * Aktivera ”prestanda” loggningsnivån

      Du kan ställa in loggning för körning av paket till ”prestanda” för att samla in mer detaljerad varaktighetsinformation för varje komponent i körningen. Information finns på: [https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

  * Kontrollera IR-nod prestanda i IR-övervakaren sidan på Azure portal.
    * Så här övervakar du SSIS Integration Runtime: [Azure SSIS-integreringskörning](monitor-integration-runtime.md#azure-ssis-integration-runtime)
    * Historiken användning av CPU/minne för SSIS Integration Runtime är tillgänglig på mått på Data Factory i Azure-portalen ![övervaka mått för SSIS Integration Runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
