---
title: REST API-felkoder - Azure Machine Learning Studio (klassisk) | Microsoft-dokument
description: Dessa felkoder kan returneras av en åtgärd på en Azure Machine Learning-webbtjänst.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217758"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klassiska) REST API-felkoder

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
Följande felkoder kan returneras av en åtgärd på en azure Machine Learning Studio -webbtjänst (klassisk).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-statuskod 400)
 
Ogiltigt argument.
 
Den här felklassen innebär att ett argument som angavs någonstans var ogiltigt. Detta kan vara en autentiseringsuppgifter eller plats för Azure-lagring till något som skickas till webbtjänsten. Titta på fältet "kod" i avsnittet "detaljer" för att diagnostisera vilket specifikt argument som var ogiltigt.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| BadParameterVärde | Det angivna parametervärdet uppfyller inte parameterregeln för parametern |
| BadSubscriptionId | Prenumerations-ID som används för att göra mål är inte det som finns i resursen |
| BadVersionCall (Senaste900) | Ogiltig versionsparameter skickades {0}under API-anropet: . Kontrollera api-hjälpsidan för att skicka rätt version och försök igen. |
| BatchJobInputsTerade | Följande obligatoriska indata angavs inte med begäran: {0}. Kontrollera att alla indata har angetts och försök igen. |
| BatchJobInputsTooMany Angivet | Begäran angav fler indata än vad som definierats i tjänsten. Lista över godkända indata: {0}. Kontrollera att alla indata anges korrekt och försök igen. |
| BlobNameTooLong | Azure blob storage path som tillhandahålls {0}för diagnostikutdata är för lång: . Förkorta banan och försök igen. |
| BlobNotFound | Det gick inte att komma {0}åt den medföljande Azure-bloben - .  Azure-felmeddelande: {1}. |
| ContainerIsEmpty | Inget Azure-lagringsbehållarenamn angavs. Ange ett giltigt behållarnamn och försök igen. |
| ContainerSegmentInvalid | Ogiltigt behållarnamn. Ange ett giltigt behållarnamn och försök igen. |
| ContainerValidationTydde | Validering av Blob-behållare {0}misslyckades med det här felet: . |
| DataTypeDet stöds inte | Datatyp som inte stöds. Ange giltiga datatyper och försök igen. |
| DupliceraInputInBatchCall | Batchbegäran är ogiltig. Det går inte att ange både enstaka och flera indata samtidigt. Ta bort ett av dessa objekt från begäran och försök igen. |
| ExpiryTimeInThePast | Utgångstiden är tidigare: {0}. Ange en framtida utgångstid i UTC och försök igen. Om du aldrig upphör att gälla ställer du in förfallotiden till NULL. |
| Ofullständigainställningar | Diagnostikinställningarna är ofullständiga. |
| InputBlobRelativeLocationInvalid | Inget Azure-lagringsblobnamn anges. Ange ett giltigt blob-namn och försök igen. |
| Ogiltigt | Ogiltig blob-specifikation {0}för blob: . Kontrollera att anslutningssträngen/relativ sökväg eller SAS-tokenspecifikationen är korrekt och försök igen. |
| Ogiltigt BlusAnslutningString | Anslutningssträngen som angetts för en av indata-/utdatablobar i ogiltiga: {0}. Rätta till detta och försök igen. |
| Ogiltig blusextension | Blob-referensen: {0} har ett ogiltigt eller saknat filnamnstillägg. Filtillägg som stöds för den{1}här utdatatypen är: " ". |
| OgiltigainputNamn | Ogiltiga tjänstindatanamn som anges i {0}begäran: . Mappa indata till rätt tjänstindata och försök igen. |
| OgiltigtUtutoverridenamn | Ogiltigt namn {0}på utdatasidosättning: . Tjänsten har ingen utdatanod med det här namnet. Skicka in ett korrekt utgångsnodnamn för att åsidosätta (ärendekänslighet gäller). |
| OgiltigtQueryParameter | Ogiltig frågeparameter '{0}'. {1} |
| SaknadInputBlobInformation | Information om Azure-lagringsblob saknas. Ange en giltig anslutningssträng och relativ sökväg eller URI och försök igen. |
| SaknasJobId | Inget jobb-ID tillhandahålls. Ett jobb-ID returneras när ett jobb skickades för första gången. Kontrollera att jobb-ID:n är korrekt och försök igen. |
| Saknade Tangenter | Inga nycklar tillhandahålls eller en av primär- eller sekundärnyckeln tillhandahålls inte. |
| Saknad Modellpaket | Ingen modellpaket-ID eller modellpaket tillhandahålls. Ange ett giltigt modellpaket-ID eller modellpaket och försök igen. |
| SaknasUtsiktÖversiktSpecifiering | Begäran saknar blob-specifikationen {0}för utdatasidosättning . Ange en giltig blob-plats med begäran eller ta bort utdataspecifikationen om ingen plats åsidosättning önskas. |
| SaknasRequestInput | Webbtjänsten förväntar sig en indata, men ingen indata har angetts. Kontrollera att giltiga indata tillhandahålls baserat på de publicerade indataportarna i modellen och försök igen. |
| MissingRequiredGlobalParameters MissingRequiredGlobalParameters MissingRequiredGlobalParameters MissingRe | Alla nödvändiga webbtjänstparameter anges inte. Kontrollera att de parametrar som förväntas för modulerna är korrekta och försök igen. |
| MissingRequiredOutputOverrides | När du anropar en krypterad tjänstslutpunkt är det obligatoriskt att skicka in utdata åsidosättningar för alla tjänstens utdata. Åsidosättningar saknas just nu för dessa utdata:{0} |
| SaknasWebServiceGroupId | Inget webbtjänstgrupp-ID tillhandahålls. Ange ett giltigt webbtjänstgrupp-ID och försök igen. |
| SaknasWebServiceId | Inget webbtjänst-ID tillhandahålls. Ange ett giltigt webbtjänst-ID och försök igen. |
| SaknasWebServicePackage | Inget webbtjänstpaket tillhandahålls. Ange ett giltigt webbtjänstpaket och försök igen. |
| MissingWorkspaceId | Inget arbetsyte-ID finns. Ange ett giltigt arbetsyte-ID och försök igen. |
| ModellkonfigurationInvalid | Ogiltig modellkonfiguration i modellpaketet. Kontrollera att modellkonfigurationen innehåller definition av utdataslutpunkter, std-felslutpunkt och std-slutpunkt och försök igen. |
| ModelPackageIdInvalid | Ogiltig modellpaket-ID. Kontrollera att modellpaket-ID:n är korrekt och försök igen. |
| RequestBodyInvalid | Inget begärandeorgan tillhandahålls eller fel i att deserialisera begäran kroppen. |
| RequestIsEmpty | Ingen begäran tillhandahålls. Ange en giltig begäran och försök igen. |
| Oväntatparameter | Oväntade parametrar tillhandahålls. Kontrollera att alla parameternamn är rättstavade, endast förväntade parametrar skickas och försök igen. |
| Okändare | Ett okänt fel. |
| AnvändareParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Det går inte att ändra {0} krav på samtidiga begäranden för webbtjänsten. |
| WebServiceIdInvalid | Ogiltigt webbtjänst-ID som tillhandahålls. Webbtjänst-ID ska vara ett giltigt guid. |
| WebServiceTooManyConcurrentRequestRequirement | Det går inte att ange kravet {0}på samtidig begäran till mer än . |
| WebServiceTypeInvalid | Ogiltig webbtjänsttyp som tillhandahålls. Kontrollera att den giltiga webbtjänsttypen är korrekt och försök igen. Giltiga webbtjänsttyper: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-statuskod 400)
 
Ogiltigt användarargument.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| InputMismatchError | Indata matchar inte indataportschemat. |
| InputParseError | Det gick inte att tolka indatavektorn.  Kontrollera att indatavektorn har rätt antal kolumner och datatyper.  Ytterligare information: {0}. |
| MissingRequiredGlobalParameters MissingRequiredGlobalParameters MissingRequiredGlobalParameters MissingRe | Parametrar som förväntas av webbtjänsten saknas. Kontrollera att alla nödvändiga parametrar som förväntas av webbtjänsten är korrekta och försök igen. |
| Oväntatparameter | Kontrollera att endast de parametrar som krävs som förväntas av webbtjänsten skickas och försök igen. |
| AnvändareParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>Ogiltigt funktion (HTTP-statuskod 400)
 
Begäran är ogiltig i den aktuella kontexten.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| Det går inte att startajob | Jobbet kan inte startas {0} eftersom det är i tillstånd. |
| Inkompatibelmodel | Modellen är inte kompatibel med begäranden. Begärandeversionen stöder endast enskilda datatable utdatamodeller. |
| MultipleInputsNotAllowed | Modellen tillåter inte flera indata. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-statuskod 400)
 
Modulkörning påträffade ett internt biblioteksfel.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-statuskod 400)
 
Modulkörning påträffade ett fel.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-statuskod 400)
 
Ogiltigt webbtjänstpaket. Kontrollera att webbtjänstpaketet som tillhandahålls är korrekt och försök igen.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| FormatError | Webbtjänstpaketet är felaktigt. Detaljer:{0} |
| KörtiderError | Webbtjänstpaketets diagram är ogiltigt. Detaljer:{0} |
| Valideringserror | Webbtjänstpaketets diagram är ogiltigt. Detaljer:{0} |
 
## <a name="unauthorized-http-status-code-401"></a>Obehörig (HTTP-statuskod 401)
 
Begäran är obehörig att komma åt resursen.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| AdminRequestUnauktoriserad | Behörighet saknas |
| ManagementRequestUnauktoriserad | Behörighet saknas |
| ScoreRequestUnauthorized | Ogiltiga autentiseringsuppgifter tillhandahålls. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-statuskod 404)
 
Det gick inte att hitta resursen.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| ModelPackageNotFound | Det gick inte att hitta modellpaketet. Kontrollera att modellpaket-ID:n är korrekt och försök igen. |
| WebServiceIdNotFoundInWorkspace | Det gick inte att hitta webbtjänsten under den här arbetsytan. Det finns en obalans mellan webServiceId och workspaceId. Kontrollera att webbtjänsten som tillhandahålls är en del av arbetsytan och försök igen. |
| Webbtjänster InteFound | Det gick inte att hitta webbtjänsten. Kontrollera att webbtjänst-ID:t är korrekt och försök igen. |
| Arbetsytan Är integrundad | Det gick inte att hitta arbetsytan. Kontrollera att arbetsyte-ID:n är korrekt och försök igen. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-statuskod 408)
 
Åtgärden kunde inte slutföras inom den tillåtna tiden.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| BegäranCanceled | Begäran avbröts av klienten. |
| ScoreRequestTimeout | Time time out för körningsbegäran. |
 
## <a name="conflict-http-status-code-409"></a>Konflikt (HTTP-statuskod 409)
 
Det finns redan en resurs.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Ogiltigt parameternamn för utdata. Försök att använda modulen metadataredigerare för att byta namn på kolumner och försök igen. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-statuskod 413)
 
Modellen hade överskridit den minneskvot som tilldelats den.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| OutOfMemoryLimit | Modellen förbrukade mer minne än vad som disponerats för den. Maximalt tillåtet minne för {0} modellen är MB. Kontrollera din modell för problem. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-statuskod 500)
 
Körningen påträffade ett internt fel.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest (rygg) |  |
| ClusterConfigBlobMiskonfigurerad |  |
| ContainerProcessTerminatedWithSystemError | Behållarprocessen kraschade med systemfel |
| ContainerProcessTerminatedWithUnknownError | Behållarprocessen kraschade med okänt fel |
| ContainerValidationTydde | Validering av Blob-behållare {0}misslyckades med det här felet: . |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| MisslyckadesGettingApiDokumentet |  |
| FailedStoringWebService |  |
| Ogiltigminnekonfigurering | OgiltigMinnekonfigurering, ConfigValue:{0} |
| OgiltigResourceCacheKonfigurering |  |
| OgiltigFörst av BorttagningstillbebyggelseKonfigurering |  |
| InvalidWebServiceResources |  |
| SaknasTaskInstance | Inga argument. Kontrollera att giltiga argument skickas och försök igen. |
| ModelPackageInvalid |  |
| ModuleExecutionFailerad |  |
| ModuleLoadFailerad |  |
| ModuleObjectCloneFailed |  |
| OutputKonversionAvred |  |
| PortDataTypeInte Stöds | Port id={0} har en datatyp {1}som inte stöds: . |
| Resursinlästerladdning |  |
| ResursbelastningFailerad |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger generation misslyckades, Detaljer:{0} |
| UnexpectedScoreStatus OväntadeScore |  |
| OkändBackendErrorResponse |  |
| Okändare |  |
| OkändJobStatusCode | Okänd jobbstatuskod {0}. |
| OkändModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupIntens |  |
| WebServicePackageInvalid | InvalidWebServicePackage, Detaljer:{0} |
| WorkerAuthorizationFailed |  |
| WorkerOför att gå inte att få |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-statuskod 500)
 
Körningen påträffade ett internt fel. Systemet har ont om minne. Försök igen.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-statuskod 500)
 
Ogiltigt modellpaket. Kontrollera att modellpaketet är korrekt och försök igen.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-statuskod 500)
 
Ogiltigt webbtjänstpaket. Kontrollera att webbpaketet är korrekt och försök igen.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| ModuleError (Moduleror) | Webbtjänstpaketets diagram är ogiltigt. Detaljer:{0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>Initieracontainers (HTTP-statuskod 503)
 
Begäran kan inte köras eftersom behållarna initieras.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>Serviceovailable (HTTP-statuskod 503)
 
Tjänsten är inte tillgänglig för tillfället.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| NoMoreResources | Inga resurser tillgängliga för begäran. |
| BegäranTrottad | Begäran begränsades {0} för slutpunkten. Den maximala samtidigheten för slutpunkten {1}är . |
| TooManyConcurrentRequests | För många samtidiga begäranden som skickas. |
| TooManyHostsBeingInitialized | Alltför många värdar som initieras samtidigt. Överväg begränsning / återförsök. |
| TooManyHostsBeingInitializedPerModel | Alltför många värdar som initieras samtidigt. Överväg begränsning / återförsök. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-statuskod 504)
 
Åtgärden kunde inte slutföras inom den tillåtna tiden.
 
| Felkod | Meddelande om användare |
| ---------- |--------------|
| BackendInitializationTimeout | Initieringen av webbtjänsten kunde inte slutföras inom den tillåtna tiden. |
| BackendScoreTimeout | Körningen av webbtjänstbegäran kunde inte slutföras inom den tillåtna tiden. |
 
