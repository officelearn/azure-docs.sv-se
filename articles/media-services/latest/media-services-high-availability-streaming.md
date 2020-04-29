---
title: Azure Media Services strömning med hög tillgänglighet
description: Lär dig hur du växlar över till ett sekundärt Media Services konto om det uppstår ett avbrott eller fel i ett regionalt Data Center.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78899235"
---
# <a name="media-services-high-availability-streaming"></a>Media Services strömning med hög tillgänglighet

Azure Media Services tillhandahåller för närvarande inte omedelbar redundansväxling av tjänsten om det uppstår ett avbrott i regionala Data Center eller fel i underliggande komponenter eller beroende tjänster. Den här artikeln innehåller information om hur du skapar strömning på begäran mellan flera regioner.

## <a name="prerequisites"></a>Krav

Läs [om hur du skapar ett regionalt kodnings system](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Så här skapar du strömning på begäran mellan flera regioner 

* Video-on-demand-strömning mellan regioner innebär att duplicera [till gångar](assets-concept.md), [innehålls nyckel principer](content-key-policy-concept.md) (om de används), [strömmande principer](streaming-policy-concept.md)och [strömmande positionerare](streaming-locators-concept.md). 
* Du måste skapa principerna i båda regionerna och se till att de är uppdaterade. 
* När du skapar strömmande positionerare ska du använda samma Locator ID-värde, ContentKey-ID-värde och ContentKey-värde.  
* Om du kodar innehållet, uppmanas du att koda innehållet i region A och publicera det och sedan kopiera det kodade innehållet till region B och publicera det med samma värden som i region A.
* Du kan använda Traffic Manager på värd namnen för ursprunget och nyckel leverans tjänsten (i Media Services konfiguration ser detta ut som en anpassad nyckel server-URL).

## <a name="next-steps"></a>Nästa steg

Kontrollera:

* [Självstudie: koda en fjärrfil baserat på URL och strömma videon](stream-files-dotnet-quickstart.md)
* [kod exempel](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
