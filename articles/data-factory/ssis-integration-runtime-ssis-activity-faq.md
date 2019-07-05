---
title: Felsöka körning av paket i SSIS-integreringskörning | Microsoft Docs
description: Den här artikeln innehåller felsökningsinformation för körning av SSIS-paket i SSIS-integreringskörning
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
ms.openlocfilehash: 68a5d5278e1181695695647cff187d4b95624b40
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537646"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Felsöka körning av paket i SSIS-integreringskörning

Den här artikeln innehåller de vanligaste felen som kan vara när du köra SQL Server Integration Services (SSIS)-paket från SSIS-integreringskörning. Det beskrivs möjliga orsaker och åtgärder för att lösa felen.

## <a name="where-to-find-logs-for-troubleshooting"></a>Var loggar för felsökning

Använda Azure Data Factory-portal för att kontrollera resultatet av körningsaktivitet för SSIS-paket. Utdata innehåller de Körningsresultat, felmeddelanden och åtgärds-ID. Mer information finns i [övervaka pipelinen](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Använda SSIS-katalogen (SSISDB) för att kontrollera detalj loggar för körningen. Mer information finns i [övervakaren som kör paket och andra åtgärder](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Vanliga fel, orsaker och lösningar

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Felmeddelande: ”Tidsgräns för anslutning upphört att gälla” eller ”tjänsten har ett fel uppstod när din begäran. Försök igen ”.

Här följer möjliga orsaker och rekommenderade åtgärder:
* Datakällan eller mål är överbelastad. Kontrollera belastningen på din datakälla eller mål och se om det finns tillräckligt med kapacitet. Om du använde Azure SQL Database kan du till exempel vara skala upp om databasen är troligt att tidsgränsen nåddes.
* Nätverket mellan SSIS-integreringskörning och data käll- och målservrar är instabil, särskilt när anslutningen är över flera regioner eller mellan lokala och Azure. Tillämpa återförsöksmönstret i SSIS-paket genom att följa dessa steg:
  * Kontrollera att dina SSIS-paket kan köra vid fel utan sidoeffekter (till exempel förlust av data eller duplicerade data).
  * Konfigurera **försök** och **återförsöksintervallet** av **köra SSIS-paket** aktivitet på den **Allmänt** fliken. ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * En ADO.NET- och OLE DB käll- eller målservrar komponent, ange **ConnectRetryCount** och **ConnectRetryInterval** i Anslutningshanteraren i SSIS-paket eller SSIS-aktivitet.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Felmeddelande: ”ADO NET källa kunde inte hämta anslutningen”... ”” med ”en nätverks- eller instans-specifika fel uppstod när en anslutning till SQLServer. Servern hittades inte eller var inte tillgänglig ”.

Det här problemet beror vanligen på datakällan eller målet kan inte nås från SSIS-integreringskörning. Anledningarna kan variera. Prova följande åtgärder:
* Kontrollera att du låter data käll- och målservrar servernamn/IP på rätt sätt.
* Kontrollera att brandväggen är korrekt.
* Kontrollera att det virtuella nätverket är korrekt konfigurerat om din datakälla eller mål är på plats:
  * Du kan kontrollera om problemet kommer från konfiguration av virtuellt nätverk genom att etablera en virtuell Azure-dator i samma virtuella nätverk. Kontrollera sedan om datakällan eller målet kan nås från den virtuella Azure-datorn.
  * Du hittar mer information om hur du använder ett virtuellt nätverk med en SSIS-integreringskörning i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Felmeddelande: ”ADO NET källa kunde inte hämta anslutningen”... ”” med ”det gick inte att skapa en hanterad anslutning manager”.

Möjlig orsak är att den ADO.NET-provider som används i paketet inte är installerat i SSIS-integreringskörning. Du kan installera providern med hjälp av en anpassad installation. Du hittar mer information om installationen av anpassad i [Anpassa installationsprogrammet för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Felmeddelande: ”Anslutningen”... ” finns inte ”

Felet kan orsakas av ett känt problem i äldre versioner av SQL Server Management Studio (SSMS). Om paketet innehåller en anpassad komponent (till exempel SSIS Azure Feature Pack eller partner komponenter) som inte är installerad på datorn där SSMS används för att göra distributionen kan ta bort komponenten SSMS och orsakar felet. Uppgradera [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) till den senaste versionen som har problemet har åtgärdats.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Felmeddelande: ”Det finns inte tillräckligt med utrymme på disken”

Detta fel innebär att den lokala disken används i SSIS integration runtime-noden. Kontrollera om ditt paket eller en anpassad installation förbrukar för mycket diskutrymme:
* Om disken används av ditt paket, ska det frigöras när paketet körningen har slutförts.
* Om disken används av din anpassade konfiguration, behöver för du att stoppa SSIS-integreringskörning ändra skriptet och starta integration runtime igen. Hela Azure blob-behållaren som du angav för anpassad installation ska kopieras till SSIS integration runtime-noden så kontrollera om det finns några onödigt innehåll under behållaren.

### <a name="error-message-cannot-open-file-"></a>Felmeddelande: ”Det går inte att öppna filen”... ””

Det här felet uppstår vid körning av paket inte kan hitta en fil i den lokala disken i SSIS-integreringskörning. Prova följande åtgärder:
* Använd inte den absoluta sökvägen i paketet som körs i SSIS-integreringskörning. Använd den aktuella arbetskatalogen från körningen (.) eller temp-mappen (% TEMP %) i stället.
* Om du vill spara vissa filer på SSIS integration runtime noder förbereda filerna enligt beskrivningen i [anpassa installationen](how-to-configure-azure-ssis-ir-custom-setup.md). Alla filer i arbetskatalogen rensas när körningen är klar.
* Använda Azure Files i stället för att spara filen i SSIS integration runtime-noden. Mer information finns i [Använd Azure-filresurser](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Felmeddelande: ”Databas” SSISDB' har nått kvotgränsen ”

En möjlig orsak är att SSIDB-databasen som skapats i Azure SQL-databas eller en hanterad instans när du skapar en SSIS-integreringskörning har uppnått sin kvot. Prova följande åtgärder:
* Överväg att öka DTU för din databas. Du hittar information i [SQL Database-resursgränser för en Azure SQL Database-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Kontrollera om ditt paket skulle generera många loggar. I så fall kan du konfigurera ett Elastiskt jobb att rensa loggarna. Mer information finns i [Rensa SSISDB loggar med Azure elastiska databasjobb](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Felmeddelande: ”Begäran gränsen för databasen är... och har uppnåtts ”.

Om många paket körs parallellt i SSIS-integreringskörning detta fel kan inträffa eftersom SSISDB har nått sin begärandegräns för. Överväg att öka DTC SSISDB för att lösa problemet. Du hittar information i [SQL Database-resursgränser för en Azure SQL Database-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Felmeddelande: ”SSIS-åtgärden misslyckades med oväntat Åtgärdsstatus:...”

Felet orsakas vanligtvis av ett tillfälligt problem, så försök att köra paketkörning. Tillämpa återförsöksmönstret i SSIS-paket genom att följa dessa steg:

* Kontrollera att dina SSIS-paket kan köra vid fel utan sidoeffekter (till exempel förlust av data eller duplicerade data).
* Konfigurera **försök** och **återförsöksintervallet** av **köra SSIS-paket** aktivitet på den **Allmänt** fliken. ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* En ADO.NET- och OLE DB käll- eller målservrar komponent, ange **ConnectRetryCount** och **ConnectRetryInterval** i Anslutningshanteraren i SSIS-paket eller SSIS-aktivitet.

### <a name="error-message-there-is-no-active-worker"></a>Felmeddelande: ”Det finns ingen aktiv arbetare”.

Det här felet innebär oftast SSIS-integreringskörning har en skadad status. Kontrollera Azure portal för status och detaljerade fel. Mer information finns i [Azure SSIS-integreringskörning](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Felmeddelande: ”Din integration runtime kan inte uppgraderas och ska upphöra att fungera, eftersom vi inte kan komma åt Azure Blob-behållare som du angav för anpassad installation”.

Det här felet uppstår när SSIS-integreringskörning har inte åtkomst till lagring som konfigurerats för anpassad installation. Kontrollera att signaturen för delad åtkomst (SAS) URI som du angav är giltigt och inte har gått ut.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Felmeddelande: ”Microsoft OLE DB Provider för Analysis Services. 'Hresult: 0x80004005 beskrivning ”: COM-fel: COM-fel: mscorlib; Undantag har inträffat i målet för en ”

En möjlig orsak är att användarnamnet eller lösenordet med Azure Multi-Factor Authentication aktiverat är konfigurerad för Azure Analysis Services-autentisering. Den här autentiseringen stöds inte i SSIS-integreringskörning. Försök att använda ett huvudnamn för tjänsten för Azure Analysis Services-autentisering:
1. Förbereda ett huvudnamn för tjänsten enligt beskrivningen i [automatisering med tjänstens huvudnamn](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. Konfigurera i Anslutningshanteraren **använder ett specifikt användarnamn och lösenord**: Ange **AppID** som användarnamn och **clientSecret** som lösenord.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Felmeddelande: ”ADONET källa har det gick inte att hämta {GUID}-anslutning med följande felmeddelande visas: Inloggningen av användaren ”NT AUTHORITY\\ANONYMOUS LOGON” misslyckades ”när du använder en hanterad identitet

Kontrollera att du inte konfigurera autentiseringsmetoden för Anslutningshanteraren som **Active Directory-lösenordsautentisering** när parametern *ConnectUsingManagedIdentity* är **SANT** . Du kan konfigurera den som **SQL-autentisering** i stället som ignoreras om *ConnectUsingManagedIdentity* har angetts.

### <a name="package-execution-takes-too-long"></a>Körning av paket tar för lång tid

Här följer möjliga orsaker och rekommenderade åtgärder:
* För många paket körningar har schemalagts på SSIS-integreringskörning. Alla körningar ska vänta i en kö sina aktivera.
  * Avgör högsta med hjälp av den här formeln: 
    
    Maximalt antal parallella körningar per IR = Nodantal * Max parallell körning per nod
  * Läs hur du ställer in antalet noder och högsta parallellkörning per nod i [skapa en Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md).
* SSIS-integreringskörning har stoppats eller har en skadad status. Läs hur du kontrollerar status för SSIS integration runtime och fel i [Azure SSIS-integreringskörning](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Vi rekommenderar också att du ställer in en tidsgräns på den **Allmänt** fliken: ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Sämre prestanda i körning av paket

Prova följande åtgärder:

* Kontrollera att SSIS-integreringskörning är i samma region som källa och mål.

* Ange loggningsnivån för körning av paket till **prestanda** att samla in varaktighetsinformation för varje komponent i körningen. Mer information finns i [Integration Services (SSIS) loggning](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Kontrollera prestanda för IR-nod i Azure portal:
  * Information om hur du övervakar SSIS-integreringskörning finns i [Azure SSIS-integreringskörning](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Du hittar processor/minne historik för SSIS integration runtime genom att visa mått för data factory i Azure-portalen.
    ![Övervaka mått för SSIS integration runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
