---
title: Direktuppspelning av Azure Media Services med hög tillgänglighet
description: Lär dig hur du växlar över till ett sekundärt Media Services-konto om ett regionalt datacenter avbrott eller fel inträffar.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899235"
---
# <a name="media-services-high-availability-streaming"></a>Direktuppspelning av Media Services-tjänster med hög tillgänglighet

Azure Media Services tillhandahåller för närvarande inte omedelbar redundans av tjänsten om det finns ett regionalt datacenteravbrott eller fel på underliggande komponent eller beroende tjänster. Den här artikeln ger vägledning hur du bygger video-on-demand cross region streaming.

## <a name="prerequisites"></a>Krav

Granska [Hur man bygger ett korsregionalt kodningssystem](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Så här bygger du direktuppspelning mellan regioner på begäran 

* Direktuppspelning av flera regioner på begäran innebär duplicera [tillgångar,](assets-concept.md)principer för [innehållsnyckel](content-key-policy-concept.md) (om de används), [streamingprinciper](streaming-policy-concept.md)och [direktuppspelningspositioner](streaming-locators-concept.md). 
* Du måste skapa principer i båda regionerna och hålla dem uppdaterade. 
* När du skapar streamingpositionerarna vill du använda samma locator-ID-värde, ContentKey ID-värde och ContentKey-värde.  
* Om du kodar innehållet rekommenderas att koda innehållet i region A och publicera det, sedan kopiera det kodade innehållet till region B och publicera det med samma värden som från region A.
* Du kan använda Traffic Manager på värdnamnen för ursprunget och nyckelleveranstjänsten (i Media Services-konfigurationen ser detta ut som en anpassad nyckelserver-URL).

## <a name="next-steps"></a>Nästa steg

Kontrollera:

* [Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon](stream-files-dotnet-quickstart.md)
* [kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
