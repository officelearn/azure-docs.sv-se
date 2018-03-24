---
title: Felkoder för Azure Machine Learning REST API | Microsoft Docs
description: Dessa felkoder kunde returneras av en åtgärd på en Azure Machine Learning-webbtjänst.
keywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 0ba44b2a93bcd542db1350def2d0554c8c44233c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="machine-learning-rest-api-error-codes"></a>Machine Learning felkoder för REST API
 
Följande felkoder kan returneras av en åtgärd på en Azure Machine Learning-webbtjänst.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-statuskod 400)
 
Ogiltigt argument har angetts.
 
Fel i den här klassen innebär ett argument som tillhandahålls någonstans var ogiltigt. Detta kan bero på en autentiseringsuppgift eller platsen för Azure storage till något som skickades till webbtjänsten. Tittar du på fältet fel ”kod” i avsnittet ”information” att diagnostisera vilka specifika argument var ogiltigt.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| BadParameterValue | Det angivna parametervärdet uppfyller inte parametern-regeln på parametern |
| BadSubscriptionId | Prenumerations-Id som används för att poängsätta är inte som finns i resursen |
| BadVersionCall | Ogiltig version parameter skickades under API-anropet: {0}. Kontrollera sidan API hjälp för att skicka rätt version och försök igen. |
| BatchJobInputsNotSpecified | De obligatoriska input(s) inte har angetts med förfrågan: {0}. Kontrollera att alla indata har angetts och försök igen. |
| BatchJobInputsTooManySpecified | Begäran angav fler indata än vad som definierats i tjänsten. Lista över godkända input(s): {0}. Kontrollera att alla indata är rätt angivet och försök igen. |
| BlobNameTooLong | Azure blob storage sökvägen för diagnostiska utdata är för lång: {0}. Ange ett kortare sökväg och försök igen. |
| BlobNotFound | Det går inte att komma åt den angivna Azure blobben - {0}.  Azure-felmeddelande: {1}. |
| ContainerIsEmpty | Inga Azure storage behållarens namn angavs. Ange ett giltigt behållarnamn och försök igen. |
| ContainerSegmentInvalid | Ogiltig behållarnamn. Ange ett giltigt behållarnamn och försök igen. |
| ContainerValidationFailed | BLOB-behållaren verifieringen misslyckades med felet: {0}. |
| DataTypeNotSupported | Typ av data som inte stöds har angetts. Ange giltig datatyperna och försök igen. |
| DuplicateInputInBatchCall | Batch-begäran är ogiltig. Det går inte att ange både enskilda och flertaliga indata samtidigt. Ta bort ett av objekten från begäran och försök igen. |
| ExpiryTimeInThePast | Förfallotiden som har inträffat: {0}. Ange en framtida förfallotiden i UTC och försök igen. Anger förfallotiden som NULL för att aldrig går ut. |
| IncompleteSettings | Diagnostikinställningar är ofullständig. |
| InputBlobRelativeLocationInvalid | Inget Azure storage blob namn har angivits. Ange ett giltigt blob-namn och försök igen. |
| InvalidBlob | Ogiltig blob-specifikationen för blob: {0}. Kontrollera att anslutningssträngen / relativ sökväg eller SAS-token specifikationen är korrekt och försök igen. |
| InvalidBlobConnectionString | Den angivna anslutningssträngen för en av i/o-blobbar ogiltig: {0}. Åtgärda detta och försök igen. |
| InvalidBlobExtension | Blob-referens: {0} har ett filtillägg som är ogiltig eller saknas. Filnamnstillägg som stöds för den här utdatatypen är: ”{1}”. |
| InvalidInputNames | Ogiltig indata namn anges i begäran: {0}. Mappa inkommande data till rätt tjänst indata och försök igen. |
| InvalidOutputOverrideName | Ogiltig åsidosättningsnamn: {0}. Tjänsten har inte en output-nod med detta namn. Ange ett korrekt utdata nod namn att åsidosätta (skiftlägeskänslighet gäller). |
| InvalidQueryParameter | Ogiltig parameter: {0}. {1} |
| MissingInputBlobInformation | Azure storage blob information saknas. Ange en giltig anslutningssträng och relativ sökväg eller URI och försök igen. |
| MissingJobId | Inget jobb-Id har angetts. Ett jobb Id returneras när ett jobb har skickats för första gången. Kontrollera jobb-Id är korrekt och försök igen. |
| MissingKeys | Inga nycklar som eller en primär eller sekundärnyckel har inte angetts. |
| MissingModelPackage | Ingen modell paket-Id eller paket i modellen. Ange en giltig modell paket-Id eller modell paketet och försök igen. |
| MissingOutputOverrideSpecification | Begäran saknar blob-specifikationen för utdata åsidosättning {0}. Ange en giltig blobbplats med begäran eller ta bort specifikationen för utdata om ingen plats åsidosättning önskas. |
| MissingRequestInput | Webbtjänsten förväntar sig indata, men inga indata har angetts. Se till att giltiga indata har angetts baserat på de publicerade portarna i modellen och försök igen. |
| MissingRequiredGlobalParameters | Inte alla nödvändiga web service parametrar angavs. Kontrollera parametrarna som förväntat för modulen eller modulerna som är korrekta och försök igen. |
| MissingRequiredOutputOverrides | När du anropar en krypterad tjänstslutpunkten som det är obligatoriskt att skicka in åsidosätter utdata i tjänstens utdata. Saknas åsidosättningar just nu för dessa utdata: {0} |
| MissingWebServiceGroupId | Ingen web serviceavtalsgrupp-Id har angetts. Ange en giltig web serviceavtalsgrupp Id och försök igen. |
| MissingWebServiceId | Inga webbtjänster Id angetts. Ange en giltig webbtjänst Id och försök igen. |
| MissingWebServicePackage | Inga web Service-paketet som anges. Ange ett giltigt web service-paket och försök igen. |
| MissingWorkspaceId | Ingen arbetsyta Id angetts. Ange en giltig arbetsyta Id och försök igen. |
| ModelConfigurationInvalid | Ogiltig modellkonfiguration i paket för modellen. Kontrollera konfigurationen modellen innehåller utdata-slutpunkt(er) definition, std fel slutpunkt och std ut slutpunkt och försök igen. |
| ModelPackageIdInvalid | Ogiltig modell paketets Id. Kontrollera att modellen paket-Id är korrekt och försök igen. |
| RequestBodyInvalid | Inga begärandetexten tillhandahålls eller fel vid avserialisering av innehållet för begäran. |
| RequestIsEmpty | Ingen förfrågan. Ange en giltig begäran och försök igen. |
| UnexpectedParameter | Oväntat parametrar som ges. Kontrollera alla parameternamn är rättstavade endast förväntade parametrar har skickats och försök igen. |
| UnknownError | Okänt fel. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Det går inte att ändra samtidiga begäranden krav för {0}-webbtjänsten. |
| WebServiceIdInvalid | Ogiltig webbtjänst-id angavs. Webbtjänst-id måste vara ett giltigt guid. |
| WebServiceTooManyConcurrentRequestRequirement | Det går inte att ange krav för antal samtidiga begäranden till mer än {0}. |
| WebServiceTypeInvalid | Ogiltig web service-typen som anges. Verifiera giltig web service-typen stämmer och försök igen. Giltig web tjänsttyper: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-statuskod 400)
 
Ogiltigt användar-argumentet som tillhandahålls.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| InputMismatchError | Inkommande data överensstämmer inte med schemat för indataport. |
| InputParseError | Tolka inkommande vector misslyckades.  Kontrollera inkommande vektorn har rätt antal kolumner och -datatyper.  Ytterligare information: {0}. |
| MissingRequiredGlobalParameters | Parametrar som förväntades av webbtjänsten saknas. Kontrollera att de obligatoriska parametrarna som förväntades av webbtjänsten är korrekta och försök igen. |
| UnexpectedParameter | Kontrollera att endast de obligatoriska parametrarna som förväntades av webbtjänsten har skickats och försök igen. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-statuskod 400)
 
Begäran är ogiltig i den aktuella kontexten.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| CannotStartJob | Jobbet kan inte startas eftersom den är i {0}. |
| IncompatibleModel | Modellen är inkompatibel med den begärda versionen. Den begärda versionen stöder endast enstaka datatable utdata modeller. |
| MultipleInputsNotAllowed | Modellen tillåter inte flera inmatningar. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-statuskod 400)
 
Körningen av-modulen påträffade ett internt bibliotek för fel.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-statuskod 400)
 
Ett fel uppstod vid körningen av modulen.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-statuskod 400)
 
Ogiltig web service-paketet. Kontrollera web service-paketet som är korrekt och försök igen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| FormatError | Web service-paketet har fel format. Information: {0} |
| RuntimesError | Web service paketet diagrammet är ogiltig. Information: {0} |
| ValidationError | Web service paketet diagrammet är ogiltig. Information: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Obehörig (HTTP-statuskod 401)
 
Begäran har inte behörighet för åtkomst till resursen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| AdminRequestUnauthorized | Behörighet saknas |
| ManagementRequestUnauthorized | Behörighet saknas |
| ScoreRequestUnauthorized | Ogiltiga autentiseringsuppgifter. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-statuskod 404)
 
Det gick inte att hitta resursen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| ModelPackageNotFound | Modell-paketet hittades inte. Kontrollera modell paket-Id är korrekt och försök igen. |
| WebServiceIdNotFoundInWorkspace | Webbtjänsten under den här arbetsytan gick inte att hitta. Det finns ett matchningsfel mellan webServiceId och workspaceId. Kontrollera att webbtjänsten som är en del av arbetsytan och försök igen. |
| WebServiceNotFound | Web service hittades inte. Kontrollera webbtjänsten Id är korrekt och försök igen. |
| WorkspaceNotFound | Det gick inte att hitta arbetsyta. Kontrollera arbetsytan Id är korrekt och försök igen. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-statuskod 408)
 
Åtgärden kunde inte slutföras under den tilldelade tiden.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| RequestCanceled | Begäran avbröts av klienten. |
| ScoreRequestTimeout | Körningsbegäran tidsgränsen. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (HTTP-statuskod 409)
 
Resursen finns redan.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Ogiltig parameter utdatanamn. Försök att använda modulen metadata Redigeraren för att byta namn på kolumner och försök igen. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-statuskod 413)
 
Modellen har överskred minneskvoten tilldelats.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| OutOfMemoryLimit | Modellen förbrukat mer minne än vad som var används för den. Maximalt tillåtna minnesstorleken för modellen som är {0} MB. Kontrollera din modell för problem. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-statuskod 500)
 
Ett internt fel inträffade vid körning.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Behållaren processen kraschat med systemfel |
| ContainerProcessTerminatedWithUnknownError | Behållaren processen kraschat med ett okänt fel |
| ContainerValidationFailed | BLOB-behållaren verifieringen misslyckades med felet: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Inga argument har angetts. Kontrollera att giltiga argument har skickats och försök igen. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Port-id = {0} har en datatyp som inte stöds: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger gick inte att generera, information: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Okänt jobb statuskoden {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, Details: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-statuskod 500)
 
Ett internt fel inträffade vid körning. Systemet har ont om ledigt minne. Försök igen.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-statuskod 500)
 
Ogiltig modell-paketet. Kontrollera de paket i modellen är korrekt och försök igen.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-statuskod 500)
 
Ogiltig web service-paketet. Kontrollera webbpaket som angetts är korrekt och försök igen.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| ModuleError | Web service paketet diagrammet är ogiltig. Information: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-statuskod 503)
 
Begäran kan inte utföra som behållarna initieras.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-statuskod 503)
 
Tjänsten är otillgänglig.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| NoMoreResources | Inga resurser som är tillgängliga för begäran. |
| RequestThrottled | Begäran har begränsats för slutpunkten {0}. Den maximala samtidigheten för slutpunkten är {1}. |
| TooManyConcurrentRequests | För många samtidiga förfrågningar som skickas. |
| TooManyHostsBeingInitialized | För många värdar initieras på samma gång. Överväg att begränsa / försöker igen. |
| TooManyHostsBeingInitializedPerModel | För många värdar initieras på samma gång. Överväg att begränsa / försöker igen. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-statuskod 504)
 
Åtgärden kunde inte slutföras under den tilldelade tiden.
 
| Felkod | Användarmeddelande |
| ---------- |--------------|
| BackendInitializationTimeout | Initiera för web-tjänsten kunde inte slutföras inom den tilldelade tiden. |
| BackendScoreTimeout | Web service begäran körningen slutfördes inte inom den tilldelade tiden. |
 
