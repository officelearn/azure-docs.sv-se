---
title: Felsök paketkörning i SSIS-integreringskörningen
description: Den här artikeln innehåller felsökningsvägledning för SSIS-paketkörning i SSIS-integreringskörningen
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 1c2db107302e4851641ef430db61ec9b29ee151f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187485"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Felsök paketkörning i SSIS-integreringskörningen

Den här artikeln innehåller de vanligaste felen som kan hitta när du kör SSIS-paket (SQL Server Integration Services) i SSIS-integreringskörningen. Den beskriver de potentiella orsakerna och åtgärder för att lösa felen.

## <a name="where-to-find-logs-for-troubleshooting"></a>Här hittar du loggar för felsökning

Använd Azure Data Factory-portalen för att kontrollera utdata från SSIS-paketkörningsaktiviteten. Utdata innehåller körningsresultatet, felmeddelanden och åtgärds-ID. Mer information finns i [Övervaka pipelinen](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Använd SSIS-katalogen (SSISDB) för att kontrollera detaljloggarna för körningen. Mer information finns i [Övervaka paket som körs och andra åtgärder](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Vanliga fel, orsaker och lösningar

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Felmeddelande: "Timeout för anslutning har upphört att gälla" eller "Tjänsten har påträffat ett fel som bearbetar din begäran. Försök igen.”

Här är potentiella orsaker och rekommenderade åtgärder:
* Datakällan eller målet är överbelastat. Kontrollera belastningen på datakällan eller målet och se om den har tillräcklig kapacitet. Om du till exempel använde Azure SQL Database kan du överväga att skala upp om databasen sannolikt kommer att ta time out.
* Nätverket mellan SSIS-integreringskörningen och datakällan eller målet är instabilt, särskilt när anslutningen är mellanregion eller mellan lokala och Azure. Använd återförsöksmönstret i SSIS-paketet genom att följa dessa steg:
  * Se till att dina SSIS-paket kan köras igen vid fel utan biverkningar (till exempel dataförlust eller dataduplicering).
  * Konfigurera intervall **för återförsök** och **försök igen** för funktionen **Kör SSIS-paket** på fliken **Allmänt.** ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * För en ADO.NET- och OLE DB-käll- eller målkomponent anger du **ConnectRetryCount** och **ConnectRetryInterval** i Anslutningshanteraren i SSIS-paketet eller SSIS-aktiviteten.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Felmeddelande: "ADO NET Source has failed to acquire the connection '...'" med "Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning till SQL Server upprättas. Servern hittades inte eller var inte tillgänglig."

Det här problemet innebär vanligtvis att datakällan eller målet inte kan nås från SSIS Integration Runtime. Anledningen kan variera. Försök med följande åtgärder:
* Kontrollera att du skickar datakällan eller målnamnet/IP:n korrekt.
* Kontrollera att brandväggen är korrekt inställd.
* Kontrollera att det virtuella nätverket är korrekt konfigurerat om datakällan eller målet är lokalt:
  * Du kan kontrollera om problemet är från virtuell nätverkskonfiguration genom att etablera en Virtuell Azure-dator i samma virtuella nätverk. Kontrollera sedan om datakällan eller målet kan nås från den virtuella Azure-datorn.
  * Du kan hitta mer information om hur du använder ett virtuellt nätverk med en SSIS-integreringskörning i [Anslut en Azure-SSIS-integrationskörning till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Felmeddelande: "ADO NET Source has failed to acquire the connection '...'" med "Det gick inte att skapa en hanterad anslutningshanterare".

Den potentiella orsaken är att den ADO.NET provider som används i paketet inte är installerad i SSIS-integreringskörningen. Du kan installera providern med hjälp av en anpassad installation. Du hittar mer information om anpassad installation i [Anpassa inställningar för Azure-SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Felmeddelande: "Anslutningen '...' hittades inte"

Ett känt problem i äldre versioner av SQL Server Management Studio (SSMS) kan orsaka det här felet. Om paketet innehåller en anpassad komponent (till exempel SSIS Azure Feature Pack eller partnerkomponenter) som inte är installerad på den dator där SSMS används för att utföra distributionen, tar SSMS bort komponenten och orsakar felet. Uppgradera [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) till den senaste versionen där problemet är korrigerat.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Felmeddelande:"SSIS Executor exit code: -1073741819."

* Möjlig orsak och rekommenderad åtgärd:
  * Det här felet kan bero på begränsningen för Excel-källa och mål när flera Excel-källor eller -mål körs parallellt i flera trådar. Du kan lösa den här begränsningen genom att ändra Dina Excel-komponenter så att de körs i följd, eller separera dem i olika paket och utlösa dem via "Kör paketuppgift" med executeoutofprocess-egenskapen som true.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Felmeddelande: "Det finns inte tillräckligt med utrymme på disken"

Det här felet innebär att den lokala disken används i noden SSIS-integreringskörning. Kontrollera om paketet eller den anpassade installationen förbrukar mycket diskutrymme:
* Om disken förbrukas av paketet frigörs den när paketkörningen har slutförts.
* Om disken förbrukas av din anpassade konfiguration måste du stoppa SSIS-integreringskörningen, ändra skriptet och starta integreringskörningen igen. Hela Azure blob-behållaren som du har angett för anpassad konfiguration kopieras till SSIS-integrationskörningsnoden, så kontrollera om det finns något onödigt innehåll under den behållaren.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Felmeddelande: "Det gick inte att hämta resursen från huvudhanteraren. Microsoft.SqlServer.integrationServices.ScaleoutContract.Common.MasterResponseFailedException: Kod:300004. Beskrivning:Läsa in filen "***" misslyckades."

* Möjlig orsak och rekommenderad åtgärd:
  * Om SSIS-aktiviteten körs paket från filsystemet (paketfil eller projektfil), uppstår det här felet om projektet, paketet eller konfigurationsfilen inte är tillgänglig med den autentiseringsuppgifter för paketåtkomst som du angav i SSIS-aktiviteten
    * Om du använder Azure File:
      * Filsökvägen ska \\ \\ \<börja med\>filresurssökvägen\\\<för lagringskontot .file.core.windows.net filresurs\>
      * Domänen ska vara "Azure"
      * Användarnamnet ska \<vara lagringskontonamn\>
      * Lösenordet ska \<vara åtkomstnyckel för lagring\>
    * Om du använder lokal fil kontrollerar du om VNet, autentiseringsuppgifter för paketåtkomst och behörighet är korrekt konfigurerade så att din Azure-SSIS-integreringskörning kan komma åt din lokala filresursdelning

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Felmeddelande: "Filnamnet '...' som anges i anslutningen inte var giltig"

* Möjlig orsak och rekommenderad åtgärd:
  * Ett ogiltigt filnamn har angetts
  * Kontrollera att du använder FQDN (fullständigt kvalificerat domännamn) i stället för kort tid i anslutningshanteraren

### <a name="error-message-cannot-open-file-"></a>Felmeddelande: "Det går inte att öppna filen'...'"

Det här felet uppstår när paketkörning inte kan hitta en fil på den lokala disken i SSIS-integreringskörningen. Försök med följande åtgärder:
* Använd inte den absoluta sökvägen i paketet som körs i SSIS-integreringskörningen. Använd den aktuella körningsarbetskatalogen (.) eller tempmappen (%TEMP%) Istället.
* Om du behöver spara vissa filer på SSIS-integreringsnoder förbereder du filerna enligt beskrivningen i [Anpassa installationen](how-to-configure-azure-ssis-ir-custom-setup.md). Alla filer i arbetskatalogen rensas när körningen är klar.
* Använd Azure-filer i stället för att lagra filen i noden SSIS-integreringskörning. Mer information finns i [Använda Azure-filresurser](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Felmeddelande: "Databasen 'SSISDB' har nått sin storlekskvot"

En möjlig orsak är att SSISDB-databasen som skapats i Azure SQL-databasen, eller en hanterad instans när du skapar en SSIS Integration Runtime, har nått sin kvot. Försök med följande åtgärder:
* Överväg att öka antalet DTU:er för din databas. Du hittar mer information i [Resursbegränsningar för SQL Database för en Azure SQL Database-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Kontrollera om ditt paket skulle generera många loggar. I så fall kan du konfigurera ett elastiskt jobb så att dessa loggar rensas. Mer information finns i [Rensa SSISDB-loggar med Azure Elastic Database-jobb](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Felmeddelande: "Begäran gränsen för databasen är ... och har nåtts."

Om många paket körs parallellt i SSIS-integreringskörningen kan det här felet uppstå eftersom SSISDB har nått sin begärangräns. Överväg att öka DTC för SSISDB för att lösa problemet. Du hittar mer information i [Resursbegränsningar för SQL Database för en Azure SQL Database-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Felmeddelande: "SSIS-åtgärden misslyckades med oväntad driftstatus: ..."

Felet orsakas oftast av ett tillfälligt problem, så försök att köra paketkörningen igen. Använd återförsöksmönstret i SSIS-paketet genom att följa dessa steg:

* Se till att dina SSIS-paket kan köras igen vid fel utan biverkningar (till exempel dataförlust eller dataduplicering).
* Konfigurera intervall **för återförsök** och **försök igen** för funktionen **Kör SSIS-paket** på fliken **Allmänt.** ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* För en ADO.NET- och OLE DB-käll- eller målkomponent anger du **ConnectRetryCount** och **ConnectRetryInterval** i Anslutningshanteraren i SSIS-paketet eller SSIS-aktiviteten.

### <a name="error-message-there-is-no-active-worker"></a>Felmeddelande: "Det finns ingen aktiv arbetare."

Det här felet innebär vanligtvis att SSIS-integreringskörningen har en felaktig status. Kontrollera om det finns status och detaljerade fel i Azure-portalen. Mer information finns i [Azure-SSIS integrationskörning](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Felmeddelande: "Din integrationskörning kan inte uppgraderas och kommer så småningom att sluta fungera, eftersom vi inte kan komma åt Azure Blob-behållaren som du angav för anpassad installation."

Det här felet uppstår när SSIS-integreringskörningen inte kan komma åt lagringen som konfigurerats för anpassad installation. Kontrollera om sas-uri -uri (shared access signature) som du angav är giltig och inte har upphört att gälla.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Felmeddelande: "Microsoft OLE DB Provider for Analysis Services. "Hresult: 0x80004005 Beskrivning:" COM-fel: COM-fel: mscorlib; Undantag har tagits bort av målet om en åkallan"

En potentiell orsak är att användarnamnet eller lösenordet med Azure Multi-Factor Authentication aktiverat är konfigurerat för Azure Analysis Services-autentisering. Den här autentiseringen stöds inte i SSIS-integreringskörningen. Försök att använda ett tjänsthuvudnamn för Azure Analysis Services-autentisering:

1. Förbered ett tjänsthuvudnamn enligt beskrivningen i [Automation med tjänstens huvudnamn](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. I Anslutningshanteraren **konfigurerar du Använd ett visst användarnamn och lösenord**: ange **AppID** som användarnamn och **clientSecret** som lösenord.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Felmeddelande: "ADONET Source has failed to acquire the connection {GUID} with the following error message: Login failed for user 'NT AUTHORITY\ANONYMOUS LOGON' when using a managed identity

Kontrollera att du inte konfigurerar autentiseringsmetoden för Anslutningshanteraren som **Active Directory-lösenordsautentisering** när parametern *ConnectUsingManagedIdentity* är **True**. Du kan konfigurera den som **SQL-autentisering** i stället, vilket ignoreras om *ConnectUsingManagedIdentity* är inställt.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Felmeddelande: "0xC020801F at ..., OData Source [...]: Det går inte att hämta en hanterad anslutning från anslutningshanteraren för körning"

En potentiell orsak är att TLS (Transport Layer Security) inte är aktiverat i SSIS-integreringskörning som krävs av OData-källan. Du kan aktivera TLS i SSIS-integreringskörning med hjälp av Anpassa inställningar. Mer information finns på [Can't connect Project Online Odata from SSIS](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) and [Customize setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Felmeddelande: "Begär mellanlagringsaktivitet med operation guid ... misslyckades sedan fel: Det gick inte att skicka mellanlagringsåtgärd med felmeddelande: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Det gick inte att läsa in dataproxy."

Kontrollera att din Azure-SSIS-integreringskörning är konfigurerad med självvärdbaserad integreringskörning. Mer information finns på [Konfigurera självvärdförd IR som en proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Felmeddelande: "Status för mellanlagringsaktivitet: Misslyckades. Mellanlagring uppgift fel: ErrorCode: 2010, ErrorMessage: Den självvärderade Integration Runtime ... är offline"

Kontrollera att din självvärderade integrationskörning är installerad och startad. Mer information finns på [Skapa och konfigurera en självvärd integrationskörning](create-self-hosted-integration-runtime.md)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Felmeddelande: "Mellanlagringsuppgiftsfel: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "Error: The requested OLE DB provider ... inte är registrerad. Om 64-bitarsdrivrutinen inte är installerad kör du paketet i 32-bitarsläge..."

Kontrollera att motsvarande provider som används av OLE DB-kontakterna i paketet är korrekt installerade på självvärdförd integrationsdator. Mer information finns på [Konfigurera självvärdförd IR som proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Felmeddelande: "Mellanlagringsuppgiftsfel: ErrorCode: 2906, ErrorMessage: Package execution failed., Output: {"OperationErrorMessages": "Error: System.IO.FileLoadException: Det gick inte att läsa in filen eller sammansättningen 'Microsoft.WindowsAzure.Storage, Version=..., Kultur=neutralt, PublicKeyToken=31bf3856ad364e35' eller något av dess beroenden. Den lokaliserade sammansättningens manifestdefinition matchar inte sammansättningsreferensen." ..."

En potentiell orsak är att din självvärderade integrationskörning inte är installerad eller uppgraderad på rätt sätt. Föreslå att du hämtar och installerar om den senaste självvärderade integrationskörningen. Mer information finns på [Skapa och konfigurera en självvärd integrationskörning](create-self-hosted-integration-runtime.md#installation-best-practices)

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Felmeddelande: "En anslutning krävs när metadata begärs. Om du arbetar offline avmarkerar du Arbeta offline på SSIS-menyn för att aktivera anslutningen"

* Möjlig orsak och rekommenderad åtgärd:
  * Om det också finns ett varningsmeddelande "Komponenten stöder inte att använda anslutningshanteraren med ConnectByProxy-värdeinställningen true" i körningsloggen innebär det att en anslutningshanterare används på en komponent som inte har stöd för "ConnectByProxy" ännu. Komponenterna som stöds finns på [Konfigurera självvärdförd IR som proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * Körningsloggen finns i [SSMS-rapporten](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) eller i loggmappen som du angav i SSIS-paketkörningsaktivitet.
  * vNet kan också användas för att komma åt lokala data som ett alternativ. Mer information finns på [Anslut en Azure-SSIS-integrationskörning till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Felmeddelande: "Status för mellanlagringsaktivitet: Misslyckades. Mellanlagringsuppgiftsfel: ErrorCode: 2906, ErrorMessage: Det gick inte att köra paketet., Utdata: {"OperationErrorMessages": "SSIS Executor exit code: -1.\n", "LogLocation": "... \\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue": { "integrationRuntimeQueue": ... }}"

Kontrollera att Visual C++ runtime är installerad på självvärdförd integrationskörningsdator. Mer information finns på [Konfigurera självvärdförd IR som proxy för Azure-SSIS IR i ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Flera paketkörningar utlöses oväntat

* Möjlig orsak och rekommenderad åtgärd:
  * ADF-lagrad proceduraktivitet eller uppslagsaktivitet används för att utlösa SSIS-paketkörning. Kommandot t-sql kan utlösa tillfälliga problem och utlösa reprisen som skulle orsaka flera paketkörningar.
  * Använd KörSSISPackage-aktivitet i stället, vilket säkerställer att paketkörningen inte körs igen om inte antalet försök för användaren i aktivitet. Detaljer finns på[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * Förfina kommandot t-sql för att kunna köra igen genom att kontrollera om en körning redan har utlösts

### <a name="package-execution-takes-too-long"></a>Paketkörning tar för lång tid

Här är potentiella orsaker och rekommenderade åtgärder:

* För många paketkörningar har schemalagts på SSIS-integreringskörningen. Alla dessa avrättningar väntar i en kö för sin tur.
  * Bestäm det maximala med hjälp av den här formeln:

    Max antal parallella körningar per IR = antal nod * Max parallell körning per nod
  * Mer information om hur du anger antal noder och maximal parallell körning per nod finns [i Skapa en Azure-SSIS-integreringskörning i Azure Data Factory](create-azure-ssis-integration-runtime.md).
* SSIS-integreringskörningen stoppas eller har felstatus. Mer information om hur du kontrollerar statusen för SSIS-integreringskörning och fel finns i [Azure-SSIS-integreringskörning .](monitor-integration-runtime.md#azure-ssis-integration-runtime)

Vi rekommenderar också att du anger en ![timeout på fliken](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) **Allmänt:** Ange egenskaper på fliken Allmänt .

### <a name="poor-performance-in-package-execution"></a>Dålig prestanda vid paketkörning

Försök med följande åtgärder:

* Kontrollera att SSIS-integreringskörningen är i samma region som datakällan och målet.

* Ange loggningsnivån för paketkörning till **Prestanda** för att samla in varaktighetsinformation för varje komponent i körningen. Mer information finns i [Loggning av Integrationstjänster (SSIS).](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)

* Kontrollera IR-nodprestanda i Azure-portalen:
  * Information om hur du övervakar SSIS-integreringskörningen finns i [Azure-SSIS-integreringskörning](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Du kan hitta CPU/minneshistorik för SSIS-integreringen genom att visa måtten för datafabriken i Azure-portalen.
    ![Övervaka mått för SSIS-integreringskörningen](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
