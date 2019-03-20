---
title: Skapa avancerade arbetsflöden för kodning med Workflow Designer | Microsoft Docs
description: Läs mer om hur du skapar avancerade arbetsflöden för kodning med Workflow Designer.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: fb24c856138bb9e59d9cb3ea07c9182454e6463a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080209"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Skapa avancerade arbetsflöden för kodning med Workflow Designer  
## <a name="overview"></a>Översikt
Den **Arbetsflödesdesigner** är ett stationära Windows-verktyg som används för att utforma och skapa anpassade arbetsflöden för kodning med **Media Encoder Premium Workflow**.
Med kraften i verktyget workflow designer kan du utforma och skapa komplexa arbetsflöden som körs i **Media Encoder Premium**.  

Arbetsflöden kan omfatta kunden beslut logik och branchning utifrån Indatakällan filens egenskaper. Du kan skapa arbetsflöden med åsidosättningsbar egenskaper och dynamiska värden så att även de mest komplexa kodningsuppgifter blir lättare att upprepa och anpassa i molnet.

Exempel-arbetsflöden som du kan skapa omfattar:

* Beslutet bygga arbetsflöden som granskar källinnehållet för matchning av och koda bara önskade utdata-spår.  Det här är användbart genom att ta bort onödigt spåren som genereras av upscaling källinnehållet oavsiktligt.
* Flera indatafiler kan användas för att stödja bildtexter, överlägg och fästa tillsammans innehåll. 

Det här verktyget kan också användas för att ändra någon av våra [publicerade arbetsflöden](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> För att få din kopia av verktyget Arbetsflödesdesigner kan du kontakta mepd@microsoft.com.

När en arbetsflödesfil har skapats kan laddas upp som en tillgång och sedan användas för kodning mediefiler. Information om att koda med **Media Encoder Premium Workflow** med **.NET**, se [avancerad kodning med Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Ändra befintliga arbetsflöden
Standard [publicerade arbetsflöden](media-services-workflow-designer.md#existing_workflows) kan ändras med hjälp av verktyget designer. Du kan hämta standard Arbetsflödesfiler [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen innehåller också en beskrivning av dessa filer.

I följande videoklipp visar hur du kan använda designern.

### <a name="day-1--getting-started"></a>Dag 1 – komma igång
Dag 1 videon beskriver:

* Designer översikt
* Grundläggande arbetsflöden – ”Hello World”
* Skapa flera utdata MP4-filer för användning med Azure Media Services-strömning

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dag 2
Dag 2 videon beskriver:

* Olika scenarier för filhantering av källa – hantering av ljud
* Arbetsflöden med avancerad logik
* Graph-faser

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dag 3
Dag 3 videon beskriver:

* Skript i arbetsflöden/skisser
* Begränsningar i den aktuella kodaren
* FRÅGOR OCH SVAR

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Om du behöver stöd för eller har frågor om hur du skapar anpassade arbetsflöden i Workflow designer-verktyget kan du skicka e-postmeddelande till mepd@microsoft.com.

## <a name="see-also"></a>Se även
[Azure Premium-kodare Workflow Designer utbildningsvideor](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

