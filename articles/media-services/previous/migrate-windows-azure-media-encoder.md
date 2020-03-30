---
title: Migrera från Windows Azure Media Encoder till Media Encoder Standard | Microsoft-dokument
description: I det här avsnittet beskrivs hur du migrerar från Azure Media Encoder till mediakodaren Standard-medieprocessorn.
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
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513209"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrera från Windows Azure Media Encoder till Media Encoder Standard

I den här artikeln beskrivs stegen för att migrera från den äldre WINDOWS Azure Media Encoder -processorn (WAME) (som dras tillbaka) till mediakodaren Standard.This article discusss the steps for migring from the legacy Windows Azure Media Encoder (WAME) media processor (which is being retired) to the Media Encoder Standard media processor. För pensioneringsdatumen finns i det här [äldre komponentavsnittet.](legacy-components.md)

När du kodar filer med WAME använde kunderna vanligtvis `H264 Adaptive Bitrate MP4 Set 1080p`en namngiven förinställd sträng, till exempel . För att kunna migrera måste koden uppdateras för att använda **mediakodarens standardprocessor** i stället för `H264 Multiple Bitrate 1080p`WAME och en av motsvarande [systemförinställningar](media-services-mes-presets-overview.md) som . 

## <a name="migrating-to-media-encoder-standard"></a>Migrera till Media Encoder Standard

Här är ett typiskt C#-kodexempel som använder den äldre komponenten. 

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

Om du hade skapat en egen kodningsförinställning för WAME med hjälp av schemat finns det ett [motsvarande schema för Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Kända skillnader 

Media Encoder Standard är mer robust, tillförlitlig, har bättre prestanda och ger bättre kvalitet än den äldre WAME-kodaren. Ytterligare: 

* Media Encoder Standard producerar utdatafiler med en annan namngivningskonvention än WAME.
* Media Encoder Standard producerar artefakter som filer som innehåller metadata för [indatafilen](media-services-input-metadata-schema.md) och metadata för [utdatafiler](media-services-output-metadata-schema.md).
* Som dokumenteras på [prissidan](https://azure.microsoft.com/pricing/details/media-services/#encoding) (särskilt i avsnittet Vanliga frågor och svar) får du faktureras baserat på längden på de filer som produceras som utdata när du kodar videor med Media Encoder Standard. Med WAME faktureras du baserat på storleken på indatavideofilerna och utdatavideofilerna.

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna en supportbiljett genom att navigera till [Ny supportförfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Nästa steg

* [Äldre komponenter](legacy-components.md)
* [Prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/#encoding)
