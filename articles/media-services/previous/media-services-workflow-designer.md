---
title: Skapa avancerade kodningsarbetsflöden med Arbetsflödesdesignern | Microsoft-dokument
description: Läs mer om hur du skapar avancerade kodningsarbetsflöden med Arbetsflödesdesignern.
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
ms.date: 03/20/2019
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 4dceb558532305c6d2e84563e25ab05508423090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72801951"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Skapa avancerade arbetsflöden för kodning med Workflow Designer  
## <a name="overview"></a>Översikt
**Arbetsflödesdesignern** är ett Windows-skrivbordsverktyg som används för att utforma och skapa anpassade arbetsflöden för kodning med **Media Encoder Premium Workflow**.
Genom att använda kraften i arbetsflödesdesignerverktyget kan du utforma och skapa komplexa arbetsflöden som körs i **Media Encoder Premium**.  

Arbetsflöden kan innehålla kundbeslutslogik och förgrening baserat på egenskaperna för indatakällfilen. Du kan skapa arbetsflöden med övergripande egenskaper och dynamiska värden för att göra även de mest komplexa kodningsuppgifterna enkla att upprepa och anpassa i molnet.

Exempel på arbetsflöden som du kan skapa är:

* Beslutsbaserade arbetsflöden som kontrollerar källinnehållet efter upplösning och kodar endast önskade utdataspår.  Detta är till hjälp genom att eliminera bortkastade spår som skulle genereras av uppskalning källinnehållet oavsiktligt.
* Flera indatafiler kan användas för att stödja bildtexter, överlägg och sy ihop innehåll. 

Detta verktyg kan också användas för att ändra någon av våra [publicerade arbetsflöden](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Kontakta dig för att få ditt mepd@microsoft.comexemplar av verktyget Arbetsflödesdesigner.

När en arbetsflödesfil har skapats kan den överföras som en tillgång och sedan användas för att koda mediefiler. Information om hur du **kodar med Media Encoder Premium Workflow** med **.NET**finns i [Avancerad kodning med Media Encoder Premium Workflow](media-services-encode-with-premium-workflow.md).

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Ändra befintliga arbetsflöden
[Standardpublicerade arbetsflöden](media-services-workflow-designer.md#existing_workflows) kan ändras med hjälp av designerverktyget. Du kan hämta standardarbetsflödesfilerna [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Mappen innehåller också en beskrivning av dessa filer.

Följande videor visar hur du använder designern.

### <a name="day-1--getting-started"></a>Dag 1 – Komma igång
Dag 1 videoomslag:

* Översikt över designer
* Grundläggande arbetsflöden - "Hello World"
* Skapa flera MP4-utdatafiler som kan användas med direktuppspelning av Azure Media Services

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Dag 2
Dag 2 videoomslag:

* Olika källfilscenarier – hantera ljud
* Arbetsflöden med avancerad logik
* Diagramsteg

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Dag 3
Dag 3 videoomslag:

* Skript inuti arbetsflöden/ritningar
* Begränsningar med den aktuella kodaren
* Frågor och svar

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna en supportbiljett genom att navigera till [Ny supportförfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Utbildningsvideor för Azure Premium-kodare](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

