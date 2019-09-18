---
title: Felsöka Azure Data Factory | Microsoft Docs
description: Lär dig hur du felsöker externa kontroll aktiviteter i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 45aa1354f6009d5eccd48f85f993bae8949139e3
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058979"
---
# <a name="troubleshoot-azure-data-factory"></a>Felsöka Azure Data Factory

Den här artikeln utforskar vanliga fel söknings metoder för externa kontroll aktiviteter i Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Anslutnings-och kopierings aktivitet

För anslutnings problem, t. ex. fel vid kopiering av aktivitet, se [fel sökning Azure Data Factory anslutningar](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Felkod:  3200

- **Meddelande**: Fel 403.

- **Orsak**:`The Databricks access token has expired.`

- **Rekommendation**: Som standard är Azure Databricks åtkomsttoken giltig i 90 dagar. Skapa en ny token och uppdatera den länkade tjänsten.


### <a name="error-code--3201"></a>Felkod:  3201

- **Meddelande**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Orsak**:`Bad authoring: Notebook path not specified correctly.`

- **Rekommendation**: Ange antecknings bokens sökväg i Databricks-aktiviteten.

<br/>

- **Meddelande**:`Cluster   ... does not exist.`

- **Orsak**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Rekommendation**: Kontrol lera att Databricks-klustret finns.

<br/>

- **Meddelande**:`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: Ange antingen absoluta sökvägar för arbets ytans adresserings scheman eller `dbfs:/folder/subfolder/foo.py` för filer som lagras i Databricks-filsystem.

<br/>

- **Meddelande**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: Verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Meddelande**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: Verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Meddelande**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: Se fel meddelandet. 

<br/>

### <a name="error-code--3202"></a>Felkod:  3202

- **Meddelande**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Orsak**:`Too many Databricks runs in an hour.`

- **Rekommendation**: Kontrol lera alla pipeliner som använder den här Databricks-arbetsytan för att skapa takten för jobbet.  Om pipelines startade för många Databricks-körningar i mängd, migrerar du några pipelines till en ny arbets yta.

<br/>

- **Meddelande**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Orsak**:`Authoring error: No value provided for the parameter.`

- **Rekommendation**: Kontrol lera pipeline-JSON och se till att alla parametrar i baseParameters Notebook anger ett värde som inte är tomt.

<br/>

- **Meddelande**: `User: `SimpleUserContext {userId =..., name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Orsak**: Användaren som genererade åtkomsttoken får inte åtkomst till det Databricks-kluster som anges i den länkade tjänsten.

- **Rekommendation**: Se till att användaren har de behörigheter som krävs på arbets ytan.


### <a name="error-code--3203"></a>Felkod:  3203

- **Meddelande**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Orsak**: Klustret avslutades. För interaktiva kluster kan detta vara ett tävlings tillstånd.

- **Rekommendation**: Det bästa sättet att undvika det här felet är att använda jobb kluster.


### <a name="error-code--3204"></a>Felkod:  3204

- **Meddelande**:`Job execution failed.`

- **Orsak**:  Fel meddelanden indikerar olika problem, till exempel ett oväntat kluster tillstånd eller en speciell aktivitet. Oftast visas inget fel meddelande alls. 

- **Rekommendation**: Gäller inte


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Följande tabell gäller för U-SQL.


### <a name="error-code--2709"></a>Felkod:  2709

- **Meddelande**:`The access token is from the wrong tenant.`

- **Orsak**:  Felaktig Azure Active Directory-klient (Azure AD).

- **Rekommendation**: Felaktig Azure Active Directory-klient (Azure AD).

<br/>

- **Meddelande**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Orsak**:  Felet orsakas av begränsning av Data Lake Analytics.

- **Rekommendation**: Minska antalet jobb som skickats till Data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics.

<br/>

- **Meddelande**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Orsak**:  Felet orsakas av begränsning av Data Lake Analytics. 

- **Rekommendation**: Minska antalet jobb som skickats till Data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics.


### <a name="error-code--2705"></a>Felkod:  2705

- **Meddelande**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Orsak**:  Tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: Kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för Data Lake Analytics jobb har åtkomst till Data Lake Analytics-kontot och standard-Data Lake Storage-instansen från rotmappen.


### <a name="error-code--2711"></a>Felkod:  2711

- **Meddelande**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Orsak**:  Tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: Kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för Data Lake Analytics jobb har åtkomst till Data Lake Analytics-kontot och standard-Data Lake Storage-instansen från rotmappen.

<br/>

- **Meddelande**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Orsak**:  Sökvägen till U-SQL-filen är felaktig eller också har autentiseringsuppgifterna för den länkade tjänsten inte åtkomst.

- **Rekommendation**: Verifiera sökvägen och autentiseringsuppgifterna som anges i den länkade tjänsten.


### <a name="error-code--2704"></a>Felkod:  2704

- **Meddelande**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Orsak**:  Tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: Kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för Data Lake Analytics jobb har åtkomst till Data Lake Analytics-kontot och standard-Data Lake Storage-instansen från rotmappen.


### <a name="error-code--2707"></a>Felkod:  2707

- **Meddelande**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Orsak**:  Det Data Lake Analytics kontot i den länkade tjänsten är felaktigt.

- **Rekommendation**: Kontrol lera att rätt konto har angetts.


### <a name="error-code--2703"></a>Felkod:  2703

- **Meddelande**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Orsak**:  Felet är från Data Lake Analytics. 

- **Rekommendation**: Ett fel som liknar exemplet innebär att jobbet skickades till Data Lake Analytics och skriptet det misslyckades. Undersök i Data Lake Analytics. I portalen går du till Data Lake Analytics kontot och letar efter jobbet med hjälp av Data Factory aktivitetens körnings-ID (inte pipelinens körnings-ID). Jobbet innehåller mer information om felet och hjälper dig att felsöka. Om upplösningen inte är klar kontaktar du Data Lake Analytics support-teamet och anger jobb-URL: en, som innehåller ditt konto namn och jobb-ID.



## <a name="azure-functions"></a>Azure functions

### <a name="error-code--3602"></a>Felkod:  3602

- **Meddelande**:`Invalid HttpMethod: {method}.`

- **Orsak**: Http-metoden som anges i aktivitetens nytto Last stöds inte av Azure Function-aktivitet. 

- **Rekommendation**: De http-metoder som stöds är placera, posta, Hämta, ta bort, alternativ, huvud och spåra.


### <a name="error-code--3603"></a>Felkod:  3603

- **Meddelande**:`Response content is not a valid JObject.`

- **Orsak**: Azure-funktionen som anropades returnerade inte en JSON-nyttolast i svaret. Azure Function-aktivitet i Data Factory stöder endast JSON-svars innehåll. 

- **Rekommendation**: Uppdatera Azure-funktionen för att returnera en giltig JSON-nyttolast. C# En funktion kan `(ActionResult)new<OkObjectResult("{`till exempel returnera \"ID\":\"123.\"`}");`


### <a name="error-code--3606"></a>Felkod:  3606

- **Meddelande**:`Azure function activity missing function key.`

- **Orsak**: Definitionen av Azure Function-aktiviteten har inte slutförts. 

- **Rekommendation**: Kontrol lera AzureFunction-aktivitetens JSON-definition har egenskapen med namnet ' functionKey '.


### <a name="error-code--3607"></a>Felkod:  3607

- **Meddelande**:`Azure function activity missing function name.`

- **Orsak**: Definitionen av Azure Function-aktiviteten har inte slutförts. 

- **Rekommendation**: Kontrol lera att JSON-definitionen för inAzureFunction aktivitet har en egenskap med namnet functionName.


### <a name="error-code--3608"></a>Felkod:  3608

- **Meddelande**:`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Orsak**: Azure Function-information i aktivitets definitionen kan vara felaktig. 

- **Rekommendation**: Åtgärda Azure Function-informationen och försök igen.


### <a name="error-code--3609"></a>Felkod:  3609

- **Meddelande**:`Azure function activity missing functionAppUrl.` 

- **Orsak**: Definitionen av Azure Function-aktiviteten har inte slutförts. 

- **Rekommendation**: Kontrol lera AzureFunction-aktivitetens JSON-definition har egenskapen med namnet ' functionAppUrl '.


### <a name="error-code--3610"></a>Felkod:  3610

- **Meddelande**:`There was an error while calling endpoint.`

- **Orsak**: Funktions webb adressen kan vara felaktig.

- **Rekommendation**: Kontrol lera att värdet för ' functionAppUrl ' i aktivitets-JSON är korrekt och försök igen.


### <a name="error-code--3611"></a>Felkod:  3611

- **Meddelande**:`Azure function activity missing Method in JSON.` 

- **Orsak**: Definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: Kontrol lera att JSON-definitionen för inAzureFunction aktivitet har egenskapen med namnet ' Method '.


### <a name="error-code--3612"></a>Felkod:  3612

- **Meddelande**:`Azure function activity missing LinkedService definition in JSON.`

- **Orsak**: Definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: Kontrol lera att det finns länkad tjänst information i indata-AzureFunction Activity JSON-definitionen.



## <a name="custom"></a>Anpassat

Följande tabell gäller för Azure Batch.


### <a name="error-code--2500"></a>Felkod:  2500

- **Meddelande**:`Hit unexpected exception and execution failed.`

- **Orsak**:`Can't launch command, or the program returned an error code.`

- **Rekommendation**:  Se till att den körbara filen finns. Om programmet har startats kontrollerar du att *STDOUT. txt* och *stderr. txt* överfördes till lagrings kontot. Det är en bra idé att generera Copious-loggar i koden för fel sökning.


### <a name="error-code--2501"></a>Felkod:  2501

- **Meddelande**:`Cannot access user batch account; please check batch account settings.`

- **Orsak**: Felaktig nyckel för batch-åtkomst eller poolnamn.

- **Rekommendation**: Verifiera poolens namn och åtkomst nyckeln för batch i den länkade tjänsten.


### <a name="error-code--2504"></a>Felkod:  2504

- **Meddelande**:`Operation returned an invalid status code 'BadRequest'.` 

- **Orsak**: För många filer i folderPath för den anpassade aktiviteten. Den totala storleken på resourceFiles får inte vara mer än 32 768 tecken.

- **Rekommendation**: Ta bort onödiga filer. Eller Lägg till ett zippa-kommando för att extrahera dem. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Felkod:  2505

- **Meddelande**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Orsak**: Anpassade aktiviteter stöder bara lagrings konton som använder en åtkomst nyckel.

- **Rekommendation**: Se fel beskrivningen.


### <a name="error-code--2507"></a>Felkod:  2507

- **Meddelande**:`The folder path does not exist or is empty: ....`

- **Orsak**: Det finns inga filer i lagrings kontot på den angivna sökvägen.

- **Rekommendation**: Mappsökvägen måste innehålla de körbara filer som du vill köra.


### <a name="error-code--2508"></a>Felkod:  2508

- **Meddelande**:`There are duplicate files in the resource folder.`

- **Orsak**: Flera filer med samma namn finns i olika undermappar i folderPath.

- **Rekommendation**: Mappstruktur för anpassade aktiviteter under folderPath.  Om du behöver bevara mappstrukturen zip-filerna och extrahera dem i Azure Batch med hjälp av ett zippa-kommando. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Felkod:  2509

- **Meddelande**:`Batch   url ... is invalid; it must be in Uri format.` 

- **Orsak**: Batch-URL: er måste likna`https://mybatchaccount.eastus.batch.azure.com`

- **Rekommendation**: Se fel beskrivningen.


### <a name="error-code--2510"></a>Felkod:  2510

- **Meddelande**:`An   error occurred while sending the request.`

- **Orsak**: Batch-URL: en är ogiltig. 

- **Rekommendation**: Verifiera batch-URL: en.


## <a name="hdinsight"></a>HDInsight

Följande tabell gäller Spark, Hive, MapReduce, gris och Hadoop streaming.


### <a name="error-code--2300"></a>Felkod:  2 300

- **Meddelande**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Orsak**: Den angivna kluster-URI: n är ogiltig. 

- **Rekommendation**:  Kontrol lera att klustret inte har tagits bort och att angiven URI är korrekt. När du öppnar URI i en webbläsare bör du se Ambari-ANVÄNDARGRÄNSSNITTET. Om klustret finns i ett virtuellt nätverk ska URI: n vara den privata URI: n. Öppna den genom att använda en virtuell dator som är en del av samma virtuella nätverk. Mer information finns i [direkt ansluta till Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Meddelande**:`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Orsak**: Tids gränsen för jobb överföringen uppnåddes. 

- **Rekommendation**: Problemet kan vara antingen allmän HDInsight-anslutning eller nätverks anslutning. Bekräfta först att HDInsight Ambari-ANVÄNDARGRÄNSSNITTET är tillgängligt från valfri webbläsare. Bekräfta att autentiseringsuppgifterna fortfarande är giltiga. Om du använder en lokal integrerad Runtime (IR), se till att göra detta från den virtuella datorn eller datorn där IR för egen värd är installerad. Försök sedan att skicka jobbet från Data Factory igen. Om det fortfarande inte går att kontakta Data Factory-teamet för support.


- **Meddelande**:`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Orsak**: Autentiseringsuppgifterna för HDInsight är felaktiga eller har upphört att gälla.

- **Rekommendation**: Korrigera autentiseringsuppgifterna och distribuera om den länkade tjänsten. Kontrol lera först att autentiseringsuppgifterna fungerar på HDInsight genom att öppna kluster-URI: n i valfri webbläsare och försök logga in. Om autentiseringsuppgifterna inte fungerar kan du återställa dem från Azure Portal.

<br/>

- **Meddelande**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Orsak**: Felet är från HDInsight.

- **Rekommendation**: Det här felet är från HDInsight-klustret. Mer information finns i [AMBARI UI 502-fel](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502-fel vid anslutning till Spark Thrift-Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), 502-fel vid [anslutning till Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)och [fel sökning av felaktiga Gateway-fel i Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Meddelande**:`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Orsak**: För många jobb skickas till HDInsight på samma tid.

- **Rekommendation**: Överväg att begränsa antalet samtidiga jobb som skickats till HDInsight. Referera till Data Factory aktivitets samtidighet om jobben skickas av samma aktivitet. Ändra utlösare så att samtidiga pipelines körs sprids över tid. Se HDInsight-dokumentationen för att `templeton.parallellism.job.submit` justera som ett fel förslag.


### <a name="error-code--2303"></a>Felkod:  2303

- **Meddelande**:`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Orsak**: Jobbet skickades till HDInsight och det misslyckades i HDInsight.

- **Rekommendation**: Jobbet skickades till HDInsight. Det misslyckades i klustret. Öppna jobbet och loggarna i HDInsight Ambari-ANVÄNDARGRÄNSSNITTET eller öppna filen från Storage som i fel meddelandet. Filen visar fel informationen.


### <a name="error-code--2310"></a>Felkod:  2310

- **Meddelande**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Orsak**: Den angivna kluster-URI: n är ogiltig. 

- **Rekommendation**:  Kontrol lera att klustret inte har tagits bort och att angiven URI är korrekt. När du öppnar URI i en webbläsare bör du se Ambari-ANVÄNDARGRÄNSSNITTET. Om klustret finns i ett virtuellt nätverk ska URI: n vara den privata URI: n. Öppna den genom att använda en virtuell dator som är en del av samma virtuella nätverk. Mer information finns i [direkt ansluta till Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Meddelande**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Orsak**: Felet är från HDInsight.

- **Rekommendation**: Det här felet är från HDInsight-klustret. Mer information finns i [AMBARI UI 502-fel](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502-fel vid anslutning till Spark Thrift-Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), 502-fel vid [anslutning till Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)och [fel sökning av felaktiga Gateway-fel i Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Meddelande**:`java.lang.NullPointerException`

- **Orsak**: Det här felet inträffar när jobbet skickas till ett Spark-kluster. 

- **Rekommendation**: Detta undantag kommer från HDInsight. Det döljer det faktiska problemet. Kontakta HDInsight-teamet för support. Ange kluster namnet och aktivitetens kör tids intervall.


### <a name="error-code--2347"></a>Felkod:  2347

- **Meddelande**:`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Orsak**: Jobbet skickades till HDInsight och det misslyckades i HDInsight.

- **Rekommendation**: Jobbet skickades till HDInsight. Det misslyckades i klustret. Öppna jobbet och loggarna i HDInsight Ambari-ANVÄNDARGRÄNSSNITTET eller öppna filen från Storage som i fel meddelandet. Filen visar fel informationen.


### <a name="error-code--2328"></a>Felkod:  2328

- **Meddelande**:`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Orsak**: Det här felet uppstår i HDInsight på begäran.

- **Rekommendation**: Felet kommer från HDInsight-tjänsten när HDInsight-etableringen Miss lyckas. Kontakta HDInsight-teamet och ange kluster namnet på begäran.



## <a name="web-activity"></a>Webbaktivitet

### <a name="error-code--2310"></a>Felkod:  2310

- **Meddelande**:`Invalid HttpMethod: '...'.`

- **Orsak**: Webb aktiviteten stöder inte den HTTP-metod som anges i aktivitetens nytto Last.

- **Rekommendation**:  De HTTP-metoder som stöds är placera, anslå, hämta och ta bort.

<br/>

- **Meddelande**:`Invalid Server Error 500.`

- **Orsak**: Internt fel på slut punkten.

- **Rekommendation**:  Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en.

<br/>

- **Meddelande**:`Unauthorized 401.`

- **Orsak**: Giltig autentisering saknas för begäran.

- **Rekommendation**:  Token kan ha upphört att gälla. Ange en giltig autentiseringsmetod. Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en.

<br/>

- **Meddelande**:`Forbidden 403.`

- **Orsak**: Nödvändiga behörigheter saknas.

- **Rekommendation**:  Kontrol lera användar behörigheter för den öppna resursen. Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en.

<br/>

- **Meddelande**:`Bad Request 400.`

- **Orsak**: Ogiltig HTTP-begäran.

- **Rekommendation**:   Kontrol lera URL, verb och brödtext i begäran. Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**:`Not found 404.` 

- **Orsak**: Det gick inte att hitta resursen.   

- **Rekommendation**:  Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**:`Service unavailable.`

- **Orsak**: Tjänsten är inte tillgänglig.

- **Rekommendation**:  Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**:`Unsupported Media Type.`

- **Orsak**: Innehålls typen matchar inte webb aktivitets texten.

- **Rekommendation**:  Ange den innehålls typ som matchar nytto Last formatet. Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**:`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Orsak**: Resursen är inte tillgänglig. 

- **Rekommendation**:  Använd Fiddler eller Postman för att kontrol lera slut punkten.

<br/>

- **Meddelande**:`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Orsak**: En felaktig webb aktivitets metod angavs i begäran.

- **Rekommendation**:  Använd Fiddler eller Postman för att kontrol lera slut punkten.

<br/>

- **Meddelande**:`invalid_payload`

- **Orsak**: Webb aktivitets texten är felaktig.

- **Rekommendation**:  Använd Fiddler eller Postman för att kontrol lera slut punkten.

Så här använder du Fiddler för att skapa en HTTP-session för det övervakade webb programmet:

1. Ladda ned, installera och öppna [Fiddler](https://www.telerik.com/download/fiddler).

1. Om ditt webb program använder https går du till **verktyg** > **Fiddler alternativ** > **https**. Välj **avbilda https ansluter** och **dekryptera HTTPS-trafik**. 
   
   ![Fiddler-alternativ](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Om programmet använder SSL-certifikat lägger du till Fiddler-certifikatet på enheten. Gå till **verktyg** > **Fiddler alternativ** > **https** **åtgärder** **Exportera rot certifikat till Skriv bordet.**  >  > 

1. Inaktivera hämtning genom att gå till **fil** > **hämtnings trafik**. Eller tryck på **F12**.

1. Rensa webbläsarens cacheminne så att alla cachelagrade objekt tas bort och måste laddas ned igen.

1. Skapa en begäran: 

   a. Välj fliken **Composer** .

   b. Ange HTTP-metod och URL.

   c. Lägg till rubriker och en begär ande text om du behöver.

   d. Välj **Kör**.

9. Aktivera trafik insamlingen igen och slutför den felande transaktionen på sidan.

10. Gå till **filen** > **Spara** > **alla sessioner**.

Mer information finns i [komma igång med Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)



