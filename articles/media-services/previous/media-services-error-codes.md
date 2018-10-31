---
title: Azure Media Services-felkoder | Microsoft Docs
description: Avsnittet ger en översikt över Azure Media Services-felkoder.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 43e2b2b7f398e8adce0760678c6f0a5208c24c34
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240536"
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services-felkoder
När du använder Microsoft Azure Media Services får du HTTP-felkoder från tjänsten beroende på problem, till exempel autentiseringstoken upphör att gälla för åtgärder som inte stöds i Media Services. Följande är en lista över **HTTP-felkoder** som kan returneras av Media Services och möjliga orsaker för dessa.  

## <a name="400-bad-request"></a>400 Felaktig förfrågan
Begäran innehåller ogiltig information och avvisas på grund av något av följande orsaker:

* En API-version som inte stöds har angetts. Den senaste versionen finns [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).
* API-versionen av Media Services har inte angetts. Information om hur du anger den API-versionen finns i [Media Services Operations REST API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Om du använder .NET eller Java-SDK: er för att ansluta till Media Services har API-version angetts för dig när du försöker och utföra någon åtgärd mot Media Services.
  > 
  > 
* En odefinierad egenskap har angetts. Egenskapsnamnet är i felmeddelandet. Endast de egenskaper som är medlemmar i en specifik entitet kan anges. Se [Azure Media Services REST API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) en lista över entiteter och deras egenskaper.
* Ett ogiltigt egenskapsvärde har angetts. Egenskapsnamnet är i felmeddelandet. Se föregående länk för giltiga egenskapstyper och deras värden.
* Ett egenskapsvärde saknas och måste anges.
* En del av Webbadressen som angetts innehåller ett felaktigt värde.
* Ett försök gjordes att uppdatera en WriteOnce-egenskap.
* Ett försök gjordes att skapa ett jobb som har en Indatatillgången med en primär AssetFile som angavs inte eller kunde inte fastställas.
* Ett försök gjordes att uppdatera en SAS-lokaliserare. SAS-lokaliserare kan bara skapas eller tas bort. Positionerare för direktuppspelning kan uppdateras. Mer information finns i [positionerare](https://docs.microsoft.com/rest/api/media/operations/locator).
* En åtgärd som inte stöds eller fråga skickades in.

## <a name="401-unauthorized"></a>401 Ej behörig
Begäran kunde inte autentiseras (innan den kan auktoriseras) på grund av något av följande orsaker:

* Autentiseringshuvud saknas.
* Dålig autentisering huvudets värde.
  * Token har upphört att gälla. 
  * Token som innehåller en ogiltig signatur.

## <a name="403-forbidden"></a>403 Åtkomst nekas
Förfrågan tillåts inte på grund av något av följande orsaker:

* Media Services-kontot kan inte hittas eller har tagits bort.
* Media Services-kontot är inaktiverat och typ av begäran är inte HTTP GET. Tjänståtgärder returnerar ett 403 svar.
* Autentiseringstoken innehåller inte användarens autentiseringsuppgifter information: AccountName och/eller prenumerations-ID. Du hittar den här informationen i Användargränssnittet för Media Services-tillägget för Media Services-kontot i Azure-hanteringsportalen.
* Resursen är inte tillgänglig.
  
  * Ett försök gjordes att använda en MediaProcessor som inte är tillgängligt för Media Services-kontot.
  * Ett försök gjordes att uppdatera en jobbmall som definieras av Media Services.
  * Ett försök gjordes att skriva över vissa andra Media Services-kontots lokaliserare.
  * Ett försök gjordes att skriva över vissa andra Media Services-kontots ContentKey.
* Det gick inte att skapa resursen på grund av en tjänstkvot nåddes för Media Services-kontot. Läs mer på i beaktande [kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Hittades inte
Begäran är inte tillåten på en resurs på grund av något av följande orsaker:

* Ett försök gjordes att uppdatera en entitet som inte finns.
* Ett försök gjordes att ta bort en entitet som inte finns.
* Ett försök gjordes att skapa en entitet som länkar till en enhet som inte finns.
* Ett försök gjordes att hämta en entitet som inte finns.
* Ett försök gjordes att ange ett lagringskonto som inte är associerad med Media Services-kontot.  

## <a name="409-conflict"></a>409 konflikt
Förfrågan tillåts inte på grund av något av följande orsaker:

* Mer än en AssetFile har det angivna namnet i tillgången.
* Ett försök gjordes att skapa en andra primära AssetFile i tillgången.
* Ett försök gjordes att skapa en ContentKey med det angivna ID: T används redan.
* Ett försök gjordes att skapa en lokaliserare med det angivna ID: T används redan.
* Mer än en IngestManifestFile har det angivna namnet i IngestManifest.
* Ett försök gjordes att länka en andra lagringskryptering ContentKey till storage-krypterad tillgången.
* Ett försök gjordes att länka samma ContentKey till tillgången.
* Ett försök gjordes att skapa en positionerare till en tillgång vars lagringsbehållare saknas eller är inte längre kopplad till tillgången.
* Ett försök gjordes att skapa en positionerare till en tillgång som redan har 5 positionerare används. (Azure Storage framtvingar högst fem principer för delad åtkomst på en lagringsbehållare.)
* Länka storage-konto för en tillgång till en IngestManifestAsset är inte detsamma som det lagringskonto som används av överordnat IngestManifest.  

## <a name="500-internal-server-error"></a>500 Internt serverfel
Under bearbetning av begäran påträffar Media Services ett fel som förhindrar att bearbetningen inte kan fortsätta. Detta kan bero på något av följande:

* Det går inte att skapa en tillgång eller jobbet eftersom kvotinformation för Media Services-konto-tjänsten är otillgänglig.
* Det går inte att skapa en tillgång eller IngestManifest blobblagringsbehållare eftersom kontots lagringskontoinformation är otillgänglig.
* Ett oväntat fel.

## <a name="503-service-unavailable"></a>503 Tjänsten är inte tillgänglig
Servern är för närvarande inte att ta emot begäranden. Det här felet kan orsakas av hög begäranden till tjänsten. Media Services begränsning mekanism begränsar resursanvändningen för program som gör överdrivna för tjänsten.

> [!NOTE]
> Kontrollera felmeddelandet och felkodsträngen för att få mer detaljerad information om orsaken som du fick 503-felet. Det här felet betyder alltid inte begränsning.
> 
> 

Möjliga statusbeskrivningar är:

* ”Servern är upptagen. Tidigare körningar av den här typen av begäran tog mer än {0} sekunder ”.
* ”Servern är upptagen. Mer än {0} begäranden per sekund kan strypas ”.
* ”Servern är upptagen. Mer än {0} begäranden inom {1} sekunder kan vara begränsad ”.

För att hantera det här felet, bör du använda exponentiell backoff-logik. Det innebär att med hjälp av progressivt längre väntar mellan försöken för efterföljande felsvar.  Mer information finns i [hantering av Programblocket tillfälliga](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Om du använder [Azure Media Services SDK för .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), logik för omprövning för 503-felet har implementerats av SDK.  
> 
> 

## <a name="see-also"></a>Se även
[Media Services Management felkoder](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

