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
ms.openlocfilehash: af0c95c197a70291e0fc863f3256be0e9a1da7f1
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133214"
---
# <a name="troubleshoot-azure-data-factory"></a>Felsöka Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för externa kontroll aktiviteter i Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Anslutnings-och kopierings aktivitet

För anslutnings problem, t. ex. fel vid kopiering av aktivitet, se [fel sökning Azure Data Factory anslutningar](connector-troubleshoot-guide.md).
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Felkod: 3200

- **Meddelande**: fel 403.

- **Orsak**:`The Databricks access token has expired.`

- **Rekommendation**: som standard är Azure Databricks åtkomsttoken giltig i 90 dagar. Skapa en ny token och uppdatera den länkade tjänsten.


### <a name="error-code--3201"></a>Felkod: 3201

- **Meddelande**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Orsak**:`Bad authoring: Notebook path not specified correctly.`

- **Rekommendation**: Ange antecknings bokens sökväg i Databricks-aktiviteten.

<br/>  

- **Meddelande**:`Cluster... does not exist.`

- **Orsak**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Rekommendation**: kontrol lera att Databricks-klustret finns.

<br/>  

- **Meddelande**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: Ange antingen absoluta sökvägar för arbets ytans adresserings scheman eller `dbfs:/folder/subfolder/foo.py` filer som lagrats i Databricks-filsystem.

<br/>  

- **Meddelande**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Meddelande**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: verifiera den [länkade tjänst definitionen](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Meddelande**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Orsak**:`Bad authoring.`

- **Rekommendation**: se fel meddelandet.

<br/>

### <a name="error-code--3202"></a>Felkod: 3202

- **Meddelande**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Orsak**:`Too many Databricks runs in an hour.`

- **Rekommendation**: kontrol lera alla pipeliner som använder den här Databricks-arbetsytan för att skapa takten för jobbet.  Om pipelines startade för många Databricks-körningar i mängd, migrerar du några pipelines till en ny arbets yta.

<br/>  

- **Meddelande**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Orsak**:`Authoring error: No value provided for the parameter.`

- **Rekommendation**: kontrol lera pipeline-JSON och se till att alla parametrar i baseParameters Notebook anger ett värde som inte är tomt.

<br/>  

- **Meddelande**: `User: `SimpleUserContext {userId =..., name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Orsak**: användaren som genererade åtkomsttoken får inte åtkomst till det Databricks-kluster som anges i den länkade tjänsten.

- **Rekommendation**: se till att användaren har de behörigheter som krävs på arbets ytan.


### <a name="error-code--3203"></a>Felkod: 3203

- **Meddelande**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Orsak**: klustret avslutades. För interaktiva kluster kan detta vara ett tävlings tillstånd.

- **Rekommendation**: det bästa sättet att undvika det här felet är att använda jobb kluster.


### <a name="error-code--3204"></a>Felkod: 3204

- **Meddelande**:`Job execution failed.`

- **Orsak**: fel meddelanden indikerar olika problem, till exempel ett oväntat kluster tillstånd eller en speciell aktivitet. Oftast visas inget fel meddelande alls.

- **Rekommendation**: ej tillämpligt
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Följande tabell gäller för U-SQL.
      
### <a name="error-code--2709"></a>Felkod: 2709

- **Meddelande**:`The access token is from the wrong tenant.`

- **Orsak**: felaktig Azure Active Directory (Azure AD)-klient.

- **Rekommendation**: felaktig Azure Active Directory (Azure AD)-klient.

<br/>

- **Meddelande**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Orsak**: det här felet orsakas av begränsning på data Lake Analytics.

- **Rekommendation**: minska antalet skickade jobb till data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics.

<br/>  

- **Meddelande**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Orsak**: det här felet orsakas av begränsning på data Lake Analytics.

- **Rekommendation**: minska antalet skickade jobb till data Lake Analytics genom att ändra Data Factory utlösare och concurrency-inställningar för aktiviteter. Eller öka gränserna för Data Lake Analytics.


### <a name="error-code--2705"></a>Felkod: 2705

- **Meddelande**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.


### <a name="error-code--2711"></a>Felkod: 2711

- **Meddelande**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.

<br/>  

- **Meddelande**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Orsak**: sökvägen till U-SQL-filen är felaktig eller också har autentiseringsuppgifterna för den länkade tjänsten inte åtkomst.

- **Rekommendation**: kontrol lera sökvägen och autentiseringsuppgifterna som anges i den länkade tjänsten.


### <a name="error-code--2704"></a>Felkod: 2704

- **Meddelande**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Orsak**: tjänstens huvud namn eller certifikat har inte åtkomst till filen i lagrings utrymmet.

- **Rekommendation**: kontrol lera att tjänstens huvud namn eller certifikat som användaren tillhandahåller för data Lake Analytics jobb har åtkomst till data Lake Analytics-kontot och standard-data Lake Storage-instansen från rotmappen.


### <a name="error-code--2707"></a>Felkod: 2707

- **Meddelande**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Orsak**: det data Lake Analytics kontot i den länkade tjänsten är felaktigt.

- **Rekommendation**: kontrol lera att rätt konto har angetts.


### <a name="error-code--2703"></a>Felkod: 2703

- **Meddelande**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Orsak**: felet är från data Lake Analytics.

- **Rekommendation**: ett fel som liknar exemplet innebär att jobbet skickades till data Lake Analytics och skriptet där det misslyckades. Undersök i Data Lake Analytics. I portalen går du till Data Lake Analytics kontot och letar efter jobbet med hjälp av Data Factory aktivitetens körnings-ID (inte pipelinens körnings-ID). Jobbet innehåller mer information om felet och hjälper dig att felsöka. Om upplösningen inte är klar kontaktar du Data Lake Analytics support-teamet och anger jobb-URL: en, som innehåller ditt konto namn och jobb-ID.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Felkod: 3602

- **Meddelande**:`Invalid HttpMethod: '%method;'.`

- **Orsak**: http-metoden som anges i aktivitetens nytto Last stöds inte av Azure Function-aktivitet.

- **Rekommendation**: de http-metoder som stöds är placera, posta, Hämta, ta bort, alternativ, huvud och spåra.


### <a name="error-code--3603"></a>Felkod: 3603

- **Meddelande**:`Response Content is not a valid JObject.`

- **Orsak**: Azure Function som anropades returnerade inte en JSON-nyttolast i svaret. ADF Azure Function-aktivitet stöder endast JSON-svars innehåll.

- **Rekommendation**: Uppdatera Azure Function för att returnera en giltig JSON-nyttolast, t. ex. en C#-funktion kan returnera (ActionResult\")\"New\"OkObjectResult\"({ID: 123});


### <a name="error-code--3606"></a>Felkod: 3606

- **Meddelande**: Azure Function-aktivitet saknar funktions nyckel.

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: kontrol lera inAzureFunction Activity JSON-definitionen har egenskapen med namnet ' functionKey '.


### <a name="error-code--3607"></a>Felkod: 3607

- **Meddelande**:`Azure function activity missing function name.`

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: kontrol lera indatamängds-AZUREFUNCTION aktivitet JSON-definitionen har egenskapen "functionname".


### <a name="error-code--3608"></a>Felkod: 3608

- **Meddelande**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Orsak**: Azure Function-information i aktivitets definitionen kan vara felaktig.

- **Rekommendation**: korrigera Azure Function-information och försök igen.


### <a name="error-code--3609"></a>Felkod: 3609

- **Meddelande**:`Azure function activity missing functionAppUrl.`

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: kontrol lera inAzureFunction Activity JSON-definitionen har egenskapen med namnet ' functionAppUrl '.


### <a name="error-code--3610"></a>Felkod: 3610

- **Meddelande**:`There was an error while calling endpoint.`

- **Orsak**: funktions webb adress kan vara felaktig.

- **Rekommendation**: kontrol lera att värdet för ' functionAppUrl ' i AKTIVITETS-JSON är korrekt och försök igen.


### <a name="error-code--3611"></a>Felkod: 3611

- **Meddelande**:`Azure function activity missing Method in JSON.`

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: kontrol lera indatamängds-AZUREFUNCTION aktivitet JSON-definitionen har egenskapen "metod".


### <a name="error-code--3612"></a>Felkod: 3612

- **Meddelande**:`Azure function activity missing LinkedService definition in JSON.`

- **Orsak**: definitionen av Azure Function-aktiviteten har inte slutförts.

- **Rekommendation**: kontrol lera indata-AZUREFUNCTION aktivitet JSON-definitionen har länkat tjänst information.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Felkod: 4101

- **Meddelande**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Orsak**: felaktigt format eller en definition av egenskapen% PropertyName; saknas.

- **Rekommendation**: kontrol lera att aktiviteten% activityName; har egenskapen% PropertyName; definierad med rätt data.


### <a name="error-code--4110"></a>Felkod: 4110

- **Meddelande**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Orsak**: AzureMLExecutePipeline aktivitets definition har inte slutförts.

- **Rekommendation**: kontrol lera att JSON-definitionen för indata-AzureMLExecutePipeline aktivitet innehåller länkad tjänst information.


### <a name="error-code--4111"></a>Felkod: 4111

- **Meddelande**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Orsak**: felaktig aktivitets definition.

- **Rekommendation**: kontrol lera att JSON-definitionen för indata-AzureMLExecutePipeline Activity har rätt länkad tjänst information.


### <a name="error-code--4112"></a>Felkod: 4112

- **Meddelande**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Orsak**: felaktigt format eller en definition av egenskapen% PropertyName; saknas.

- **Rekommendation**: kontrol lera om den länkade tjänsten har egenskapen% PropertyName; definierad med rätt data.


### <a name="error-code--4121"></a>Felkod: 4121

- **Meddelande**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: autentiseringsuppgifterna som används för att komma åt Azure Machine Learning har upphört att gälla.

- **Rekommendation**: kontrol lera att autentiseringsuppgifterna är giltigt och försök igen


### <a name="error-code--4122"></a>Felkod: 4122

- **Meddelande**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: autentiseringsuppgifterna som angavs i Azure Machine Learning länkade tjänsten är ogiltiga eller har inte behörighet för åtgärden.

- **Rekommendation**: kontrol lera att autentiseringsuppgifterna i den länkade tjänsten är giltiga och har åtkomst behörighet till Azure Machine Learning.


### <a name="error-code--4123"></a>Felkod: 4123

- **Meddelande**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: egenskaperna för aktiviteten, till exempel pipelineParameters, är ogiltiga för Azure ml-pipeline.

- **Rekommendation**: kontrol lera värdet för aktivitets egenskaper för att matcha förväntad nytto last för den publicerade Azure ml-pipelinen som anges i den länkade tjänsten.


### <a name="error-code--4124"></a>Felkod: 4124

- **Meddelande**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: slut punkten för den publicerade Azure ml-pipelinen finns inte.

- **Rekommendation**: kontrol lera att den publicerade Azure Machine Learning pipelinen som angavs i den länkade tjänsten finns i Azure Machine Learning.


### <a name="error-code--4125"></a>Felkod: 4125

- **Meddelande**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: Server fel på Azure Machine Learning.

- **Rekommendation**: försök igen senare. Kontakta Azure Machine Learning team för hjälp om problemet kvarstår.


### <a name="error-code--4126"></a>Felkod: 4126

- **Meddelande**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Orsak**: körningen av Azure ml-pipeline misslyckades.

- **Rekommendation**: sök i Azure Machine Learning efter fler fel loggar och korrigera ml-pipelinen.



## <a name="common"></a>Common

### <a name="error-code--2103"></a>Felkod: 2103

- **Meddelande**:`Please provide value for the required property '%propertyName;'.`

- **Orsak**: egenskapens värde har inte angetts, men det krävs i scenariot.

- **Rekommendation**: Ange värdet från meddelandet och försök igen.


### <a name="error-code--2104"></a>Felkod: 2104

- **Meddelande**:`The type of the property '%propertyName;' is incorrect.`

- **Orsak**: typen för den angivna egenskapen är inte som förväntat.

- **Rekommendation**: korrigera egenskapens typ och försök igen.


### <a name="error-code--2105"></a>Felkod: 2105

- **Meddelande**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Orsak**: värdet för egenskapen är ogiltigt eller har inte det förväntade formatet.

- **Rekommendation**: Sök i dokumentationen för egenskapen och se till att det angivna värdet har förväntat format och typ.


### <a name="error-code--2106"></a>Felkod: 2106

- **Meddelande**:`The storage connection string is invalid. %errorMessage;`

- **Orsak**: anslutnings strängen för lagrings platsen är ogiltig eller har fel format.

- **Rekommendation**: gå till Azure Portal, hitta din lagring, kopiera anslutnings strängen och klistra in den i den länkade tjänsten och försök igen.


### <a name="error-code--2108"></a>Felkod: 2108

- **Meddelande**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Orsak**: begäran misslyckades på grund av ett underliggande problem, till exempel nätverks anslutning, DNS-fel, verifiering av Server certifikat eller tids gräns.

- **Rekommendation**: Använd Fiddler/Postman för att verifiera begäran.


### <a name="error-code--2110"></a>Felkod: 2110

- **Meddelande**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Orsak**: den länkade tjänsten som angetts i aktiviteten var felaktig.

- **Rekommendation**: kontrol lera att den länkade tjänst typen är en av de typer som stöds för aktiviteten. Till exempel kan den länkade tjänst typen vara HDInsight eller HDInsightOnDemand för HDI-aktiviteter.


### <a name="error-code--2111"></a>Felkod: 2111

- **Meddelande**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Orsak**: typen för den angivna egenskapen är inte som förväntat.

- **Rekommendation**: korrigera egenskapens typ och försök igen.


### <a name="error-code--2112"></a>Felkod: 2112

- **Meddelande**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Orsak**: moln typen stöds inte eller kunde inte fastställas för lagring från EndpointSuffix.

- **Rekommendation**: Använd lagring i ett annat moln och försök igen.


### <a name="error-code--2128"></a>Felkod: 2128

- **Meddelande**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Orsak**: nätverks anslutning, DNS-fel, verifiering av Server certifikat eller tids gräns.

- **Rekommendation**: kontrol lera att slut punkten som du försöker trycka på svarar på begär Anden. Du kan använda verktyg som Fiddler/Postman.



## <a name="custom"></a>Anpassad

Följande tabell gäller för Azure Batch.
      
### <a name="error-code--2500"></a>Felkod: 2500

- **Meddelande**:`Hit unexpected exception and execution failed.`

- **Orsak**:`Can't launch command, or the program returned an error code.`

- **Rekommendation**: kontrol lera att den körbara filen finns. Om programmet har startats kontrollerar du att *STDOUT. txt* och *stderr. txt* överfördes till lagrings kontot. Det är en bra idé att generera Copious-loggar i koden för fel sökning.


### <a name="error-code--2501"></a>Felkod: 2501

- **Meddelande**:`Cannot access user batch account; please check batch account settings.`

- **Orsak**: felaktig nyckel för batch-åtkomst eller poolnamn.

- **Rekommendation**: verifiera poolens namn och grupp åtkomst nyckeln i den länkade tjänsten.


### <a name="error-code--2502"></a>Felkod: 2502

- **Meddelande**:`Cannot access user storage account; please check storage account settings.`

- **Orsak**: felaktigt lagrings konto namn eller åtkomst nyckel.

- **Rekommendation**: kontrol lera lagrings kontots namn och åtkomst nyckeln i den länkade tjänsten.


### <a name="error-code--2504"></a>Felkod: 2504

- **Meddelande**:`Operation returned an invalid status code 'BadRequest'.`

- **Orsak**: för många filer i folderPath för den anpassade aktiviteten. Den totala storleken på resourceFiles får inte vara mer än 32 768 tecken.

- **Rekommendation**: ta bort onödiga filer. Eller Lägg till ett zippa-kommando för att extrahera dem. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Felkod: 2505

- **Meddelande**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Orsak**: anpassade aktiviteter stöder bara lagrings konton som använder en åtkomst nyckel.

- **Rekommendation**: se fel beskrivningen.


### <a name="error-code--2507"></a>Felkod: 2507

- **Meddelande**:`The folder path does not exist or is empty: ...`

- **Orsak**: inga filer finns i lagrings kontot på den angivna sökvägen.

- **Rekommendation**: sökvägen till mappen måste innehålla de körbara filer som du vill köra.


### <a name="error-code--2508"></a>Felkod: 2508

- **Meddelande**:`There are duplicate files in the resource folder.`

- **Orsak**: flera filer med samma namn finns i olika undermappar i folderPath.

- **Rekommendation**: sammanslagen mappstruktur för anpassade aktiviteter under folderPath.  Om du behöver bevara mappstrukturen zip-filerna och extrahera dem i Azure Batch med hjälp av ett zippa-kommando. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Felkod: 2509

- **Meddelande**:`Batch   url ... is invalid; it must be in Uri format.`

- **Orsak**: batch-URL: er måste likna`https://mybatchaccount.eastus.batch.azure.com`

- **Rekommendation**: se fel beskrivningen.


### <a name="error-code--2510"></a>Felkod: 2510

- **Meddelande**:`An   error occurred while sending the request.`

- **Orsak**: batch-URL: en är ogiltig.

- **Rekommendation**: verifiera batch-URL: en.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Felkod: 200

- **Meddelande**:`Unexpected error happened: '%error;'.`

- **Orsak**: det finns ett internt tjänst problem.

- **Rekommendation**: kontakta ADF-supporten om du behöver ytterligare hjälp.


### <a name="error-code--201"></a>Felkod: 201

- **Meddelande**:`JobType %jobType; is not found.`

- **Orsak**: det finns en ny jobbtyp som inte stöds av ADF.

- **Rekommendation**: kontakta ADF support team om du behöver ytterligare hjälp.


### <a name="error-code--202"></a>Felkod: 202

- **Meddelande**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: fel meddelandet ska visa information om vad som gått fel.

- **Rekommendation**: fel meddelandet bör hjälpa till att felsöka problemet. Om det inte finns tillräckligt med information kan du kontakta ADF-supporten om du vill ha mer hjälp.


### <a name="error-code--203"></a>Felkod: 203

- **Meddelande**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: fel meddelandet ska visa information om vad som gått fel.

- **Rekommendation**: fel meddelandet bör hjälpa till att felsöka problemet. Om det inte finns tillräckligt med information kan du kontakta ADF-supporten om du vill ha mer hjälp.


### <a name="error-code--204"></a>Felkod: 204

- **Meddelande**:`The resumption token is missing for runId '%runId;'.`

- **Orsak**: det finns ett internt tjänst problem.

- **Rekommendation**: kontakta ADF-supporten om du behöver ytterligare hjälp.


### <a name="error-code--205"></a>Felkod: 205

- **Meddelande**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Orsak**: det uppstod ett fel när HDI på demand-klustret skapades.

- **Rekommendation**: kontakta ADF-supporten om du behöver ytterligare hjälp.


### <a name="error-code--206"></a>Felkod: 206

- **Meddelande**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Orsak**: det uppstod ett internt problem med tjänsten som orsakade detta.

- **Rekommendation**: det kan vara ett tillfälligt problem. Försök utföra jobbet igen. om problemet kvarstår kontaktar du supporten för ADF för ytterligare hjälp.


### <a name="error-code--207"></a>Felkod: 207

- **Meddelande**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Orsak**: det uppstod ett internt fel vid försök att fastställa regionen från det primära lagrings kontot.

- **Rekommendation**: försök med ett annat lagrings utrymme. Om detta inte är en acceptabel lösning kan du kontakta ADF support team för ytterligare hjälp.


### <a name="error-code--208"></a>Felkod: 208

- **Meddelande**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Orsak**: det uppstod ett internt fel vid försök att läsa tjänstens huvud namn eller instansiera MSI-autentiseringen.

- **Rekommendation**: Överväg att ange ett huvud namn för tjänsten som har behörighet att skapa ett HDInsight-kluster i den angivna prenumerationen och försök igen. Se till att [hantera identiteter är korrekt](https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities)konfigurerade. Om detta inte är en acceptabel lösning kan du kontakta ADF support team för ytterligare hjälp.


### <a name="error-code--2300"></a>Felkod: 2300

- **Meddelande**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar "fjärrnamnet kunde inte matchas". Detta kan betyda att den angivna kluster-URI: n är ogiltig.


- **Rekommendation**: kontrol lera att klustret inte har tagits bort och att angiven URI är korrekt. När du öppnar URI i en webbläsare bör du se Ambari-ANVÄNDARGRÄNSSNITTET. Om klustret finns i ett virtuellt nätverk ska URI: n vara den privata URI: n. Öppna den genom att använda en virtuell dator som är en del av samma virtuella nätverk. Mer information finns i [detta](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar "en uppgift har avbrutits." innebär det att jobb överföringen har nått sin tids gräns.

- **Rekommendation**: problemet kan vara antingen allmän HDInsight-anslutning eller nätverks anslutning. Bekräfta först att HDInsight Ambari-ANVÄNDARGRÄNSSNITTET är tillgängligt från valfri webbläsare. Bekräfta att autentiseringsuppgifterna fortfarande är giltiga. Mer information finns i [Ambari Web UI](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-manage-ambari#ambari-web-ui). Om du använder en lokal integrerad Runtime (IR), se till att göra detta från den virtuella datorn eller datorn där IR för egen värd är installerad. Försök sedan att skicka jobbet från Data Factory igen. Om det fortfarande inte går att kontakta Data Factory-teamet för support.

<br>

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar "användar administratör är utelåst i Ambari" eller "otillåtet: Ambari användar namn eller lösen ord är felaktigt" innebär det att autentiseringsuppgifterna för HDInsight är felaktiga eller har upphört att gälla.

- **Rekommendation**: korrigera autentiseringsuppgifterna och distribuera om den länkade tjänsten. Kontrol lera först att autentiseringsuppgifterna fungerar på HDInsight genom att öppna kluster-URI: n i valfri webbläsare och försök logga in. Om autentiseringsuppgifterna inte fungerar kan du återställa dem från Azure Portal. För ESP-kluster kan du [återställa lösen ordet med hjälp av lösen ords återställning via](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password)självbetjäning.

<br>

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar "502-webb servern tog emot ett ogiltigt svar när den fungerade som en gateway eller proxyserver" returneras det här felet av HDInsight-tjänsten.

- **Rekommendation**: om 502-fel, är det mesta av tiden detta beror på att Ambari-serverns process stängdes av. Du kan starta om Ambari-tjänsterna genom att starta om Head-noden.  

    1. Anslut till en nod i HDInsight med hjälp av SSH.
    2. Identifiera din aktiva Head Node-värd genom att köra ping headnodehost.
    3. Anslut till din aktiva Head-nod som Ambari-server på den aktiva Head-noden med SSH.  
    4. Starta om den aktiva Head-noden.

        Mer information finns i fel söknings dokumentation för Azure HDInsight, till exempel: [AMBARI UI 502-fel](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [RpcTimeoutException för Apache Spark Thrift-Server](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception), [fel sökning av felaktiga Gateway-fel i Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br>

- **Orsak**: när fel meddelandet innehåller ett meddelande som liknar "Det gick inte att hantera begäran om att skicka jobb som Templeton-tjänsten är upptagen med för många begäran om att skicka jobb eller Queue rot. joblauncher har redan 500 program, det går inte att godkänna att programmet skickas, vilket innebär att för många jobb skickas till HDInsight samtidigt.

- **Rekommendation**: Överväg att begränsa antalet samtidiga jobb som skickats till HDInsight. Referera till Data Factory aktivitets samtidighet om jobben skickas av samma aktivitet. Ändra utlösare så att samtidiga pipelines körs sprids över tid. Se [HDInsight-dokumentationen](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors) för att `templeton.parallellism.job.submit` justera som ett fel förslag.


### <a name="error-code--2301"></a>Felkod: 2301

- **Meddelande**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Orsak**: HDInsight-kluster eller-tjänsten har problem.


- **Rekommendation**: det här felet uppstår när ADF inte får svar från HDInsight-kluster vid försök att hämta status för jobbet som körs. Det kan vara orsaken till problem i själva klustret, eller så kan HDInsight-tjänsten ha ett avbrott. Se fel söknings dokumentation för HDInsight https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guidepå eller kontakta supporten om du behöver hjälp.
                


### <a name="error-code--2302"></a>Felkod: 2302

- **Meddelande**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Orsak**: jobbet skickades till HDI-klustret och misslyckades där.

- **Rekommendation**: Följ länken för garn loggar i aktiviteten kör utdata och leta efter felen i HDI-utdata. Kontakta HDInsight-teamet om du behöver hjälp.


### <a name="error-code--2303"></a>Felkod: 2303

- **Meddelande**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Orsak**: jobbet skickades till HDI-klustret och misslyckades där.

- **Rekommendation**: Följ länken för garn loggar i aktiviteten kör utdata och leta efter felen i HDI-utdata. Försök igen eller kontakta HDInsight-teamet om du behöver hjälp.


### <a name="error-code--2304"></a>Felkod: 2304

- **Meddelande**:`MSI authentication is not supported on storages for HDI activities.`

- **Orsak**: de länkade lagrings tjänster som används i HDI länkade tjänst-eller HDI-aktivitet konfigureras med MSI-autentisering vilket inte stöds.

- **Rekommendation**: ange fullständiga anslutnings strängar för lagrings konton som används i HDI länkade tjänst-eller HDI-aktiviteter.


### <a name="error-code--2305"></a>Felkod: 2305

- **Meddelande**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Orsak**: anslutnings informationen för HDI-klustret är felaktig, den angivna användaren har inte behörighet att utföra den begärda åtgärden, eller så har HDInsight-tjänsten problem med att svara på begär Anden från ADF.

- **Rekommendation**: kontrol lera att användar informationen är korrekt. Kontrol lera också att Ambari-ANVÄNDARGRÄNSSNITTET för HDI-klustret kan öppnas i en webbläsare från den virtuella datorn där IR är installerat vid en lokal IR-installation, eller om den kan öppnas från vilken dator som helst i Azure IR.


### <a name="error-code--2306"></a>Felkod: 2306

- **Meddelande**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Orsak**: JSON-filen som angavs för skript åtgärden är ogiltig.


- **Rekommendation**: fel meddelandet bör hjälpa till att identifiera problemet. Åtgärda JSON-konfigurationen och försök igen. Mer https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service information finns i.
                

### <a name="error-code--2310"></a>Felkod: 2310

- **Meddelande**:`Failed to submit Spark job. Error: '%message;'`

- **Orsak**: ADF försökte skapa en batch i ett Spark-kluster med livy API (livy/batch), men tog emot ett fel.

- **Rekommendation**: åtgärda problemet genom att följa fel meddelandet. Om det inte finns tillräckligt med information för att lösa problemet kan du kontakta HDI-teamet och ge dem batch-ID och jobb-ID som finns i sidan aktivitets körnings utdata på sidan ADF-övervakning.


### <a name="error-code--2312"></a>Felkod: 2312

- **Meddelande**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Orsak**: det gick inte att utföra jobbet på HDInsight Spark-kluster.

- **Rekommendation**: Följ länkarna i sidan aktivitets körnings utdata på sidan ADF-övervakning för att felsöka körningen på HDInsight Spark-klustret. Kontakta HDInsight support team om du behöver mer hjälp.


### <a name="error-code--2313"></a>Felkod: 2313

- **Meddelande**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Orsak**: gruppen togs bort i HDInsight Spark-klustret.

- **Rekommendation**: Felsöka batchar i HDInsight Spark-klustret. Kontakta HDInsight-supporten om du behöver mer hjälp. 


### <a name="error-code--2328"></a>Felkod: 2328

- **Meddelande**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**:`The error message should show the details of what went wrong.`

- **Rekommendation**: fel meddelandet bör hjälpa till att felsöka problemet.


### <a name="error-code--2329"></a>Felkod: 2329

- **Meddelande**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: fel meddelandet ska visa information om vad som gått fel.

- **Rekommendation**: fel meddelandet bör hjälpa till att felsöka problemet.


### <a name="error-code--2331"></a>Felkod: 2331

- **Meddelande**:`The file path should not be null or empty.`

- **Orsak**: den angivna fil Sök vägen är tom.

- **Rekommendation**: Ange en sökväg till en fil som finns.


### <a name="error-code--2340"></a>Felkod: 2340

- **Meddelande**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Orsak**: HDInsightOnDemand länkade tjänst stöder inte körning via SelfHosted IR.

- **Rekommendation**: välj en Azure IR och försök igen.


### <a name="error-code--2341"></a>Felkod: 2341

- **Meddelande**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Orsak**: den angivna webb adressen är inte i rätt format.

- **Rekommendation**: åtgärda kluster-URL: en och försök igen.


### <a name="error-code--2342"></a>Felkod: 2342

- **Meddelande**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Orsak**: de angivna autentiseringsuppgifterna är fel för klustret eller också har det uppstått ett nätverks konfigurations-eller anslutnings problem eller också har det uppstått problem med att ansluta till klustret.

- **Rekommendation**:  
      1. Kontrol lera att autentiseringsuppgifterna är korrekta genom att öppna HDInsight-klustrets Ambari-användargränssnitt i en webbläsare.
      2. Om klustret är i VNet och lokal IR används, ska HDI-URL: en till vara den privata URL: en i virtuella nätverk, vilket innebär att den ska ha "-int" efter kluster namnet. Till exempel "https://mycluster.azurehdinsight.net/" ska ändras till "https://mycluster-int.azurehdinsight.net/".
      2. Om klustret är i VNet används egen värd-IR och den privata URL: en användes, och anslutningen fortfarande misslyckades. den virtuella datorn där IR har installerats hade problem med att ansluta till HDI. Anslut till den virtuella datorn där IR är installerat och öppna Ambari-ANVÄNDARGRÄNSSNITTET i en webbläsare. Använd den privata URL: en för klustret. Den här anslutningen bör fungera från webbläsaren. Om den inte gör det kontaktar du HDInsight support team för ytterligare hjälp.
      3. Om IR med egen värd inte används bör HDI-klustret vara tillgängligt offentligt. Öppna Ambari-ANVÄNDARGRÄNSSNITTET i en webbläsare och se till att det öppnas. Om det finns några problem med klustret eller tjänsterna på den kontaktar du HDInsight support team för att få hjälp.
      I allmänhet måste HDI-kluster-URL: en som används i den länkade ADF-tjänsten vara tillgänglig för ADF IR (lokal eller Azure) för att test anslutningen ska kunna skickas och för att körningen ska fungera. Detta kan vara enkelt att verifiera genom att öppna URL: en från en webbläsare, antingen från en virtuell dator eller någon offentlig dator.
    


### <a name="error-code--2343"></a>Felkod: 2343

- **Meddelande**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Orsak**: antingen användar namnet eller lösen ordet är tomt.

- **Rekommendation**: ange rätt autentiseringsuppgifter för att ansluta till HDI och försök igen.


### <a name="error-code--2345"></a>Felkod: 2345

- **Meddelande**:`Failed to read the content of the hive script. Error: '%message;'`

- **Orsak**: skript filen finns inte eller så kunde inte ADF ansluta till platsen för skriptet.

- **Rekommendation**: kontrol lera att skriptet finns och att den associerade länkade tjänsten har rätt autentiseringsuppgifter för anslutningen.


### <a name="error-code--2346"></a>Felkod: 2346

- **Meddelande**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Orsak**: ADF försökte upprätta en ODBC-anslutning till HDI-klustret och misslyckades med felet.

- **Rekommendation**: fel meddelandet och felkoden bör hjälpa till att felsöka ODBC-anslutningsfel. Om de inte räcker för att lösa problemet kan du kontakta Azure HDInsight-teamet för support.


### <a name="error-code--2347"></a>Felkod: 2347

- **Meddelande**:`Hive execution through ODBC failed with error message '%message;'.`

- **Orsak**: ADF skickade Hive-skriptet för körning till HDI-klustret via ODBC-anslutningen och skriptet har misslyckats på HDI.

- **Rekommendation**: körningen av Hive-skriptet misslyckades i HDI-kluster och fel meddelande och felkod bör vara till hjälp vid fel sökning. Om de inte räcker för att lösa problemet kan du kontakta Azure HDInsight-teamet för support.


### <a name="error-code--2348"></a>Felkod: 2348

- **Meddelande**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Orsak**: de länkade tjänst egenskaperna för lagring har inte angetts korrekt.

- **Rekommendation**: endast fullständiga anslutnings strängar stöds i den länkade huvud lagrings tjänsten för HDI-aktiviteter. Se till att du inte använder MSI-autentisering eller program.


### <a name="error-code--2350"></a>Felkod: 2350

- **Meddelande**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Orsak**: autentiseringsuppgifterna som angavs för att ansluta till lagrings platsen där filerna ska placeras är felaktiga, eller så finns inte filerna där.

- **Rekommendation**: det här felet uppstår när ADF förbereder förberedelse steg för HDI-aktiviteter. Det försöker kopiera filer till huvud lagringen innan jobbet skickas till HDI. Kontrol lera att filerna finns på den angivna platsen. lagrings anslutningen är korrekt. ADF HDI-aktiviteter stöder inte MSI-autentisering på lagrings konton som är relaterade till HDI-aktiviteter, så se till att dessa länkade tjänster har fullständiga nycklar eller använder Azure Key Vault.


### <a name="error-code--2351"></a>Felkod: 2351

- **Meddelande**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Orsak**: filen finns inte på den angivna sökvägen.

- **Rekommendation**: kontrol lera om filen verkligen finns och den länkade tjänsten med anslutnings information som pekar på den här filen har rätt autentiseringsuppgifter.


### <a name="error-code--2352"></a>Felkod: 2352

- **Meddelande**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Orsak**: egenskaperna för den länkade fil lagrings tjänsten har inte angetts korrekt.

- **Rekommendation**: kontrol lera att egenskaperna för den länkade fil lagrings tjänsten är korrekt konfigurerade.


### <a name="error-code--2353"></a>Felkod: 2353

- **Meddelande**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Orsak**: de länkade tjänst egenskaperna för skript lagring har inte angetts korrekt.

- **Rekommendation**: kontrol lera att egenskaperna för den länkade skript lagrings tjänsten är korrekt konfigurerade.


### <a name="error-code--2354"></a>Felkod: 2354

- **Meddelande**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Orsak**: den länkade tjänst typen för lagring stöds inte av aktiviteten.

- **Rekommendation**: kontrol lera att den valda länkade tjänsten har en av de typer som stöds för aktiviteten. HDI-aktiviteter stöder AzureBlobStorage-och AzureBlobFSStorage-länkade tjänster. Mer information finns i [jämföra lagrings alternativ för användning med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options)


### <a name="error-code--2355"></a>Felkod: 2355

- **Meddelande**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Orsak**: den angivna commandEnvironment är felaktig.

- **Rekommendation**:  
      Kontrol lera att det angivna värdet liknar \": commandEnvironment\": [ \"variableName = variableValue\" ] och varje variabel visas bara en gång i listan.
    


### <a name="error-code--2356"></a>Felkod: 2356

- **Meddelande**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Orsak**: variabeln angavs två gånger i commandEnvironment.

- **Rekommendation**:  
      Kontrol lera att det angivna värdet liknar \": commandEnvironment\": [ \"variableName = variableValue\" ] och varje variabel visas bara en gång i listan.
    


### <a name="error-code--2357"></a>Felkod: 2357

- **Meddelande**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Orsak**: de angivna autentiseringsuppgifterna är felaktiga.

- **Rekommendation**: kontrol lera anslutnings informationen i ADLS gen 1-länkade tjänst och kontrol lera att test anslutningen lyckas.


### <a name="error-code--2358"></a>Felkod: 2358

- **Meddelande**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Orsak**: det angivna värdet för den obligatoriska egenskapen TimeToLive har ett ogiltigt format. 

- **Rekommendation**: uppdatera värdet till ett föreslaget intervall och försök igen.


### <a name="error-code--2359"></a>Felkod: 2359

- **Meddelande**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Orsak**: det angivna värdet för egenskapen roles är ogiltigt.

- **Rekommendation**: uppdatera värdet till ett av förslagen och försök igen.


### <a name="error-code--2360"></a>Felkod: 2360

- **Meddelande**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Orsak**: den angivna anslutnings strängen för HCatalogLinkedService är ogiltig.

- **Rekommendation**: uppdatera värdet till rätt Azure SQL-anslutningssträng och försök igen.


### <a name="error-code--2361"></a>Felkod: 2361

- **Meddelande**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Orsak**: det gick inte att skapa klustret och ADF fick inget fel tillbaka från HDInsight-tjänsten.

- **Rekommendation**: öppna Azure Portal och försök hitta HDI-resursen med det angivna namnet och kontrol lera etablerings statusen. Kontakta HDInsight support team för ytterligare hjälp.


### <a name="error-code--2362"></a>Felkod: 2362

- **Meddelande**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Orsak**: den tillhandahållna ytterligare lagringen var inte Azure Blob Storage.

- **Rekommendation**: Tillhandahåll Azure Blob Storage-konto som ytterligare lagrings utrymme för en länkad HDInsight-tjänst på begäran.



## <a name="web-activity"></a>Webbaktivitet

### <a name="error-code--2128"></a>Felkod: 2128

- **Meddelande**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Orsak**: nätverks anslutning, DNS-fel, verifiering av Server certifikat eller tids gräns.

- **Rekommendation**: kontrol lera att slut punkten som du försöker trycka på svarar på begär Anden. Du kan använda verktyg som Fiddler/Postman.


### <a name="error-code--2108"></a>Felkod: 2108

- **Meddelande**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Orsak**: begäran misslyckades på grund av ett underliggande problem, till exempel nätverks anslutning, DNS-fel, verifiering av Server certifikat eller tids gräns.

- **Rekommendation**: Använd Fiddler/Postman för att verifiera begäran.
<br>


#### <a name="more-details"></a>Mer information
Så här använder du Fiddler för att skapa en HTTP-session för det övervakade webb programmet:

1. Ladda ned, installera och öppna [Fiddler](https://www.telerik.com/download/fiddler).

1. Om ditt webb program använder https går du till **verktyg** > **Fiddler alternativ** > **https**. Välj **avbilda https ansluter** och **dekryptera HTTPS-trafik**.

   ![Fiddler-alternativ](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Om ditt program använder TLS/SSL-certifikat, lägger du till Fiddler-certifikatet på enheten. Gå till **verktyg** > **Fiddler alternativ** > **https** > **åtgärder** > **Exportera rot certifikat till Skriv bordet**.

1. Inaktivera hämtning genom att gå till **fil** > **hämtnings trafik**. Eller tryck på **F12**.

1. Rensa webbläsarens cacheminne så att alla cachelagrade objekt tas bort och måste laddas ned igen.

1. Skapa en begäran:

   1. Välj fliken **Composer** .

   1. Ange HTTP-metod och URL.
   
   1. Lägg till rubriker och en begär ande text om du behöver.

   1. Välj **Kör**.

1. Aktivera trafik insamlingen igen och slutför den felande transaktionen på sidan.

1. Gå till **filen** > **Spara** > **alla sessioner**.

Mer information finns i [komma igång med Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)


            
