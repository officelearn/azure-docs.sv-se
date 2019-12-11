---
title: Migrera från Windows Azure Media Encoder till Media Encoder Standard | Microsoft Docs
description: I det här avsnittet beskrivs hur du migrerar från Azure Media Encoder till Media Encoder Standard medie processor.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 068b8b0a6e248caa70a8f88ea55d07c30a9437f0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997174"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrera från Windows Azure Media Encoder till Media Encoder Standard

I den här artikeln beskrivs stegen för att migrera från den äldre Windows Azure Media Encoder-processorn (WAME), som dras tillbaka den 31 mars 2020 till Media Encoder Standard medie processor.

När du kodar filer med WAME använder kunderna vanligt vis en namngiven förinställd sträng, till exempel `H264 Adaptive Bitrate MP4 Set 1080p`. För att du ska kunna migrera måste koden uppdateras för att använda **Media Encoder Standard** medie processor i stället för WAME, och en av motsvarande [system för inställningar](media-services-mes-presets-overview.md) som `H264 Multiple Bitrate 1080p`. 

## <a name="migrating-to-media-encoder-standard"></a>Migrera till Media Encoder Standard

Här är ett typiskt C# kod exempel som använder den äldre komponenten. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Här är den uppdaterade versionen som använder Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Avancerade scenarier 

Om du har skapat en egen kodnings för inställning för WAME med dess schema, finns det ett [motsvarande schema för Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Kända skillnader 

Media Encoder Standard är stabilare, tillförlitlig, har bättre prestanda och ger bättre kvalitet på utdata än den äldre WAME-kodaren. Ytterligare: 

* Media Encoder Standard producerar utdatafiler med en annan namngivnings konvention än WAME.
* Media Encoder Standard skapar artefakter, till exempel filer som innehåller metadata för [indatafilen](media-services-input-metadata-schema.md) och [metadata för utdatafilen](media-services-output-metadata-schema.md).
* Som dokumenteras på [sidan med priser](https://azure.microsoft.com/pricing/details/media-services/#encoding) (särskilt i avsnittet med vanliga frågor och svar), när du kodar videor med Media Encoder Standard, faktureras du utifrån de filer som skapas som utdata. Med WAME faktureras du utifrån storlekarna på indata-videofiler och video fil (er).

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Nästa steg

* [Äldre komponenter](legacy-components.md)
* [Sidan prissättning](https://azure.microsoft.com/pricing/details/media-services/#encoding)
