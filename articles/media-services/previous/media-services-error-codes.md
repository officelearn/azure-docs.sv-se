---
title: Felkoder för Azure Media Services | Microsoft-dokument
description: HTTP-felkoder från tjänsten kan visas beroende på problem som autentiseringstoken som löper ut till åtgärder som inte stöds i Media Services. Den här artikeln innehåller en översikt över Azure Media Services v2 API-felkoder.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f5a2dd68d86a7a38fc7f2942351c42c84742d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887096"
---
# <a name="azure-media-services-error-codes"></a>Felkoder för Azure Media Services
När du använder Microsoft Azure Media Services kan http-felkoder från tjänsten visas beroende på problem som autentiseringstoken som löper ut till åtgärder som inte stöds i Media Services. Följande är en lista över **HTTP-felkoder** som kan returneras av Media Services och möjliga orsaker till dem.  

## <a name="400-bad-request"></a>400 dålig begäran
Begäran innehåller ogiltig information och avvisas på grund av något av följande skäl:

* En API-version som inte stöds har angetts. Den senaste versionen finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md).
* API-versionen av Media Services har inte angetts. Information om hur du anger API-versionen finns i [Rest API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)för Media Services Operations REST API .
  
  > [!NOTE]
  > Om du använder .NET- eller Java-SDK:erna för att ansluta till Media Services anges API-versionen åt dig när du försöker utföra vissa åtgärder mot Media Services.
  > 
  > 
* En odefinierad egenskap har angetts. Egenskapsnamnet finns i felmeddelandet. Endast de egenskaper som är medlemmar i en viss entitet kan anges. Se [AZURE Media Services REST API Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) för en lista över entiteter och deras egenskaper.
* Ett ogiltigt egenskapsvärde har angetts. Egenskapsnamnet finns i felmeddelandet. Se föregående länk för giltiga egenskapstyper och deras värden.
* Ett egenskapsvärde saknas och krävs.
* En del av den angivna webbadressen innehåller ett felaktigt värde.
* Ett försök gjordes att uppdatera en WriteOnce-egenskap.
* Ett försök gjordes att skapa ett jobb som har en indatatillgång med en primär AssetFile som inte angavs eller inte kunde fastställas.
* Ett försök gjordes att uppdatera en SAS Locator. SAS-positionerare kan bara skapas eller tas bort. Streaming locators kan uppdateras. Mer information finns i [Locators](https://docs.microsoft.com/rest/api/media/operations/locator).
* En åtgärd eller fråga som inte stöds har skickats.

## <a name="401-unauthorized"></a>401 Obehörig
Begäran kunde inte autentiseras (innan den kan godkännas) på grund av något av följande skäl:

* Autentiseringshuvud saknas.
* Felaktigt autentiseringshuvudvärde.
  * Token har upphört att gälla. 
  * Token innehåller en ogiltig signatur.

## <a name="403-forbidden"></a>403 Förbjudet
Begäran är inte tillåten på grund av något av följande skäl:

* Det går inte att hitta eller har tagits bort medietjänstkontot.
* Media Services-kontot är inaktiverat och typen begäran är inte HTTP GET. Serviceåtgärder returnerar också ett 403-svar.
* Autentiseringstoken innehåller inte användarens autentiseringsuppgifter: AccountName och/eller SubscriptionId. Du hittar den här informationen i användargränssnittstillägget för Media Services för ditt Media Services-konto i Azure Management Portal.
* Det går inte att komma åt resursen.
  
  * Ett försök gjordes att använda en MediaProcessor som inte är tillgänglig för ditt Media Services-konto.
  * Ett försök gjordes att uppdatera en JobTemplate som definierats av Media Services.
  * Ett försök gjordes att skriva över något annat Media Services-kontos Locator.
  * Ett försök gjordes att skriva över något annat ContentKey för ett annat Media Services-konto.
* Det gick inte att skapa resursen på grund av en tjänstkvot som uppnåddes för Media Services-kontot. Mer information om tjänstkvoterna finns i [Kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Hittades inte
Begäran är inte tillåten för en resurs på grund av något av följande skäl:

* Ett försök gjordes att uppdatera en entitet som inte finns.
* Ett försök gjordes att ta bort en entitet som inte finns.
* Ett försök gjordes att skapa en entitet som länkar till en entitet som inte finns.
* Ett försök gjordes att hämta en entitet som inte finns.
* Ett försök gjordes att ange ett lagringskonto som inte är kopplat till Media Services-kontot.  

## <a name="409-conflict"></a>409 Konflikt
Begäran är inte tillåten på grund av något av följande skäl:

* Mer än en AssetFile har det angivna namnet i tillgången.
* Ett försök gjordes att skapa en andra primär AssetFile i tillgången.
* Ett försök gjordes att skapa en ContentKey med det angivna ID som redan används.
* Ett försök gjordes att skapa en sökare med det angivna ID som redan används.
* Mer än en IngestManifestFile har det angivna namnet inom IngestManifest.
* Ett försök gjordes att länka en andra lagringskryptering ContentKey till den lagringskrypterade tillgången.
* Ett försök gjordes att länka samma ContentKey till tillgången.
* Ett försök gjordes att skapa en positionerare till en tillgång vars lagringsbehållare saknas eller inte längre är associerad med tillgången.
* Ett försök gjordes att skapa en positionerare till en tillgång som redan har 5 positionerare som används. (Azure Storage tillämpar gränsen på fem principer för delad åtkomst på en lagringsbehållare.)
* Att länka lagringskonto för en tillgång till en IngestManifestAsset är inte samma sak som lagringskontot som används av det överordnade IngestManifest.  

## <a name="500-internal-server-error"></a>500 Internt serverfel
Under bearbetningen av begäran stöter Media Services på ett visst fel som förhindrar att bearbetningen fortsätter. Detta kan bero på något av följande:

* Det går inte att skapa en tillgång eller ett jobb eftersom mediatjänstkontots tjänstkvotinformation inte är tillgänglig för tillfället.
* Det går inte att skapa en lagringsbehållare för en tillgång eller IngestManifest-bloblagring eftersom kontots lagringskontoinformation inte är tillgänglig för tillfället.
* Andra oväntade fel.

## <a name="503-service-unavailable"></a>503 Tjänsten är inte tillgänglig
Servern kan för närvarande inte ta emot begäranden. Det här felet kan orsakas av för stora begäranden till tjänsten. Begränsningsmekanismen för Media Services begränsar resursanvändningen för program som gör en överdriven begäran till tjänsten.

> [!NOTE]
> Kontrollera felmeddelandet och felkodsträngen för att få mer detaljerad information om orsaken till att du fick 503-felet. Det här felet innebär inte alltid begränsning.
> 
> 

Möjliga statusbeskrivningar är:

* "Servern är upptagen. Tidigare körningar av den här {0} typen av begäran tog mer än sekunder."
* "Servern är upptagen. Mer {0} än begäranden per sekund kan begränsas."
* "Servern är upptagen. Mer {0} än {1} begäranden inom några sekunder kan begränsas."

För att hantera det här felet rekommenderar vi att du använder exponentiell back-off-återförsökslogik. Det innebär att använda successivt längre väntetider mellan försök för på varandra följande felsvar.  Mer information finns i [Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Om du använder [Azure Media Services SDK för .Net](https://github.com/Azure/azure-sdk-for-media-services/tree/master)har logiken för återförsök för 503-felet implementerats av SDK.  
> 
> 

## <a name="see-also"></a>Se även
[Felkoder för hantering av Media Services](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

