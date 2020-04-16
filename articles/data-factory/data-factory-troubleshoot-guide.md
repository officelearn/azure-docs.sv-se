---
title: Felsöka Azure Data Factory | Microsoft-dokument
description: Lär dig hur du felsöker externa kontrollaktiviteter i Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c9a1ac831c4300c0523717fddc1fa53417068b89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416564"
---
# <a name="troubleshoot-azure-data-factory"></a>Felsöka Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs vanliga felsökningsmetoder för externa kontrollaktiviteter i Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Kopplings- och kopieringsaktivitet

Information om anslutningsproblem, t.ex. [Troubleshoot Azure Data Factory Connectors](connector-troubleshoot-guide.md)
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Felkod: 3200

- **Meddelande**: Fel 403.

- **Orsak:**`The Databricks access token has expired.`

- **Rekommendation:** Som standard är Azure Databricks-åtkomsttoken giltig i 90 dagar. Skapa en ny token och uppdatera den länkade tjänsten.


### <a name="error-code--3201"></a>Felkod: 3201

- **Meddelande:**`Missing required field: settings.task.notebook_task.notebook_path.`

- **Orsak:**`Bad authoring: Notebook path not specified correctly.`

- **Rekommendation**: Ange sökvägen till anteckningsboken i databricks-aktiviteten.

<br/>  

- **Meddelande:**`Cluster... does not exist.`

- **Orsak:**`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Rekommendation**: Kontrollera att Databricks-klustret finns.

<br/>  

- **Meddelande:**`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Orsak:**`Bad authoring.`

- **Rekommendation**: Ange antingen absoluta sökvägar för `dbfs:/folder/subfolder/foo.py` arbetsyteadresseringsscheman eller för filer som lagras i Databricks-filsystemet.

<br/>  

- **Meddelande:**`{0} LinkedService should have domain and accessToken as required properties.`

- **Orsak:**`Bad authoring.`

- **Rekommendation**: Verifiera definitionen av [länkade tjänster](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Meddelande:**`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Orsak:**`Bad authoring.`

- **Rekommendation**: Verifiera definitionen av [länkade tjänster](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Meddelande:**`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Orsak:**`Bad authoring.`

- **Rekommendation**: Se felmeddelandet.

<br/>

### <a name="error-code--3202"></a>Felkod: 3202

- **Meddelande:**`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Orsak:**`Too many Databricks runs in an hour.`

- **Rekommendation**: Kontrollera alla pipelines som använder den här Databricks-arbetsytan för att skapa jobb.  Om pipelines har startat för många Databricks-körningar sammanlagt migrerar du vissa pipelines till en ny arbetsyta.

<br/>  

- **Meddelande:**`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Orsak:**`Authoring error: No value provided for the parameter.`

- **Rekommendation**: Kontrollera rörledningen JSON och se till att alla parametrar i databasenParameters anger ett icke-stort värde.

<br/>  

- **Meddelande** `User: `: SimpleUserContext{userId=...,user@company.comname= , orgId=...}` is not   authorized to access cluster.`

- **Orsak**: Användaren som genererade åtkomsttoken får inte komma åt databricks-klustret som anges i den länkade tjänsten.

- **Rekommendation**: Se till att användaren har de behörigheter som krävs på arbetsytan.


### <a name="error-code--3203"></a>Felkod: 3203

- **Meddelande:**`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Orsak**: Klustret avbröts. För interaktiva kluster kan detta vara ett konkurrensvillkor.

- **Rekommendation**: Det bästa sättet att undvika det här felet är att använda jobbkluster.


### <a name="error-code--3204"></a>Felkod: 3204

- **Meddelande:**`Job execution failed.`

- **Orsak**: Felmeddelanden anger olika problem, till exempel ett oväntat klustertillstånd eller en viss aktivitet. Oftast visas inget felmeddelande alls.

- **Rekommendation**: Saknas
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Följande tabell gäller U-SQL.
      
### <a name="error-code--2709"></a>Felkod: 2709

- **Meddelande:**`The access token is from the wrong tenant.`

- **Orsak**: Felaktig Azure Active Directory (Azure AD) klientorganisation.

- **Rekommendation:** Felaktig Azure Active Directory (Azure AD) klientorganisation.

<br/>

- **Meddelande:**`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Orsak**: Det här felet orsakas av begränsning på DataSjöanalys.

- **Rekommendation**: Minska antalet inskickade jobb till Data Lake Analytics genom att ändra datafabriksutlösare och inställningar för samtidighet på aktiviteter. Eller öka gränserna för DataSjöanalys.

<br/>  

- **Meddelande:**`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Orsak**: Det här felet orsakas av begränsning på DataSjöanalys.

- **Rekommendation**: Minska antalet inskickade jobb till Data Lake Analytics genom att ändra datafabriksutlösare och inställningar för samtidighet på aktiviteter. Eller öka gränserna för DataSjöanalys.


### <a name="error-code--2705"></a>Felkod: 2705

- **Meddelande:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Orsak**: Tjänstens huvudnamn eller certifikat har inte åtkomst till filen i lagring.

- **Rekommendation**: Kontrollera att tjänstens huvudnamn eller certifikat som användaren tillhandahåller för DataSjöanalys-jobb har åtkomst till DataSjöanalys-kontot och standardinstansen DataSjölagring från rotmappen.


### <a name="error-code--2711"></a>Felkod: 2711

- **Meddelande:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Orsak**: Tjänstens huvudnamn eller certifikat har inte åtkomst till filen i lagring.

- **Rekommendation**: Kontrollera att tjänstens huvudnamn eller certifikat som användaren tillhandahåller för DataSjöanalys-jobb har åtkomst till DataSjöanalys-kontot och standardinstansen DataSjölagring från rotmappen.

<br/>  

- **Meddelande:**`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Orsak**: Sökvägen till U-SQL-filen är fel eller så har inte de länkade tjänstautentiseringsuppgifterna åtkomst.

- **Rekommendation**: Verifiera sökvägen och autentiseringsuppgifterna som anges i den länkade tjänsten.


### <a name="error-code--2704"></a>Felkod: 2704

- **Meddelande:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Orsak**: Tjänstens huvudnamn eller certifikat har inte åtkomst till filen i lagring.

- **Rekommendation**: Kontrollera att tjänstens huvudnamn eller certifikat som användaren tillhandahåller för DataSjöanalys-jobb har åtkomst till DataSjöanalys-kontot och standardinstansen DataSjölagring från rotmappen.


### <a name="error-code--2707"></a>Felkod: 2707

- **Meddelande:**`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Orsak**: DataSjöanalyskontot i den länkade tjänsten är fel.

- **Rekommendation**: Kontrollera att rätt konto tillhandahålls.


### <a name="error-code--2703"></a>Felkod: 2703

- **Meddelande:**`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Orsak**: Felet kommer från Data Lake Analytics.

- **Rekommendation**: Ett fel som exemplet innebär att jobbet skickades till Data Lake Analytics, och skriptet där misslyckades. Undersök i Data Lake Analytics. Gå till DataSjö Analytics-kontot i portalen och leta efter jobbet med hjälp av aktivitetskörnings-ID:t datafabrik (inte pipeline-körnings-ID). Jobbet där ger mer information om felet och hjälper dig att felsöka. Om lösningen inte är klar kontaktar du supportteamet för DataSjöanalys och anger jobbadressen, som innehåller ditt kontonamn och jobb-ID.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Felkod: 3602

- **Meddelande:**`Invalid HttpMethod: '%method;'.`

- **Orsak**: Http-metod som anges i aktivitetsnyttolasten stöds inte av Azure Function Activity.

- **Rekommendation**: Http-metoderna som stöds är PUT, POST, GET, DELETE, OPTIONS, HEAD och TRACE.


### <a name="error-code--3603"></a>Felkod: 3603

- **Meddelande:**`Response Content is not a valid JObject.`

- **Orsak**: Azure-funktionen som anropades returnerade inte en JSON Nyttolast i svaret. ADF Azure-funktionsaktivitet stöder endast JSON-svarsinnehåll.

- **Rekommendation:** Uppdatera Azure-funktionen för att returnera en giltig JSON Nyttolast t.ex.\"\"\"\"


### <a name="error-code--3606"></a>Felkod: 3606

- **Meddelande**: Funktionsnyckel för Azure-funktion saknas.

- **Orsak**: Azure-funktionsaktivitetsdefinitionen är inte klar.

- **Rekommendation:** Kontrollera indata AzureFunction aktivitet JSON definition har egenskapen heter 'functionKey'.


### <a name="error-code--3607"></a>Felkod: 3607

- **Meddelande:**`Azure function activity missing function name.`

- **Orsak**: Azure-funktionsaktivitetsdefinitionen är inte klar.

- **Rekommendation:** Kontrollera indata AzureFunction aktivitet JSON definition har egenskapen heter 'functionName'.


### <a name="error-code--3608"></a>Felkod: 3608

- **Meddelande:**`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Orsak**: Azure-funktionsinformation i aktivitetsdefinitionen kan vara felaktig.

- **Rekommendation**: Åtgärda azure-funktionsinformationen och försök igen.


### <a name="error-code--3609"></a>Felkod: 3609

- **Meddelande:**`Azure function activity missing functionAppUrl.`

- **Orsak**: Azure-funktionsaktivitetsdefinitionen är inte klar.

- **Rekommendation:** Kontrollera indata AzureFunction aktivitet JSON definition har egenskapen heter 'functionAppUrl'.


### <a name="error-code--3610"></a>Felkod: 3610

- **Meddelande:**`There was an error while calling endpoint.`

- **Orsak**: Funktions-URL:en kan vara felaktig.

- **Rekommendation**: Kontrollera att värdet för "functionAppUrl" i aktiviteten JSON är korrekt och försök igen.


### <a name="error-code--3611"></a>Felkod: 3611

- **Meddelande:**`Azure function activity missing Method in JSON.`

- **Orsak**: Azure-funktionsaktivitetsdefinitionen är inte klar.

- **Rekommendation:** Kontrollera den indata AzureFunction-aktiviteten JSON-definitionen har egenskapen "metod".


### <a name="error-code--3612"></a>Felkod: 3612

- **Meddelande:**`Azure function activity missing LinkedService definition in JSON.`

- **Orsak**: Azure-funktionsaktivitetsdefinitionen är inte klar.

- **Rekommendation:** Kontrollera den indata AzureFunction-aktiviteten som JSON-definitionen har länkat tjänstinformation.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Felkod: 4101

- **Meddelande:**`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Orsak**: Felaktigt format eller definition av egenskap %propertyName saknas.

- **Rekommendation**: Kontrollera om aktiviteten %activityName har egenskapen %propertyName, definierat med korrekta data.


### <a name="error-code--4110"></a>Felkod: 4110

- **Meddelande:**`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Orsak**: AzureMLExecutePipeline-aktivitetsdefinitionen är inte klar.

- **Rekommendation**: Kontrollera om den indata AzureMLExecutePipeline-aktiviteten JSON-definitionen har länkat tjänstinformation.


### <a name="error-code--4111"></a>Felkod: 4111

- **Meddelande:**`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Orsak**: Felaktig aktivitetsdefinition.

- **Rekommendation**: Kontrollera om den indata AzureMLExecutePipeline-aktiviteten JSON-definitionen har korrigerat länkade tjänstinformation.


### <a name="error-code--4112"></a>Felkod: 4112

- **Meddelande:**`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Orsak**: Felaktigt format eller definition av egenskap %propertyName saknas.

- **Rekommendation**: Kontrollera om den länkade tjänsten har egenskapen %propertyName, definierad med korrekta data.


### <a name="error-code--4121"></a>Felkod: 4121

- **Meddelande:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: Autentiseringsuppgifter som används för att komma åt Azure Machine Learning har upphört att gälla.

- **Rekommendation**: Kontrollera att autentiseringsuppgifterna är giltiga och försök igen


### <a name="error-code--4122"></a>Felkod: 4122

- **Meddelande:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: Autentiseringsuppgifter som tillhandahålls i Azure Machine Learning Linked Service är ogiltig eller har inte behörighet för åtgärden.

- **Rekommendation**: Kontrollera att autentiseringsuppgifterna i Länkad tjänst är giltiga och har behörighet att komma åt Azure Machine Learning.


### <a name="error-code--4123"></a>Felkod: 4123

- **Meddelande:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: Egenskaper för aktiviteten, till exempel pipelineParametrar, är ogiltiga för Azure ML-pipelinen.

- **Rekommendation:** Kontrollera värdet för aktivitetsegenskaper för att matcha förväntad nyttolast för den publicerade Azure ML-pipelinen som anges i länkad tjänst.


### <a name="error-code--4124"></a>Felkod: 4124

- **Meddelande:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak**: Den publicerade Azure ML-pipelineslutpunkten finns inte.

- **Rekommendation:** Kontrollera att den publicerade Azure Machine Learning-pipeline-slutpunkten som anges i Länkad tjänst finns i Azure Machine Learning.


### <a name="error-code--4125"></a>Felkod: 4125

- **Meddelande:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Orsak:** Serverfel på Azure Machine Learning.

- **Rekommendation**: Försök igen senare. Kontakta Azure Machine Learning-teamet om du vill ha hjälp om problemet kvarstår.


### <a name="error-code--4126"></a>Felkod: 4126

- **Meddelande:**`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Orsak**: Azure ML pipeline-körning misslyckades.

- **Rekommendation:** Kontrollera i Azure Machine Learning för fler felloggar och åtgärda ML-pipelinen.



## <a name="common"></a>Common

### <a name="error-code--2103"></a>Felkod: 2103

- **Meddelande:**`Please provide value for the required property '%propertyName;'.`

- **Orsak**: Värdet för egenskapen har inte angetts, men det krävs i scenariot.

- **Rekommendation**: Ange värdet från meddelandet och försök igen.


### <a name="error-code--2104"></a>Felkod: 2104

- **Meddelande:**`The type of the property '%propertyName;' is incorrect.`

- **Orsak**: Typen av den angivna egenskapen är inte som förväntat.

- **Rekommendation**: Åtgärda typen av egenskap och försök igen.


### <a name="error-code--2105"></a>Felkod: 2105

- **Meddelande:**`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Orsak**: Värdet för egenskapen är ogiltigt eller har inte det förväntade formatet.

- **Rekommendation**: Slå upp dokumentationen för egenskapen och se till att värdet som har förväntat format och typ.


### <a name="error-code--2106"></a>Felkod: 2106

- **Meddelande:**`The storage connection string is invalid. %errorMessage;`

- **Orsak**: Anslutningssträngen för lagringen är ogiltig eller har felaktigt format.

- **Rekommendation:** Gå till Azure-portalen, hitta ditt lagringsutrymme, kopiera anslutningssträngen och klistra in den länkade tjänsten och försök igen.


### <a name="error-code--2108"></a>Felkod: 2108

- **Meddelande:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Orsak**: Begäran misslyckades på grund av ett underliggande problem som nätverksanslutning, DNS-fel, validering av servercertifikat eller timeout.

- **Rekommendation**: Använd Fiddler/Postman för att validera begäran.


### <a name="error-code--2110"></a>Felkod: 2110

- **Meddelande:**`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Orsak**: Den länkade tjänst som angavs i aktiviteten var fel.

- **Rekommendation**: Kontrollera att den länkade tjänsttypen är en av de typer som stöds för aktiviteten. För HDI-aktiviteter kan till exempel den länkade tjänsttypen vara HDInsight eller HDInsightOnDemand.


### <a name="error-code--2111"></a>Felkod: 2111

- **Meddelande:**`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Orsak**: Typen av den angivna egenskapen är inte som förväntat.

- **Rekommendation**: Åtgärda typen av egenskap och försök igen.


### <a name="error-code--2112"></a>Felkod: 2112

- **Meddelande:**`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Orsak**: Molntypen stöds inte eller kunde inte fastställas för lagring från EndpointSuffix.

- **Rekommendation**: Använd lagring i ett annat moln och försök igen.


### <a name="error-code--2128"></a>Felkod: 2128

- **Meddelande:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Orsak:** Nätverksanslutning, DNS-fel, validering av servercertifikat eller timeout.

- **Rekommendation**: Verifiera att slutpunkten som du försöker träffa svarar på begäranden. Du kan använda verktyg som Fiddler / Postman.



## <a name="custom"></a>Anpassat

Följande tabell gäller för Azure Batch.
      
### <a name="error-code--2500"></a>Felkod: 2500

- **Meddelande:**`Hit unexpected exception and execution failed.`

- **Orsak:**`Can't launch command, or the program returned an error code.`

- **Rekommendation**: Kontrollera att den körbara filen finns. Om programmet startade kontrollerar du att *stdout.txt* och *stderr.txt* har överförts till lagringskontot. Det är en bra idé att avge kopiösa loggar i koden för felsökning.


### <a name="error-code--2501"></a>Felkod: 2501

- **Meddelande:**`Cannot access user batch account; please check batch account settings.`

- **Orsak**: Felaktig batchåtkomstnyckel eller poolnamn.

- **Rekommendation**: Verifiera poolnamnet och batchåtkomstnyckeln i den länkade tjänsten.


### <a name="error-code--2502"></a>Felkod: 2502

- **Meddelande:**`Cannot access user storage account; please check storage account settings.`

- **Orsak**: Felaktigt lagringskontonamn eller åtkomstnyckel.

- **Rekommendation**: Verifiera lagringskontonamnet och åtkomstnyckeln i den länkade tjänsten.


### <a name="error-code--2504"></a>Felkod: 2504

- **Meddelande:**`Operation returned an invalid status code 'BadRequest'.`

- **Orsak**: För många filer i mappenPath för den anpassade aktiviteten. Den totala storleken på resourceFiles får inte vara mer än 32 768 tecken.

- **Rekommendation**: Ta bort onödiga filer. Eller zip dem och lägga till en packa upp kommando för att extrahera dem. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Felkod: 2505

- **Meddelande:**`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Orsak:** Anpassade aktiviteter stöder endast lagringskonton som använder en åtkomstnyckel.

- **Rekommendation**: Se felbeskrivningen.


### <a name="error-code--2507"></a>Felkod: 2507

- **Meddelande:**`The folder path does not exist or is empty: ...`

- **Orsak**: Inga filer finns i lagringskontot vid den angivna sökvägen.

- **Rekommendation**: Mappsökvägen måste innehålla de körbara filer som du vill köra.


### <a name="error-code--2508"></a>Felkod: 2508

- **Meddelande:**`There are duplicate files in the resource folder.`

- **Orsak**: Flera filer med samma namn finns i olika undermappar i folderPath.

- **Rekommendation**: Anpassade aktiviteter förenklar mappstrukturen under folderPath.  Om du behöver bevara mappstrukturen, zip filerna och extrahera dem i Azure Batch med hjälp av ett packa upp kommando. Använd till exempel`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Felkod: 2509

- **Meddelande:**`Batch   url ... is invalid; it must be in Uri format.`

- **Orsak:** Batch-URL:er måste likna`https://mybatchaccount.eastus.batch.azure.com`

- **Rekommendation**: Se felbeskrivningen.


### <a name="error-code--2510"></a>Felkod: 2510

- **Meddelande:**`An   error occurred while sending the request.`

- **Orsak**: Batch-URL:en är ogiltig.

- **Rekommendation**: Verifiera batch-URL:en.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Felkod: 200

- **Meddelande:**`Unexpected error happened: '%error;'.`

- **Orsak**: Det finns ett internt serviceproblem.

- **Rekommendation**: Kontakta ADF-supporten för ytterligare hjälp.


### <a name="error-code--201"></a>Felkod: 201

- **Meddelande:**`JobType %jobType; is not found.`

- **Orsak**: Det finns en ny jobbtyp som inte stöds av ADF.

- **Rekommendation**: Kontakta ADF supportteam för ytterligare hjälp.


### <a name="error-code--202"></a>Felkod: 202

- **Meddelande:**`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: Felmeddelandet ska visa information om vad som gick fel.

- **Rekommendation**: Felmeddelandet bör hjälpa till att felsöka problemet. Om det inte finns tillräckligt med information, vänligen kontakta ADF-supporten för ytterligare hjälp.


### <a name="error-code--203"></a>Felkod: 203

- **Meddelande:**`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: Felmeddelandet ska visa information om vad som gick fel.

- **Rekommendation**: Felmeddelandet bör hjälpa till att felsöka problemet. Om det inte finns tillräckligt med information, vänligen kontakta ADF-supporten för ytterligare hjälp.


### <a name="error-code--204"></a>Felkod: 204

- **Meddelande:**`The resumption token is missing for runId '%runId;'.`

- **Orsak**: Det finns ett internt serviceproblem.

- **Rekommendation**: Kontakta ADF-supporten för ytterligare hjälp.


### <a name="error-code--205"></a>Felkod: 205

- **Meddelande:**`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Orsak**: Det uppstod ett fel när HDI-klustret på begäran skapades.

- **Rekommendation**: Kontakta ADF-supporten för ytterligare hjälp.


### <a name="error-code--206"></a>Felkod: 206

- **Meddelande:**`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Orsak**: Det uppstod ett internt problem med tjänsten som orsakade detta.

- **Rekommendation**: Detta kan vara en tillfällig fråga. Försök igen och kontakta ADF-supporten om problemet kvarstår för ytterligare hjälp.


### <a name="error-code--207"></a>Felkod: 207

- **Meddelande:**`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Orsak**: Det uppstod ett internt fel när regionen försökte hitta regionen från det primära lagringskontot.

- **Rekommendation**: Prova ett annat lagringsutrymme. Om detta inte är en godtagbar lösning kontaktar du ADF:s supportteam för ytterligare hjälp.


### <a name="error-code--208"></a>Felkod: 208

- **Meddelande:**`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Orsak**: Det uppstod ett internt fel när du försökte läsa tjänstens huvudnamn eller instansiera MSI-autentiseringen.

- **Rekommendation**: Överväg att tillhandahålla ett tjänsthuvudnamn som har behörighet att skapa ett HDInsight-kluster i den angivna prenumerationen och försök igen. Om detta inte är en godtagbar lösning kontaktar du ADF:s supportteam för ytterligare hjälp.


### <a name="error-code--2300"></a>Felkod: 2300

- **Meddelande:**`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Orsak**: När felmeddelandet innehåller ett meddelande som liknar "Fjärrnamnet kunde inte matchas.", kan det innebära att den angivna kluster-URI:n är ogiltig.


- **Rekommendation**: Kontrollera att klustret inte har tagits bort och att den angivna URI:n är korrekt. När du öppnar URI i en webbläsare bör du se Ambari UI. Om klustret finns i ett virtuellt nätverk ska URI vara den privata URI:n. Om du vill öppna den använder du en virtuell dator som ingår i samma virtuella nätverk. Mer information finns i [den här](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Orsak**: När felmeddelandet innehåller ett meddelande som liknar "En aktivitet avbröts.", betyder det att timeingången för jobböverföringen har gett time out.

- **Rekommendation**: Problemet kan vara antingen allmän HDInsight-anslutning eller nätverksanslutning. Bekräfta först att HDInsight Ambari UI är tillgängligt från alla webbläsare. Bekräfta att dina autentiseringsuppgifter fortfarande är giltiga. Om du använder självvärdförd integrerad körning (IR) kontrollerar du att du gör detta från den virtuella datorn eller datorn där den självvärdbaserade IR:en är installerad. Försök sedan skicka jobbet från Data Factory igen. Om det fortfarande misslyckas kontaktar du Data Factory-teamet för support.

<br>

- **Orsak**: När felmeddelandet innehåller ett meddelande som liknar "Användaradministratör är utelåst i Ambari" eller "Obehörigt: Ambari användarnamn eller lösenord är felaktigt", betyder det att autentiseringsuppgifterna för HDInsight är felaktiga eller har gått ut.

- **Rekommendation**: Korrigera autentiseringsuppgifterna och distribuera om den länkade tjänsten. Kontrollera först att autentiseringsuppgifterna fungerar på HDInsight genom att öppna kluster-URI:n i alla webbläsare och försöka logga in. Om autentiseringsuppgifterna inte fungerar kan du återställa dem från Azure-portalen.

<br>

- **Orsak**: När felmeddelandet innehåller ett meddelande som liknar '502 - Webbservern fick ett ogiltigt svar när den agerade som en gateway eller proxyserver", returneras det här felet av HDInsight-tjänsten.


- **Rekommendation**: Titta igenom Azure HDInsight felsökningsdokumentation, https://hdinsight.github.io/ambari/ambari-ui-502-error.htmltill exempel , https://hdinsight.github.io/spark/spark-thriftserver-errors.html, https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Orsak:** När felmeddelandet innehåller ett meddelande som liknar "Det går inte att hantera skicka jobbbegäran eftersom templeton-tjänsten är upptagen med för många skicka jobbförfrågningar" eller "Körot.joblauncher redan har 500 ansökningar, kan inte acceptera inlämning av ansökan", innebär det att alltför många jobb skickas till HDInsight samtidigt.

- **Rekommendation**: Överväg att begränsa antalet samtidiga jobb som skickas till HDInsight. Se Aktivitetskonstäman för Data Factory om jobben skickas av samma aktivitet. Ändra utlösarna så att de samtidiga pipelinekörningarna sprids över tiden. Se HDInsight dokumentation för att justera templeton.parallellism.job.submit som felet antyder.


### <a name="error-code--2301"></a>Felkod: 2301

- **Meddelande:**`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Orsak**: HDInsight-kluster eller tjänst har problem.


- **Rekommendation**: Det här felet inträffar när ADF inte får svar från HDInsight-kluster när du försöker få status för det jobb som körs. Det kan bero på problem i själva klustret, eller HDInsight-tjänsten kan ha ett avbrott. Se HDInsight felsökningsdokumentation https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guidepå , eller kontakta deras support för ytterligare hjälp.
                


### <a name="error-code--2302"></a>Felkod: 2302

- **Meddelande:**`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Orsak**: Jobbet skickades till HDI-kluster och misslyckades där.

- **Rekommendation**: Följ länken Garnloggar i aktivitetskörningen Utdata och leta efter fel i HDI-utdata. Kontakta HDInsight-teamet för support om det behövs.


### <a name="error-code--2303"></a>Felkod: 2303

- **Meddelande:**`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Orsak**: Jobbet skickades till HDI-kluster och misslyckades där.

- **Rekommendation**: Följ länken Garnloggar i aktivitetskörningen Utdata och leta efter fel i HDI-utdata. Försök igen eller kontakta HDInsight-teamet för support om det behövs.


### <a name="error-code--2304"></a>Felkod: 2304

- **Meddelande:**`MSI authentication is not supported on storages for HDI activities.`

- **Orsak**: De lagringslänkade tjänster som används i HDI-länkad tjänst eller HDI-aktivitet konfigureras med MSI-autentisering som inte stöds.

- **Rekommendation:** Ange fullständiga anslutningssträngar för lagringskonton som används i HDI-länkad tjänst eller HDI-aktivitet.


### <a name="error-code--2305"></a>Felkod: 2305

- **Meddelande:**`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Orsak**: Anslutningsinformationen för HDI-klustret är felaktig, den angivna användaren har inte behörighet att utföra den åtgärd som krävs eller hdinsight-tjänsten hade problem med att svara på begäranden från ADF.

- **Rekommendation**: Kontrollera att användarinformationen är korrekt. Kontrollera också att Ambari-användargränssnittet för HDI-klustret kan öppnas i en webbläsare från den virtuella datorn där IR är installerat vid självvärd ir eller kan öppnas från valfri dator om Azure IR.


### <a name="error-code--2306"></a>Felkod: 2306

- **Meddelande:**`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Orsak**: Den json som anges för skriptåtgärden är ogiltig.


- **Rekommendation**: Felmeddelandet bör hjälpa till att identifiera problemet. Åtgärda konfigurationen av JSON och försök igen. Sök https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service efter mer information.
                


### <a name="error-code--2310"></a>Felkod: 2310

- **Meddelande:**`Failed to submit Spark job. Error: '%message;'`

- **Orsak**: ADF försökte skapa en batch på ett Spark-kluster med Livy API (livy/batch), men fick ett fel.

- **Rekommendation**: Följ felmeddelandet för att åtgärda problemet. Om det inte finns tillräckligt med information för att få det löst, kontakta HDI-teamet och ge dem batch-ID och jobb-ID som finns i aktivitetskörningen Utdata på ADF Monitoring-sidan.


### <a name="error-code--2312"></a>Felkod: 2312

- **Meddelande:**`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Orsak**: Jobbet misslyckades i HDInsight Spark-klustret.

- **Rekommendation:** Följ länkarna i aktivitetskörningen Utdata på ADF-övervakningssidan för att felsöka körningen på HDInsight Spark-klustret. Kontakta HDInsights supportteam för ytterligare hjälp.


### <a name="error-code--2313"></a>Felkod: 2313

- **Meddelande:**`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Orsak**: Batchen togs bort i HDInsight Spark-klustret.

- **Rekommendation**: Felsöka batchar på HDInsight Spark-klustret. Kontakta HDInsight-supporten för ytterligare hjälp. 


### <a name="error-code--2328"></a>Felkod: 2328

- **Meddelande:**`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Orsak:**`The error message should show the details of what went wrong.`

- **Rekommendation**: Felmeddelandet bör hjälpa till att felsöka problemet.


### <a name="error-code--2329"></a>Felkod: 2329

- **Meddelande:**`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Orsak**: Felmeddelandet ska visa information om vad som gick fel.

- **Rekommendation**: Felmeddelandet bör hjälpa till att felsöka problemet.


### <a name="error-code--2331"></a>Felkod: 2331

- **Meddelande:**`The file path should not be null or empty.`

- **Orsak**: Den angivna filsökvägen är tom.

- **Rekommendation**: Ange en sökväg för en fil som finns.


### <a name="error-code--2340"></a>Felkod: 2340

- **Meddelande:**`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Orsak**: HDInsightOnDemand länkad tjänst stöder inte körning via SelfHosted IR.

- **Rekommendation:** Välj en Azure IR och försök igen.


### <a name="error-code--2341"></a>Felkod: 2341

- **Meddelande:**`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Orsak**: Den angivna webbadressen är inte i korrekt format.

- **Rekommendation**: Åtgärda kluster-URL:en och försök igen.


### <a name="error-code--2342"></a>Felkod: 2342

- **Meddelande:**`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Orsak**: De angivna autentiseringsuppgifterna är felaktiga för klustret eller om det uppstod en nätverkskonfiguration eller ett anslutningsproblem eller att IR har problem med att ansluta till klustret.

- **Rekommendation**:  
      1. Kontrollera att autentiseringsuppgifterna är korrekta genom att öppna HDInsight-klustrets Ambari-gränssnitt i en webbläsare.
      2. Om klustret finns i VNet och självvärdna IR används, hdi-URL:en som ska vara den privata URL:en i virtuella nätverk, betyder det att den ska ha '-int' efter klusternamnet. Till exempelhttps://mycluster.azurehdinsight.net/" " börhttps://mycluster-int.azurehdinsight.net/ändras till " ".
      2. Om klustret finns i VNet används självvärderade IR och den privata URL:en användes och anslutningen fortfarande misslyckades, då hade den virtuella datorn där IR är installerat problem med att ansluta till HDI. Anslut till den virtuella datorn där IR är installerat och öppna Ambari UI i en webbläsare. Använd den privata URL:en för klustret. Den här anslutningen bör fungera från webbläsaren. Om den inte gör det kontaktar du HDInsights supportteam för ytterligare hjälp.
      3. Om självvärdbaserad IR inte används bör HDI-klustret vara tillgängligt för allmänheten. Öppna Ambari UI i en webbläsare och se till att det öppnas. Om det finns några problem med klustret eller tjänsterna på det kontaktar du HDInsights supportteam för hjälp.
      Så i allmänhet måste HDI-kluster-URL:en som används i ADF-länkad tjänst vara tillgänglig för ADF IR (självvärd eller Azure) för att testanslutningen ska klaras och för att körningarna ska fungera. Detta kan enkelt verifieras genom att öppna webbadressen från en webbläsare antingen från virtuell dator eller någon offentlig dator.
    


### <a name="error-code--2343"></a>Felkod: 2343

- **Meddelande:**`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Orsak**: Antingen är användarnamnet eller lösenordet tomma.

- **Rekommendation**: Ange korrekta autentiseringsuppgifter för att ansluta till HDI och försök igen.


### <a name="error-code--2345"></a>Felkod: 2345

- **Meddelande:**`Failed to read the content of the hive script. Error: '%message;'`

- **Orsak**: Skriptfilen finns inte eller ADF kunde inte ansluta till platsen för skriptet.

- **Rekommendation**: Kontrollera att skriptet finns och den associerade länkade tjänsten har rätt autentiseringsuppgifter för anslutning.


### <a name="error-code--2346"></a>Felkod: 2346

- **Meddelande:**`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Orsak**: ADF försökte upprätta en ODBC-anslutning till HDI-klustret och det misslyckades med fel.

- **Rekommendation**: Felmeddelandet och felkoden bör hjälpa till att felsöka ODBC-anslutningsfelen. Om de inte är tillräckliga för att lösa problemet kontaktar du Azure HDInsight-teamet för support.


### <a name="error-code--2347"></a>Felkod: 2347

- **Meddelande:**`Hive execution through ODBC failed with error message '%message;'.`

- **Orsak:** ADF skickade hive-skriptet för körning till HDI-klustret via ODBC-anslutning och skriptet har misslyckats på HDI.

- **Rekommendation**: Körningen av hive-skriptet misslyckades på HDI-kluster och felmeddelande och felkod bör hjälpa till att felsöka. Om de inte är tillräckliga för att lösa problemet kontaktar du Azure HDInsight-teamet för support.


### <a name="error-code--2348"></a>Felkod: 2348

- **Meddelande:**`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Orsak**: De lagringslänkade tjänstegenskaperna är inte korrekt inställda.

- **Rekommendation:** Endast fullständiga anslutningssträngar stöds i huvudlagringslänkade tjänsten för HDI-aktiviteter. Kontrollera att du inte använder MSI-autentisering eller program.


### <a name="error-code--2350"></a>Felkod: 2350

- **Meddelande:**`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Orsak**: De autentiseringsuppgifter som tillhandahålls för att ansluta till lagringen där filerna ska placeras är felaktiga eller filerna finns inte där.

- **Rekommendation**: Det här felet inträffar när ADF förbereder steg för HDI-aktiviteter. Den försöker kopiera filer till huvudlagringen innan du skickar jobbet till HDI. Kontrollera att det finns filer på den angivna platsen, lagringsanslutningen är korrekt. ADF HDI-aktiviteter stöder inte MSI-autentisering på lagringskonton relaterade till HDI-aktiviteter, så se till att dessa länkade tjänster har fullständiga nycklar eller använder Azure Key Vault.


### <a name="error-code--2351"></a>Felkod: 2351

- **Meddelande:**`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Orsak**: Filen finns inte vid angiven sökväg.

- **Rekommendation**: Kontrollera om filen verkligen finns, och den länkade tjänsten med anslutningsinformation som pekar på den här filen har korrekta autentiseringsuppgifter.


### <a name="error-code--2352"></a>Felkod: 2352

- **Meddelande:**`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Orsak**: De fillagringslänkade tjänstegenskaperna är inte korrekt inställda.

- **Rekommendation**: Kontrollera att egenskaperna för den länkade tjänsten för fillagring är korrekt konfigurerade.


### <a name="error-code--2353"></a>Felkod: 2353

- **Meddelande:**`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Orsak**: Egenskaper för skriptlagringslänkade tjänst är inte korrekt inställda.

- **Rekommendation**: Kontrollera att egenskaperna för den länkade skriptlagringstjänsten är korrekt konfigurerade.


### <a name="error-code--2354"></a>Felkod: 2354

- **Meddelande:**`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Orsak**: Den lagringslänkade tjänsttypen stöds inte av aktiviteten.

- **Rekommendation**: Kontrollera att den valda länkade tjänsten har en av de typer som stöds för aktiviteten. HDI-aktiviteter stöder AzureBlobStorage och AzureBlobFSStorage länkade tjänster.


### <a name="error-code--2355"></a>Felkod: 2355

- **Meddelande:**`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Orsak**: Den föreskjutande för kommandoMiljö är felaktig.

- **Rekommendation**:  
      Kontrollera att det angivna värdet \"liknar: commandEnvironment\": [ \"\" variableName=variableValue ] Och varje variabel visas bara i listan en gång.
    


### <a name="error-code--2356"></a>Felkod: 2356

- **Meddelande:**`The commandEnvironment already contains a variable named '%variableName;'.`

- **Orsak**: Variabeln angavs två gånger i kommandotMiljö .

- **Rekommendation**:  
      Kontrollera att det angivna värdet \"liknar: commandEnvironment\": [ \"\" variableName=variableValue ] Och varje variabel visas bara i listan en gång.
    


### <a name="error-code--2357"></a>Felkod: 2357

- **Meddelande:**`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Orsak**: De angivna autentiseringsuppgifterna är felaktiga.

- **Rekommendation**: Kontrollera anslutningsinformationen i ADLS Gen 1-länkad tjänst och se till att testanslutningen lyckas.


### <a name="error-code--2358"></a>Felkod: 2358

- **Meddelande:**`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Orsak**: Det angivna värdet för den obligatoriska egenskapen TimeToLive har ogiltigt format. 

- **Rekommendation**: Uppdatera värdet så att det finns i det föreslagna intervallet och försök igen.


### <a name="error-code--2359"></a>Felkod: 2359

- **Meddelande:**`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Orsak**: Det angivna värdet för egenskapen "roller" är ogiltigt.

- **Rekommendation**: Uppdatera värdet så att det blir ett av förslagen och försök igen.


### <a name="error-code--2360"></a>Felkod: 2360

- **Meddelande:**`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Orsak**: Den angivna anslutningssträngen för HCatalogLinkedService är ogiltig.

- **Rekommendation:** Uppdatera värdet till en korrekt Azure SQL-anslutningssträng och försök igen.


### <a name="error-code--2361"></a>Felkod: 2361

- **Meddelande:**`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Orsak**: Det gick inte att skapa klustret och ADF fick inte tillbaka något fel från HDInsight-tjänsten.

- **Rekommendation**: Öppna Azure-portalen och försök hitta HDI-resursen med ett medgett namn och kontrollera etableringsstatusen. Kontakta HDInsight supportteam för ytterligare hjälp.


### <a name="error-code--2362"></a>Felkod: 2362

- **Meddelande:**`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Orsak**: Den angivna ytterligare lagringen var inte Azure Blob-lagring.

- **Rekommendation:** Ange Azure Blob storage-konto som en extra lagring för HDInsight på begäran länkad tjänst.



## <a name="web-activity"></a>Webbaktivitet

### <a name="error-code--2128"></a>Felkod: 2128

- **Meddelande:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Orsak:** Nätverksanslutning, DNS-fel, validering av servercertifikat eller timeout.

- **Rekommendation**: Verifiera att slutpunkten som du försöker träffa svarar på begäranden. Du kan använda verktyg som Fiddler / Postman.


### <a name="error-code--2108"></a>Felkod: 2108

- **Meddelande:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Orsak**: Begäran misslyckades på grund av ett underliggande problem som nätverksanslutning, DNS-fel, validering av servercertifikat eller timeout.

- **Rekommendation**: Använd Fiddler/Postman för att validera begäran.
<br>


#### <a name="more-details"></a>Mer information
Så här använder du Fiddler för att skapa en HTTP-session för det övervakade webbprogrammet:

1. Ladda ner, installera och öppna [Fiddler](https://www.telerik.com/download/fiddler).

1. Om webbprogrammet använder HTTPS går du till **Tools** > **Fiddler Options** > **HTTPS**. Välj **Fånga HTTPS CONNECTs** och **Dekryptera HTTPS-trafik**.

   ![Alternativ för spelman](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Om ditt program använder TLS/SSL-certifikat lägger du till Fiddler-certifikatet på enheten. Gå till **Verktyg** > **Fiddler Alternativ** > **HTTPS** > **Åtgärder** > Exportera**rotcertifikat till skrivbordet**.

1. Stäng av hämtning genom att gå till **File** > **Capture Traffic**. Eller tryck på **F12**.

1. Rensa webbläsarens cacheminne så att alla cachelagrade objekt tas bort och måste hämtas igen.

1. Skapa en begäran:

   1. Välj fliken **Kompositör.**

   1. Ange HTTP-metod och URL.
   
   1. Lägg till rubriker och en begärandetext om du behöver.

   1. Välj **Kör**.

1. Aktivera trafikhämtning igen och slutför den problematiska transaktionen på sidan.

1. Gå till Spara alla**Save** > **sessioner** **för fil** > .

Mer information finns i [Komma igång med Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Nästa steg

Mer felsökningshjälp finns i följande resurser:

*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Begäran om datafabriksfunktion](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Spill forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information om Data Factory](https://twitter.com/hashtag/DataFactory)


            
