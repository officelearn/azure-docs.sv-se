---
title: Felsöka paket körning i SSIS-integration runtime | Microsoft Docs
description: Den här artikeln innehåller fel söknings vägledning för körning av SSIS-paket i SSIS integration runtime
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
ms.openlocfilehash: a7ad0f3be754029c654b04d19750aab7bbcd210d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933641"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Felsöka paket körning i SSIS integration runtime

Den här artikeln innehåller de vanligaste fel som kan uppstå när du kör SQL Server Integration Services-paket (SSIS) i SSIS integration Runtime. Det beskriver möjliga orsaker och åtgärder för att lösa felen.

## <a name="where-to-find-logs-for-troubleshooting"></a>Här hittar du loggar för fel sökning

Använd Azure Data Factory Portal för att kontrol lera utdata från körnings aktiviteten för SSIS-paketet. I utdata ingår körnings resultat, fel meddelanden och åtgärds-ID. Mer information finns i [övervaka pipelinen](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Använd SSIS-katalogen (SSISDB) för att kontrol lera informations loggarna för körningen. Mer information finns i [övervaka paket som körs och andra åtgärder](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Vanliga fel, orsaker och lösningar

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Felmeddelande: "Tids gräns för anslutning upphört att gälla" eller "tjänsten har påträffat ett fel vid bearbetning av begäran. Försök igen. "

Här är möjliga orsaker och rekommenderade åtgärder:
* Data källan eller målet är överbelastat. Kontrol lera belastningen på data källan eller målet och se om det finns tillräckligt med kapacitet. Om du t. ex. använde Azure SQL Database kan du skala upp om databasen troligen är tids gräns.
* Nätverket mellan SSIS-integrerings körningen och data källan eller målet är instabilt, särskilt när anslutningen är mellan olika regioner eller mellan lokala platser och Azure. Använd mönstret för återförsök i SSIS-paketet genom att följa dessa steg:
  * Se till att dina SSIS-paket kan köras igen vid fel utan sido effekter (till exempel data förlust eller dataduplicering).
  * Konfigurera återförsök och ÅTERFÖRSÖKSINTERVALL för aktiviteten **kör SSIS-paket** på fliken **Allmänt** . ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * För en ADO.NET och OLE DB käll-eller mål komponent anger du **ConnectRetryCount** och **ConnectRetryInterval** i anslutnings hanteraren i SSIS-paketet eller SSIS-aktiviteten.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Felmeddelande: "ADO NET source kunde inte hämta anslutningen"... "" med "ett nätverksrelaterade eller instans-relaterat fel uppstod när en anslutning upprättades till SQL Server. Det gick inte att hitta servern eller så var den inte tillgänglig. "

Det här problemet innebär vanligtvis att datakällan eller målet inte kan nås från SSIS Integration Runtime. Anledningen kan variera. Försök med följande åtgärder:
* Kontrol lera att du har överför data källan eller mål namnet/IP-adressen korrekt.
* Kontrol lera att brand väggen är korrekt inställd.
* Kontrol lera att det virtuella nätverket är korrekt konfigurerat om data källan eller målet är lokalt:
  * Du kan kontrol lera om problemet beror på konfigurationen av virtuella nätverk genom att tillhandahålla en virtuell Azure-dator i samma virtuella nätverk. Kontrol lera sedan om data källan eller målet kan nås från den virtuella Azure-datorn.
  * Du hittar mer information om hur du använder ett virtuellt nätverk med en SSIS integration runtime i delta i en [Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Felmeddelande: "ADO NET source kunde inte hämta anslutningen"... "" med "Det gick inte att skapa en hanterad anslutnings hanterare".

Den potentiella orsaken är att ADO.NET-providern som används i paketet inte är installerad i SSIS integration Runtime. Du kan installera providern med hjälp av en anpassad installation. Du hittar mer information om anpassade inställningar i [Anpassa installations programmet för Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Felmeddelande: "Anslutningen"... " hittades inte "

Ett känt problem i äldre versioner av SQL Server Management Studio (SSMS) kan orsaka det här felet. Om paketet innehåller en anpassad komponent (till exempel SSIS Azure Feature Pack eller partnerkomponenter) som inte är installerad på den dator där SSMS används för att utföra distributionen, tar SSMS bort komponenten och orsakar felet. Uppgradera [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) till den senaste versionen som har problemet åtgärdat.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Fel meddelande: "SSIS utförar avslutning Code:-1073741819".

* Möjlig orsak & Rekommenderad åtgärd:
  * Det här felet kan bero på begränsningen för Excel-källa och-mål när flera Excel-källor eller-mål körs parallellt i flera trådar. Du kan lösa den här begränsningen genom att ändra dina Excel-komponenter så att de körs i följd, eller separera dem till olika paket och utlösa genom att köra paket uppgift med egenskapen ExecuteOutOfProcess inställd på sant.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Felmeddelande: "Det finns inte tillräckligt med utrymme på disken"

Det här felet innebär att den lokala disken används i SSIS-noden för integration Runtime. Kontrol lera om paketet eller den anpassade installationen tar mycket disk utrymme:
* Om disken används av ditt paket frigörs den när paket körningen har slutförts.
* Om disken används av den anpassade installationen måste du stoppa SSIS integration runtime, ändra skriptet och starta integrerings körningen igen. Hela Azure Blob-behållaren som du har angett för anpassad installation kopieras till SSIS-noden för integration runtime, så kontrol lera om det finns onödigt innehåll under behållaren.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Felmeddelande: "Det gick inte att hämta resursen från huvud servern. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Kod: 300004. Beskrivning: det gick inte att läsa in filen "* * *". "

* Möjlig orsak & Rekommenderad åtgärd:
  * Om SSIS-aktiviteten kör ett paket från fil systemet (paketfil eller projekt filen) uppstår det här felet om projektet, paketet eller konfigurations filen inte är tillgängliga med de autentiseringsuppgifter för paket åtkomst som du angav i SSIS-aktiviteten
    * Om du använder Azure-fil:
      * Fil Sök vägen ska börja med \\ \\ \<lagrings konto\>namnet.\\File.Core.Windows.net\<fil resurs Sök väg\>
      * Domänen ska vara "Azure"
      * Användar namnet måste vara \<ett lagrings konto namn\>
      * Lösen ordet ska vara \<lagrings åtkomst nyckel\>
    * Om du använder en lokal fil kontrollerar du om VNet, paket åtkomst behörighet och behörighet har kon figurer ATS korrekt så att din Azure-SSIS integration runtime kan komma åt din lokala fil resurs

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Felmeddelande: "Fil namnet"... " angavs i anslutningen var inte giltigt.

* Möjlig orsak & Rekommenderad åtgärd:
  * Ett ogiltigt fil namn har angetts
  * Kontrol lera att du använder FQDN (fullständigt kvalificerat domän namn) i stället för kort tid i anslutnings hanteraren

### <a name="error-message-cannot-open-file-"></a>Felmeddelande: Det går inte att öppna filen...

Det här felet uppstår när paket körningen inte kan hitta en fil på den lokala disken i SSIS integration Runtime. Försök med följande åtgärder:
* Använd inte den absoluta sökvägen i paketet som körs i SSIS integration Runtime. Använd den aktuella körnings arbets katalogen (.) eller Temp-mappen (% TEMP%) i stället.
* Om du behöver spara några filer på SSIS-noderna för integration runtime förbereder du filerna enligt beskrivningen i [Anpassa installationen](how-to-configure-azure-ssis-ir-custom-setup.md). Alla filer i arbets katalogen kommer att rensas när körningen har slutförts.
* Använd Azure Files i stället för att lagra filen i SSIS integration runtime-noden. Mer information finns i [använda Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)-filresurser.

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Felmeddelande: "Databasen" SSISDB "har nått sin storleks kvot"

En möjlig orsak är att SSISDB-databasen som skapats i Azure SQL-databasen, eller en hanterad instans när du skapar en SSIS Integration Runtime, har nått sin kvot. Försök med följande åtgärder:
* Överväg att öka antalet DTU:er för din databas. Du hittar mer information i [Resursbegränsningar för SQL Database för en Azure SQL Database-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Kontrollera om ditt paket skulle generera många loggar. I så fall kan du konfigurera ett elastiskt jobb så att dessa loggar rensas. Mer information finns i [Rensa SSISDB-loggar med Azure Elastic Database-jobb](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Felmeddelande: "Begränsningen för begäran för databasen är... och har nåtts. "

Om många paket körs parallellt i SSIS-integrerings körningen kan det här felet inträffa eftersom SSISDB har nått sin gräns för begäran. Överväg att öka DTC-SSISDB för att lösa problemet. Du hittar mer information i [Resursbegränsningar för SQL Database för en Azure SQL Database-server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Felmeddelande: "SSIS-åtgärden misslyckades med oväntad åtgärds status:..."

Felet orsakas oftast av ett tillfälligt problem, så försök att köra paket körningen igen. Använd mönstret för återförsök i SSIS-paketet genom att följa dessa steg:

* Se till att dina SSIS-paket kan köras igen vid fel utan sido effekter (till exempel data förlust eller dataduplicering).
* Konfigurera återförsök och ÅTERFÖRSÖKSINTERVALL för aktiviteten **kör SSIS-paket** på fliken **Allmänt** . ![Ange egenskaper på fliken Allmänt](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* För en ADO.NET och OLE DB käll-eller mål komponent anger du **ConnectRetryCount** och **ConnectRetryInterval** i anslutnings hanteraren i SSIS-paketet eller SSIS-aktiviteten.

### <a name="error-message-there-is-no-active-worker"></a>Felmeddelande: "Det finns ingen aktiv arbets rutin."

Det här felet innebär vanligt vis att SSIS integration Runtime har en felaktig status. Kontrol lera Azure Portal för status och detaljerade fel. Mer information finns i [Azure-SSIS integration runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Felmeddelande: "Det går inte att uppgradera integrerings körningen och upphör att fungera, eftersom det inte går att komma åt Azure Blob-behållaren som du angav för anpassad installation."

Felet uppstår när SSIS-integrerings körningen inte kan komma åt lagrings utrymmet som kon figurer ATS för anpassad installation. Kontrol lera om den angivna URL: en för signaturen för delad åtkomst (SAS) är giltig och inte har gått ut.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Felmeddelande: "Microsoft OLE DB Provider för Analysis Services. HRESULT Beskrivning av 0x80004005 visas: COM-fel: COM-fel: mscorlib; Ett undantag har utlösts av målet för ett anrop "

En möjlig orsak är att användar namnet eller lösen ordet med Azure Multi-Factor Authentication har Aktiver ATS för Azure Analysis Services autentisering. Den här autentiseringen stöds inte i integrerings körningen för SSIS. Försök att använda ett huvud namn för tjänsten för Azure Analysis Services autentisering:
1. Förbered ett huvud namn för tjänsten enligt beskrivningen i [Automation med tjänstens huvud namn](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. I anslutnings hanteraren konfigurerar du **Använd ett särskilt användar namn och lösen ord**: ange **AppID** som användar namn och **clientSecret** som lösen ord.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Felmeddelande: "ADONET-källan kunde inte hämta anslutningen {GUID} med följande fel meddelande: Inloggningen misslyckades för användarens NT AUTHORITY\ANONYMOUS-inloggning när en hanterad identitet används

Se till att du inte konfigurerar autentiseringsmetoden för anslutnings hanteraren som **Active Directory** lösenordsautentisering när parametern *ConnectUsingManagedIdentity* är **True**. Du kan konfigurera den som **SQL-autentisering** i stället, vilket ignoreras om *ConnectUsingManagedIdentity* har angetts.

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Flera paket körningar utlöses oväntade

* Möjlig orsak & Rekommenderad åtgärd:
  * ADF-lagrad procedur aktivitet används för att utlösa körning av SSIS-paket. T-SQL-kommandot kan nå ett tillfälligt problem och utlösa omkörningen vilket skulle orsaka flera paket körningar.
  * Använd ExecuteSSISPackage-aktivitet i stället för att se till att paket körningen inte körs igen om inte antalet nya försök i aktiviteten. Information finns på[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

### <a name="package-execution-takes-too-long"></a>Paket körningen tar för lång tid

Här är möjliga orsaker och rekommenderade åtgärder:

* För många paket körningar har schemalagts i SSIS integration Runtime. Alla dessa körningar väntar i en kö för sin tur.
  * Fastställ Max värdet genom att använda den här formeln:

    Maximalt antal parallella körningar per IR = antal noder * maximal parallell körning per nod
  * Information om hur du ställer in antal noder och maximal parallell körning per nod finns i [skapa en Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Integrerings körningen för SSIS har stoppats eller är i fel tillstånd. Information om hur du kontrollerar SSIS för integration runtime finns i [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Vi rekommenderar också att du anger en tids gräns på fliken **Allmänt** : ![Ange egenskaper på fliken](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)allmänt.

### <a name="poor-performance-in-package-execution"></a>Dåliga prestanda vid paket körning

Försök med följande åtgärder:

* Se till att integration runtime för SSIS finns i samma region som data källan och målet.

* Ange loggnings nivå för paket körning till **prestanda** för att samla in varaktighets information för varje komponent i körningen. Mer information finns i [loggning av integrerings tjänster (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Kontrol lera prestanda för IR-nod i Azure Portal:
  * Information om hur du övervakar SSIS integration runtime finns i [Azure-SSIS integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Du hittar processor-/minnes historik för SSIS integration runtime genom att visa måtten för data fabriken i Azure Portal.
    ![Övervaka mått för integrerings körningen i SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
