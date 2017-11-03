---
title: "Skapa avancerade kodning arbetsflöden med Arbetsflödesdesignern | Microsoft Docs"
description: "Lär dig mer om hur du skapar avancerade kodning arbetsflöden med Arbetsflödesdesignern."
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: ef277710713d342eb9aaaf119e2985e8c1d82d0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Skapa avancerade arbetsflöden för kodning med Workflow Designer
## <a name="overview"></a>Översikt
Den **Arbetsflödesdesignern** är ett stationära Windows-verktyg som används för att skapa anpassade arbetsflöden för kodning med **Media Encoder Premium arbetsflöde**.
Med kraften i verktyget workflow designer kan du utforma och skapa komplexa arbetsflöden som körs i **Media Encoder Premium**.  

Arbetsflöden kan omfatta kunden beslut logik och förgrening utifrån indatakälla filens egenskaper. Du kan skapa arbetsflöden med åsidosättningsbar egenskaper och dynamiska värden så att även de mest komplexa kodning aktiviteterna enkelt att upprepa och anpassa i molnet.

Exempel arbetsflöden som du kan skapa inkluderar:

* Beslutet arbetsflöden inspektera källinnehållet för matchning och koda önskade utgående spår.  Detta är helfpul genom att ta bort onödigt spår som genereras av upscaling källa innehåll råkat.
* Flera inkommande filer kan användas för att stödja titlar, överlägg och fästa tillsammans innehåll. 

Det här verktyget kan också användas för att ändra någon av våra [publicerade arbetsflöden](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> För att få en kopia av verktyget Workflow Designer kan du kontakta mepd@microsoft.com.
> 
> 

När en arbetsflödesfil har skapats kan överföras som en tillgång och sedan kan användas för kodning mediefiler. Information om att koda med **Media Encoder Premium arbetsflöde** med **.NET**, se [avancerade encoding med Media Encoder Premium arbetsflöde](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Ändra befintliga arbetsflöden
Standard [publicerade arbetsflöden](media-services-workflow-designer.md#existing_workflows) kan ändras med hjälp av verktyget designer. Du kan hämta standard Arbetsflödesfiler [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen innehåller också en beskrivning av dessa filer.

Följande videoklipp visar hur du använder designern.

### <a name="day-1--getting-started"></a>Dag 1 – komma igång
Dag 1 video omfattar:

* Designer översikt
* Grundläggande arbetsflöden – ”Hello World”
* Skapa flera utdata MP4-filer för användning med Azure Media Services-direktuppspelning

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dag 2
Dag 2 video omfattar:

* Olika scenarier för filhantering i källan – hantering av ljud
* Arbetsflöden med avancerad logik
* Diagrammet faser

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dag 3
Dag 3 video omfattar:

* Skript inuti arbetsflöden/ritningarna
* Begränsningar med aktuella kodaren
* FRÅGOR OCH SVAR

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

Om du behöver stöd för eller har frågor om hur du skapar anpassade arbetsflöden i Workflow designer verktyget kan skicka e-postmeddelande till mepd@microsoft.com.

## <a name="see-also"></a>Se även
[Azure Premium-kodare arbetsflödet Designer utbildning videor](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

