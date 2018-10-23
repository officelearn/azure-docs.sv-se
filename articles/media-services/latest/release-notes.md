---
title: Azure Media Services v3 viktig information | Microsoft Docs
description: Om du vill hålla dig uppdaterad med den senaste utvecklingen, ger dig med de senaste uppdateringarna på Azure Media Services v3 i den här artikeln.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 10bd2101839e1b6d1dbdc1e53c31693ab0be98fd
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647773"
---
# <a name="azure-media-services-v3-release-notes"></a>Azure Media Services v3 viktig information 

Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

* Den senaste versionen
* Kända problem
* Felkorrigeringar
* Inaktuell funktion
* Planer för ändringar

## <a name="october-2018---ga"></a>Oktober 2018, GA

Det här avsnittet beskrivs Azure Media Services (AMS) oktober uppdateringar.

### <a name="rest-v3-ga-release"></a>REST v3 GA-version

Den [REST v3 GA-version](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) innehåller fler API: er för Live, konto/tillgången manifest nivåfilter och DRM-stöd.

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Azure CLI 2.0-modulen omfattar för närvarande åtgärder för Live, nyckel Innehållsprinciper, Streaming principer. Mer information finns i [planer för ändringar](#plans-for-changes).

#### <a name="azure-resource-management"></a>Azure Resource Management 

Stöd för Azure-resurshantering kan enhetlig hantering och åtgärder API (nu allt från en plats).

Från och med den här versionen kan använda du Resource Manager-mallar för att skapa Live-händelser.

#### <a name="improvement-of-asset-operations"></a>Förbättring av tillgången åtgärder 

Följande förbättringar introducerades:

- Mata in från http (s)-URL: er eller Azure Blob Storage SAS URL: er.
- Anger du namn på egna behållare för tillgångar. 
- Enklare utdata-stöd för att skapa anpassade arbetsflöden med Azure Functions.

#### <a name="new-transform-object"></a>Nytt Transform-objekt

Den nya **transformera** objektet förenklar Encoding-modellen. Nytt objekt gör det enkelt att skapa och dela kodning Resource Manager-mallar och förinställningar. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Azure Active Directory-autentisering och RBAC

Azure AD-autentisering och rollbaserad åtkomstkontroll (RBAC) aktivera säker transformeringar, LiveEvents, Innehållsprinciper till nyckeln eller tillgångar efter roll eller användare i Azure AD.

#### <a name="client-sdks"></a>Klient-SDK: er  

Språk som stöds i Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Live encoding uppdateringar

Följande uppdateringar för live encoding introduceras:

- Nya läget för låg latens för live-(10 sekunder slutpunkt till slutpunkt).
- Förbättrad RTMP support (ökad stabilitet och mer käll-kodare stöder).
- RTMPS säker mata in.

    När du skapar en LiveEvent kan du nu hämta 4 infognings-URL: er. 4 mata in URL: er är nästan identiska, har samma strömmande token (AppId), bara den numeriska delen port är olika. Två av de URL: er är primär och sekundär för RTMPS. 
- 24 timmars transkodning stöd. 
- Förbättrat stöd för ad-signalering i RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Förbättrat stöd för Event Grid

Du kan se följande Event Grid stöd förbättringar:

- Azure EventGrid-integrering för enklare utveckling med Logic Apps och Azure Functions. 
- Prenumerera på händelser på kodning och Live Channels.

### <a name="cmaf-support"></a>CMAF support

Stöd för CMAF och 'cbcs' kryptering för (iOS 11 +) för Apple HLS och MPEG-DASH spelare som har stöd för CMAF.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA-versionen har vi tillkännagav i augusti. Ny information om funktioner som stöds för närvarande finns i [vad är Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planer för ändringar

Azure CLI-versionen kommer 11/06/2018.

### <a name="known-issues"></a>Kända problem

Kunder som använde förhandsversionen av API för tillgång eller AccountFilters som påverkas av följande problem.

Om du har skapat tillgångar eller kontofilter mellan 09/28 och 10/12 med Media Services v3 CLI eller API: er, måste du ta bort alla tillgångar och AccountFilters och återskapa dem på grund av en versionskonflikt. 

## <a name="may-2018---preview"></a>Maj 2018 - förhandsversion

### <a name="net-sdk"></a>.NET SDK

Följande funktioner finns i .net SDK:

* **Omvandlar** och **jobb** att koda eller analysera medieinnehåll. Exempel finns i [Stream filer](stream-files-tutorial-with-api.md) och [analysera](analyze-videos-tutorial-with-api.md).
* **StreamingLocators** för att publicera och strömning av innehåll till slutanvändarens enheter
* **StreamingPolicies** och **ContentKeyPolicies** konfigurera viktiga leverans- och content protection (DRM) när du levererar innehåll.
* **LiveEvents** och **LiveOutputs** att konfigurera för inmatning och arkivering av liveuppspelningsinnehåll.
* **Tillgångar** att lagra och publicera medieinnehåll i Azure Storage. 
* **Strömningsslutpunkter** du konfigurerar och skalar dynamisk paketering, kryptering och strömning för både live och på begäran medieinnehåll.

### <a name="known-issues"></a>Kända problem

* När du skickar ett jobb kan du ange för att mata in källvideon med hjälp av HTTPS-adresser, SAS URL: er eller sökvägar till filer i Azure Blob storage. AMS v3 stöder för närvarande inte segmentvis överföringskodning över HTTPS-URL:er.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt](media-services-overview.md)
