---
title: Felsöka Azure Data Factory
description: Lär dig hur du felsöker externa kontroll aktiviteter i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 66590e40ff2440a166310ec3d57026f5a2cdbd1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676854"
---
# <a name="troubleshoot-azure-data-factory"></a>Felsöka Azure Data Factory

Den här artikeln utforskar vanliga fel söknings metoder för externa kontroll aktiviteter i Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Anslutnings-och kopierings aktivitet

För anslutnings problem, t. ex. fel vid kopiering av aktivitet, se [fel sökning Azure Data Factory anslutningar](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Felkod: 3200

- **Meddelande**: fel 403.

- **Orsak**: `The Databricks access token has expired.`

- **Rekommendation**: som standard är Azure Databricks åtkomsttoken giltig i 90 dagar. Skapa en ny token och uppdatera den länkade tjänsten.


### <a name="error-code--3201"></a>Felkod: 3201

- **Meddelande**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Orsak**: `Bad authoring: Notebook path not specified correctly.`

- **Rekommendation**: Ange antecknings bokens sökväg i Databricks-aktiviteten.

<br/>

- **Meddelande**: `Cluster   ... does not exist.`

- **Orsak**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Rekommendation**: kontrol lera att Databricks-klustret finns.

<br/>

- **Meddelande**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: Ange antingen absoluta sökvägar för arbets ytans adresserings scheman eller `dbfs:/folder/subfolder/foo.py` för filer som lagras i fil systemet Databricks.

<br/>

- **Meddelande**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Meddelande**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Meddelande**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Orsak**: `Bad authoring.`

- **Rekommendation**: se fel meddelandet. 

<br/>

### <a name="error-code--3202"></a>Felkod: 3202

- **Meddelande**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Orsak**: `Too many Databricks runs in an hour.`

- **Rekommendation**: kontrol lera alla pipeliner som använder den här Databricks-arbetsytan för att skapa takten för jobbet.  Om pipelines startade för många Databricks-körningar i mängd, migrerar du några pipelines till en ny arbets yta.

<br/>

- **Meddelande**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Orsak**: `Authoring error: No value provided for the parameter.`

- **Rekommendation**: kontrol lera pipeline-JSON och se till att alla parametrar i baseParameters Notebook anger ett värde som inte är tomt.

<br/>

- **Meddelande**: `User: `SimpleUserContext {userId =..., name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Orsak**: användaren som genererade åtkomsttoken får inte åtkomst till det Databricks-kluster som anges i den länkade tjänsten.

- **Rekommendation**: se till att användaren har de behörigheter som krävs på arbets ytan.


### <a name="error-code--3203"></a>Felkod: 3203

- **Meddelande**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Orsak**: klustret avslutades. För interaktiva kluster kan detta vara ett tävlings tillstånd.

- **Rekommendation**: det bästa sättet att undvika det här felet är att använda jobb kluster.


### <a name="error-code--3204"></a>Felkod: 3204

- **Meddelande**: `Job execution failed.`

- **Orsak**: fel meddelanden indikerar olika problem, till exempel ett oväntat kluster tillstånd eller en speciell aktivitet. Oftast visas inget fel meddelande alls. 

- **Rekommendation**: ej tillämpligt


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Följande tabell gäller för U-SQL.


### <a name="error-code--2709"></a>Felkod: 2709

- **Meddelande**: `The access token is from the wrong tenant.`

- **Orsak**: felaktig Azure Active Directory (Azure AD)-klient.

- **Rekommendation**: felaktig Azure Active Directory (Azure AD)-klient.

<br/>

- **Meddelande**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Orsak**: det här felet orsakas av begränsning på data Lake Analytics.

- **Rekommendation**: minska antalet skickade jobb till data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics.

<br/>

- **Meddelande**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Orsak**: det här felet orsakas av begränsning på data Lake Analytics. 

- **Rekommendation**: minska antalet skickade jobb till data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics.


### <a name="error-code--2705"></a>Felkod: 2705

- **Meddelande**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.


### <a name="error-code--2711"></a>Felkod: 2711

- **Meddelande**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.

<br/>

- **Meddelande**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Orsak**: sökvägen till U-SQL-filen är felaktig eller också har autentiseringsuppgifterna för den länkade tjänsten inte åtkomst.

- **Rekommendation**: kontrol lera sökvägen och autentiseringsuppgifterna som anges i den länkade tjänsten.


### <a name="error-code--2704"></a>Felkod: 2704

- **Meddelande**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.


### <a name="error-code--2707"></a>Felkod: 2707

- **Meddelande**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Orsak**: det data Lake Analytics kontot i den länkade tjänsten är felaktigt.

- **Rekommendation**: kontrol lera att rätt konto har angetts.


### <a name="error-code--2703"></a>Felkod: 2703

- **Meddelande**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Orsak**: felet är från data Lake Analytics. 

- **Rekommendation**: ett fel som liknar exemplet innebär att jobbet skickades till data Lake Analytics och skriptet där det misslyckades. Undersök i Data Lake Analytics. I portalen går du till Data Lake Analytics kontot och letar efter jobbet med hjälp av Data Factory aktivitetens körnings-ID (inte pipelinens körnings-ID). Jobbet innehåller mer information om felet och hjälper dig att felsöka. Om upplösningen inte är klar kontaktar du Data Lake Analytics support-teamet och anger jobb-URL: en, som innehåller ditt konto namn och jobb-ID.



## <a name="azure-functions"></a>Azure functions

### <a name="error-code--3602"></a>Felkod: 3602

- **Meddelande**: `Invalid HttpMethod: {method}.`

- **Orsak**: http-metoden som anges i aktivitetens nytto Last stöds inte av Azure Function-aktivitet. 

- **Rekommendation**: de http-metoder som stöds är placera, posta, Hämta, ta bort, alternativ, huvud och spåra.


### <a name="error-code--3603"></a>Felkod: 3603

- **Meddelande**: `Response content is not a valid JObject.`

- **Orsak**: Azure-funktionen som anropades returnerade inte en JSON-nyttolast i svaret. Azure Function-aktivitet i Data Factory stöder endast JSON-svars innehåll. 

- **Rekommendation**: Uppdatera Azure-funktionen för att returnera en giltig JSON-nyttolast. En C# funktion kan till exempel returnera `(ActionResult)new<OkObjectResult("{`\"-ID\":\"123\"`}");`.


### <a name="error-code--3606"></a>Felkod: 3606

- **Meddelande**: `Azure function activity missing function key.`

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts. 

- **Rekommendation**: kontrol lera inAzureFunction Activity JSON-definitionen har egenskapen med namnet ' functionKey '.


### <a name="error-code--3607"></a>Felkod: 3607

- **Meddelande**: `Azure function activity missing function name.`

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts. 

- **Rekommendation**: kontrol lera indatamängds-AZUREFUNCTION aktivitet JSON-definitionen har egenskapen "functionname".


### <a name="error-code--3608"></a>Felkod: 3608

- **Meddelande**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Orsak**: Azure Function-information i aktivitets definitionen kan vara felaktig. 

- **Rekommendation**: korrigera Azure Function-information och försök igen.


### <a name="error-code--3609"></a>Felkod: 3609

- **Meddelande**: `Azure function activity missing functionAppUrl.` 

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts. 

- **Rekommendation**: kontrol lera inAzureFunction Activity JSON-definitionen har egenskapen med namnet ' functionAppUrl '.


### <a name="error-code--3610"></a>Felkod: 3610

- **Meddelande**: `There was an error while calling endpoint.`

- **Orsak**: funktions webb adress kan vara felaktig.

- **Rekommendation**: kontrol lera att värdet för ' functionAppUrl ' i AKTIVITETS-JSON är korrekt och försök igen.


### <a name="error-code--3611"></a>Felkod: 3611

- **Meddelande**: `Azure function activity missing Method in JSON.` 

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: kontrol lera om JSON-definitionen för inAzureFunctions aktivitet har en egenskap med namnet "metod".


### <a name="error-code--3612"></a>Felkod: 3612

- **Meddelande**: `Azure function activity missing LinkedService definition in JSON.`

- **Orsak**: definitionen av Azure Function-aktiviteten kanske inte är fullständig.

- **Rekommendation**: kontrol lera indata-AZUREFUNCTION aktivitet JSON-definitionen har länkat tjänst information.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Felkod: 4101

- **Meddelande**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Orsak**: felaktigt format eller saknar definition för en egenskap.

- **Rekommendation**: kontrol lera att aktiviteten har definierats med rätt data.


### <a name="error-code--4110"></a>Felkod: 4110

- **Meddelande**: AzureMLExecutePipeline-aktivitet saknar LinkedService-definition i JSON.

- **Orsak**: AzureMLExecutePipeline aktivitets definition har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för indata-AzureMLExecutePipeline aktivitet innehåller länkad tjänst information.


### <a name="error-code--4111"></a>Felkod: 4111

- **Meddelande**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Orsak**: felaktig aktivitets definition.

- **Rekommendation**: kontrol lera att JSON-definitionen för indata-AzureMLExecutePipeline Activity har rätt länkad tjänst information.


### <a name="error-code--4112"></a>Felkod: 4112

- **Meddelande**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Orsak**: felaktigt format eller saknar definition för en egenskap.

- **Rekommendation**: kontrol lera om den länkade tjänst definitionen har rätt data.


### <a name="error-code--4121"></a>Felkod: 4121

- **Meddelande**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Orsak**: autentiseringsuppgifterna som används för att komma åt Azure ml-tjänsten har upphört att gälla.

- **Rekommendation**: kontrol lera att autentiseringsuppgifterna är giltigt och försök igen


### <a name="error-code--4122"></a>Felkod: 4122

- **Meddelande**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Orsak**: autentiseringsuppgifterna som angetts i den länkade tjänsten i azureml-tjänsten är ogiltiga eller har inte behörighet för åtgärden.

- **Rekommendation**: kontrol lera att autentiseringsuppgiften i den länkade tjänsten är giltig och har behörighet att komma åt azureml-tjänsten.


### <a name="error-code--4123"></a>Felkod: 4123

- **Meddelande**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Orsak**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Rekommendation**: kontrol lera värdet för aktivitets egenskaper för att matcha förväntad nytto last för den publicerade Azure ml-pipelinen som anges i den länkade tjänsten.


### <a name="error-code--4124"></a>Felkod: 4124

- **Meddelande**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Orsak**: slut punkten för den publicerade Azure ml-pipelinen finns inte.

- **Rekommendation**: kontrol lera att den publicerade Azure ml-pipelinen som angavs i den länkade tjänsten finns i Azure ml-tjänsten.


### <a name="error-code--4125"></a>Felkod: 4125

- **Meddelande**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Orsak**: Server fel i Azure ml-tjänsten.

- **Rekommendation**: försök igen senare. Kontakta Azure ML-teamet för hjälp om problemet kvarstår.


### <a name="error-code--4126"></a>Felkod: 4126

- **Meddelande**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Orsak**: azureml-pipeline-körningen misslyckades.

- **Rekommendation**: kontrol lera i AzureMLService för fler fel loggningar och åtgärda ml-pipeline


## <a name="custom"></a>Anpassat

Följande tabell gäller för Azure Batch.


### <a name="error-code--2500"></a>Felkod: 2500

- **Meddelande**: `Hit unexpected exception and execution failed.`

- **Orsak**: `Can't launch command, or the program returned an error code.`

- **Rekommendation**: kontrol lera att den körbara filen finns. Om programmet har startats kontrollerar du att *STDOUT. txt* och *stderr. txt* överfördes till lagrings kontot. Det är en bra idé att generera Copious-loggar i koden för fel sökning.


### <a name="error-code--2501"></a>Felkod: 2501

- **Meddelande**: `Cannot access user batch account; please check batch account settings.`

- **Orsak**: felaktig nyckel för batch-åtkomst eller poolnamn.

- **Rekommendation**: verifiera poolens namn och grupp åtkomst nyckeln i den länkade tjänsten.


### <a name="error-code--2502"></a>Felkod: 2502

- **Meddelande**: `Cannot access user storage account; please check storage account settings.`

- **Orsak**: felaktigt lagrings konto namn eller åtkomst nyckel.

- **Rekommendation**: kontrol lera lagrings kontots namn och åtkomst nyckeln i den länkade tjänsten.


### <a name="error-code--2504"></a>Felkod: 2504

- **Meddelande**: `Operation returned an invalid status code 'BadRequest'.` 

- **Orsak**: för många filer i folderPath för den anpassade aktiviteten. Den totala storleken på resourceFiles får inte vara mer än 32 768 tecken.

- **Rekommendation**: ta bort onödiga filer. Eller Lägg till ett zippa-kommando för att extrahera dem. Använd till exempel `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Felkod: 2505

- **Meddelande**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Orsak**: anpassade aktiviteter stöder bara lagrings konton som använder en åtkomst nyckel.

- **Rekommendation**: se fel beskrivningen.


### <a name="error-code--2507"></a>Felkod: 2507

- **Meddelande**: `The folder path does not exist or is empty: ....`

- **Orsak**: inga filer finns i lagrings kontot på den angivna sökvägen.

- **Rekommendation**: sökvägen till mappen måste innehålla de körbara filer som du vill köra.


### <a name="error-code--2508"></a>Felkod: 2508

- **Meddelande**: `There are duplicate files in the resource folder.`

- **Orsak**: flera filer med samma namn finns i olika undermappar i folderPath.

- **Rekommendation**: sammanslagen mappstruktur för anpassade aktiviteter under folderPath.  Om du behöver bevara mappstrukturen zip-filerna och extrahera dem i Azure Batch med hjälp av ett zippa-kommando. Använd till exempel `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Felkod: 2509

- **Meddelande**: `Batch   url ... is invalid; it must be in Uri format.` 

- **Orsak**: batch-URL: er måste likna `https://mybatchaccount.eastus.batch.azure.com`

- **Rekommendation**: se fel beskrivningen.


### <a name="error-code--2510"></a>Felkod: 2510

- **Meddelande**: `An   error occurred while sending the request.`

- **Orsak**: batch-URL: en är ogiltig. 

- **Rekommendation**: verifiera batch-URL: en.


## <a name="hdinsight"></a>HDInsight

Följande tabell gäller Spark, Hive, MapReduce, gris och Hadoop streaming.


### <a name="error-code--2300"></a>Felkod: 2300

- **Meddelande**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Orsak**: den angivna kluster-URI: n är ogiltig. 

- **Rekommendation**: kontrol lera att klustret inte har tagits bort och att angiven URI är korrekt. När du öppnar URI i en webbläsare bör du se Ambari-ANVÄNDARGRÄNSSNITTET. Om klustret finns i ett virtuellt nätverk ska URI: n vara den privata URI: n. Öppna den genom att använda en virtuell dator som är en del av samma virtuella nätverk. Mer information finns i [direkt ansluta till Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Meddelande**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Orsak**: jobb sändningen tog för lång tid. 

- **Rekommendation**: problemet kan vara antingen allmän HDInsight-anslutning eller nätverks anslutning. Bekräfta först att HDInsight Ambari-ANVÄNDARGRÄNSSNITTET är tillgängligt från valfri webbläsare. Bekräfta att autentiseringsuppgifterna fortfarande är giltiga. Om du använder en lokal integrerad Runtime (IR), se till att göra detta från den virtuella datorn eller datorn där IR för egen värd är installerad. Försök sedan att skicka jobbet från Data Factory igen. Om det fortfarande inte går att kontakta Data Factory-teamet för support.


- **Meddelande**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Orsak**: autentiseringsuppgifterna för HDInsight är felaktiga eller har upphört att gälla.

- **Rekommendation**: korrigera autentiseringsuppgifterna och distribuera om den länkade tjänsten. Kontrol lera först att autentiseringsuppgifterna fungerar på HDInsight genom att öppna kluster-URI: n i valfri webbläsare och försök logga in. Om autentiseringsuppgifterna inte fungerar kan du återställa dem från Azure Portal.

<br/>

- **Meddelande**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Orsak**: det här felet är från HDInsight.

- **Rekommendation**: det här felet är från HDInsight-klustret. Mer information finns i [AMBARI UI 502-fel](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502-fel vid anslutning till Spark Thrift-Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), 502-fel vid [anslutning till Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)och [fel sökning av felaktiga Gateway-fel i Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Meddelande**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Orsak**: för många jobb skickas till HDInsight på samma tid.

- **Rekommendation**: Överväg att begränsa antalet samtidiga jobb som skickats till HDInsight. Referera till Data Factory aktivitets samtidighet om jobben skickas av samma aktivitet. Ändra utlösare så att samtidiga pipelines körs sprids över tid. Se HDInsight-dokumentationen för att justera `templeton.parallellism.job.submit` som ett fel förslag.


### <a name="error-code--2303"></a>Felkod: 2303

- **Meddelande**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Orsak**: jobbet skickades till HDInsight och det misslyckades i HDInsight.

- **Rekommendation**: jobbet skickades till HDInsight. Det misslyckades i klustret. Öppna jobbet och loggarna i HDInsight Ambari-ANVÄNDARGRÄNSSNITTET eller öppna filen från Storage som i fel meddelandet. Filen visar fel informationen.


### <a name="error-code--2310"></a>Felkod: 2310

- **Meddelande**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Orsak**: den angivna kluster-URI: n är ogiltig. 

- **Rekommendation**: kontrol lera att klustret inte har tagits bort och att angiven URI är korrekt. När du öppnar URI i en webbläsare bör du se Ambari-ANVÄNDARGRÄNSSNITTET. Om klustret finns i ett virtuellt nätverk ska URI: n vara den privata URI: n. Öppna den genom att använda en virtuell dator som är en del av samma virtuella nätverk. Mer information finns i [direkt ansluta till Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Meddelande**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Orsak**: det här felet är från HDInsight.

- **Rekommendation**: det här felet är från HDInsight-klustret. Mer information finns i [AMBARI UI 502-fel](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502-fel vid anslutning till Spark Thrift-Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), 502-fel vid [anslutning till Spark Thrift Server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)och [fel sökning av felaktiga Gateway-fel i Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Meddelande**: `java.lang.NullPointerException`

- **Orsak**: det här felet uppstår när jobbet skickas till ett Spark-kluster. 

- **Rekommendation**: detta undantag kommer från HDInsight. Det döljer det faktiska problemet. Kontakta HDInsight-teamet för support. Ange kluster namnet och aktivitetens kör tids intervall.


### <a name="error-code--2347"></a>Felkod: 2347

- **Meddelande**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Orsak**: jobbet skickades till HDInsight och det misslyckades i HDInsight.

- **Rekommendation**: jobbet skickades till HDInsight. Det misslyckades i klustret. Öppna jobbet och loggarna i HDInsight Ambari-ANVÄNDARGRÄNSSNITTET eller öppna filen från Storage som i fel meddelandet. Filen visar fel informationen.


### <a name="error-code--2328"></a>Felkod: 2328

- **Meddelande**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Orsak**: det här felet uppstår i HDInsight på begäran.

- **Rekommendation**: det här felet kommer från HDInsight-tjänsten när HDInsight-etableringen Miss lyckas. Kontakta HDInsight-teamet och ange kluster namnet på begäran.



## <a name="web-activity"></a>Webbaktivitet

### <a name="error-code--2108"></a>Felkod: 2108

- **Meddelande**: `Invalid HttpMethod: '...'.`

- **Orsak**: webb aktiviteten stöder inte den http-metod som anges i aktivitetens nytto Last.

- **Rekommendation**: de http-metoder som stöds är placera, anslå, hämta och ta bort.

<br/>

- **Meddelande**: `Invalid Server Error 500.`

- **Orsak**: internt fel på slut punkten.

- **Rekommendation**: Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en.

<br/>

- **Meddelande**: `Unauthorized 401.`

- **Orsak**: giltig autentisering saknas i begäran.

- **Rekommendation**: token kan ha gått ut. Ange en giltig autentiseringsmetod. Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en.

<br/>

- **Meddelande**: `Forbidden 403.`

- **Orsak**: nödvändiga behörigheter saknas.

- **Rekommendation**: kontrol lera användar behörigheter för den öppna resursen. Använd Fiddler eller Postman för att kontrol lera funktionen på URL: en.

<br/>

- **Meddelande**: `Bad Request 400.`

- **Orsak**: ogiltig http-begäran.

- **Rekommendation**: kontrol lera URL, verb och brödtext i begäran. Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**: `Not found 404.` 

- **Orsak**: det gick inte att hitta resursen.   

- **Rekommendation**: Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**: `Service unavailable.`

- **Orsak**: tjänsten är inte tillgänglig.

- **Rekommendation**: Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**: `Unsupported Media Type.`

- **Orsak**: innehålls typen matchar inte webb aktivitets texten.

- **Rekommendation**: Ange den innehålls typ som matchar nytto Last formatet. Använd Fiddler eller Postman för att verifiera begäran.

<br/>

- **Meddelande**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Orsak**: resursen är inte tillgänglig. 

- **Rekommendation**: Använd Fiddler eller Postman för att kontrol lera slut punkten.

<br/>

- **Meddelande**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Orsak**: en felaktig webb aktivitets metod angavs i begäran.

- **Rekommendation**: Använd Fiddler eller Postman för att kontrol lera slut punkten.

<br/>

- **Meddelande**: `invalid_payload`

- **Orsak**: webb aktivitets texten är felaktig.

- **Rekommendation**: Använd Fiddler eller Postman för att kontrol lera slut punkten.


### <a name="error-code--2208"></a>Felkod: 2208

- **Meddelande**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Orsak**: mål tjänsten returnerade fel statusen.

- **Rekommendation**: Använd Fiddler/Postman för att verifiera begäran.


### <a name="error-code--2308"></a>Felkod: 2308

- **Meddelande**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Orsak**: det kan finnas flera orsaker till det här felet, t. ex. nätverks anslutning, DNS-fel, verifiering av Server certifikat eller tids gräns.

- **Rekommendation**: Använd Fiddler/Postman för att verifiera begäran.


Så här använder du Fiddler för att skapa en HTTP-session för det övervakade webb programmet:

1. Ladda ned, installera och öppna [Fiddler](https://www.telerik.com/download/fiddler).

1. Om ditt webb program använder HTTPS går du till **verktyg** > **Fiddler-alternativ** > **https**. Välj **avbilda https ansluter** och **dekryptera HTTPS-trafik**. 
   
   ![Fiddler-alternativ](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Om programmet använder SSL-certifikat lägger du till Fiddler-certifikatet på enheten. Gå till **verktyg** > **Fiddler alternativ** > **https** > **åtgärder** > **Exportera rot certifikat till Skriv bordet**.

1. Inaktivera hämtning genom att gå till **fil** > **avbildnings trafik**. Eller tryck på **F12**.

1. Rensa webbläsarens cacheminne så att alla cachelagrade objekt tas bort och måste laddas ned igen.

1. Skapa en begäran: 

   a. Välj fliken **Composer** .

   b. Ange HTTP-metod och URL.

   c. Lägg till rubriker och en begär ande text om du behöver.

   d. Välj **Kör**.

9. Aktivera trafik insamlingen igen och slutför den felande transaktionen på sidan.

10. Gå till **fil** > **Spara** > **alla sessioner**.

Mer information finns i [komma igång med Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory-bloggen](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)



