---
title: Azure Media Services-felkoder | Microsoft Docs
description: "Avsnittet ger en översikt över Azure Media Services-felkoder."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: 39886a955124429302609dd9d5a7c20ae7f498d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services-felkoder
När du använder Microsoft Azure Media Services kan du få HTTP felkoder från beroende på problem som till exempel autentiseringstoken ut till åtgärder som inte stöds i Media Services-tjänsten. Följande är en lista över **HTTP felkoder** som kan returneras av Media Services och möjliga orsaker för dessa.  

## <a name="400-bad-request"></a>400 Felaktig förfrågan
Begäran innehåller ogiltig information och avvisas på grund av följande orsaker:

* En API-version som inte stöds har angetts. Den senaste versionen finns [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).
* API-versionen av Media Services har inte angetts. Information om hur du anger den API-versionen finns i [Media Services Operations REST API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Om du använder .NET eller Java SDK för att ansluta till Media Services har API-versionen angetts för dig när du försöker och utföra vissa åtgärder mot Media Services.
  > 
  > 
* En odefinierad egenskap har angetts. Egenskapsnamnet är i felmeddelandet. Endast de egenskaper som är medlemmar i en given entitet kan anges. Se [Azure Media Services REST API-referens](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) för en lista över enheter och deras egenskaper.
* Ett ogiltigt egenskapsvärde har angetts. Egenskapsnamnet är i felmeddelandet. Se föregående länk för giltig egenskapstyperna och deras värden.
* Ett värde för egenskapen saknas och måste anges.
* En del av Webbadressen som angetts innehåller ett felaktigt värde.
* Ett försök gjordes att uppdatera en WriteOnce-egenskap.
* Ett försök gjordes att skapa ett jobb som har en inkommande tillgång med en primär AssetFile som angavs inte eller kunde inte fastställas.
* Ett försök gjordes att uppdatera en SAS-lokaliserare. SAS-positionerare kan endast skapas eller tas bort. Strömning positionerare kan uppdateras. Mer information finns i [lokaliserare](https://docs.microsoft.com/rest/api/media/operations/locator).
* En åtgärd som inte stöds eller en fråga skickades.

## <a name="401-unauthorized"></a>401 obehörig
Begäran kunde inte autentiseras (innan den kan godkännas) på grund av följande orsaker:

* Autentiseringshuvud saknas.
* Felaktig autentisering huvudvärde.
  * Token har upphört att gälla. 
  * Token som innehåller en ogiltig signatur.

## <a name="403-forbidden"></a>403 Nekad
Begäran tillåts inte på grund av följande orsaker:

* Media Services-konto kan inte hittas eller har tagits bort.
* Media Services-kontot är inaktiverat och typ av begäran är inte HTTP GET. Tjänståtgärder returneras ett 403 svar.
* Autentiseringstoken innehåller inte information om användarens autentiseringsuppgifter: AccountName och/eller prenumerations-ID. Du hittar den här informationen i Användargränssnittet för Media Services-tillägget för ditt Media Services-konto i Azure-hanteringsportalen.
* Resursen kan inte nås.
  
  * Ett försök gjordes att använda en MediaProcessor som inte är tillgängligt för Media Services-kontot.
  * Ett försök gjordes att uppdatera en jobbmall som definierats av Media Services.
  * Ett försök gjordes att skriva över vissa andra Media Services kontots lokaliserare.
  * Ett försök gjordes att skriva över vissa andra Media Services kontots ContentKey.
* Det gick inte att skapa resursen på grund av en tjänst-kvot har uppnåtts för Media Services-kontot. Mer information om tjänsten kvoter finns [kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 Hittades inte
Begäran är inte tillåten på en resurs på grund av följande orsaker:

* Ett försök gjordes att uppdatera en entitet som inte finns.
* Ett försök gjordes att ta bort en entitet som inte finns.
* Ett försök gjordes att skapa en entitet som länkar till en entitet som inte finns.
* Ett försök gjordes att hämta en entitet som inte finns.
* Ett försök gjordes att ange ett lagringskonto som inte är associerad med Media Services-kontot.  

## <a name="409-conflict"></a>409 konflikt
Begäran tillåts inte på grund av följande orsaker:

* Mer än en AssetFile har det angivna namnet i tillgången.
* Ett försök gjordes att skapa en andra primära AssetFile i tillgången.
* Ett försök gjordes att skapa en ContentKey med angivet Id används redan.
* Ett försök gjordes att skapa en lokaliserare med angivet Id används redan.
* Mer än en IngestManifestFile har det angivna namnet i IngestManifest.
* Ett försök gjordes att länka en andra lagringskryptering ContentKey till tillgången lagring krypteras.
* Ett försök gjordes att länka samma ContentKey till tillgången.
* Ett försök gjordes att skapa en positionerare till en tillgång vars lagringsbehållaren saknas eller är inte längre kopplade till tillgången.
* Ett försök gjordes att skapa en positionerare till en tillgång som redan har 5 lokaliserare används. (Azure Storage tillämpar högst fem principer för delad åtkomst på en lagringsbehållare.)
* Länka storage-konto för en tillgång till en IngestManifestAsset är inte samma som det lagringskontot som används av överordnat IngestManifest.  

## <a name="500-internal-server-error"></a>500 Internt serverfel
Media Services påträffar ett fel som förhindrar bearbetningen fortsätter under bearbetning av begäran. Detta kan bero på något av följande:

* Skapa en tillgång eller jobb misslyckas eftersom Media Services-kontot service-kvotinformation är otillgänglig.
* Skapa en tillgång eller IngestManifest blobblagringsbehållare misslyckas eftersom kontots information om lagringskontot är inte tillgänglig för tillfället.
* Ett oväntat fel.

## <a name="503-service-unavailable"></a>503 inte tillgänglig
Servern är för närvarande inte att ta emot begäranden. Det här felet kan orsakas av långa förfrågningar till tjänsten. Media Services begränsning mekanism begränsar resursanvändningen för program som gör överdriven begäran för tjänsten.

> [!NOTE]
> Kontrollera felmeddelandet och felsträng kod för att få mer detaljerad information om orsaken till att du har fått 503-felet. Det här felet innebär alltid inte begränsning.
> 
> 

Möjliga status är:

* ”Servern är upptagen. Tidigare körs av denna typ av begäran tog mer än {0} sekunder ”.
* ”Servern är upptagen. Mer än {0}-begäranden per sekund kan vara begränsas ”.
* ”Servern är upptagen. Fler än {0}-förfrågningar inom {1} sekunder kan begränsas ”.

Om du vill hantera det här felet, bör du använda exponentiell inte logik. Som innebär att progressivt längre väntar mellan två på varandra följande felsvar försök.  Mer information finns i [tillfälliga fel hanterar programmet Block](https://msdn.microsoft.com/library/hh680905.aspx).

> [!NOTE]
> Om du använder [Azure Media Services SDK för .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master), logik för omprövning för 503-fel har implementerats av SDK.  
> 
> 

## <a name="see-also"></a>Se även
[Media Services Management felkoder](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

