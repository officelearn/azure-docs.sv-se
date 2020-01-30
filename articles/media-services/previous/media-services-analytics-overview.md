---
title: Medieanalys på Media Services plattform | Microsoft Docs
description: Översikt över den allmänt tillgängliga för hands versionen av Medieanalys, en samling tal-och syn tjänster i företags skala, efterlevnad, säkerhet och global räckvidd
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: 4b0d360c11313e086f6ec26e5ee46b8d6f49869a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844351"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Medieanalys på Media Servicess plattformen 

## <a name="retirement-plans"></a>Pensions planer

> [!IMPORTANT]
> Följande tidigare medie processor kommer att dras tillbaka om 2020, se information i följande tabell. 

|Namn på mediebearbetare|Datum för indragning|Ytterligare information|
|---|---|
|[Azure Media Indexer](media-services-index-content.md)|1 oktober 2020|Medie processorn kommer att ersättas av [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Mer information finns i [Migrera från Azure Media Indexer till Azure Media Services video Indexer](migrate-indexer-v1-v2.md)
 
## <a name="overview"></a>Översikt

Fler organisationer använder video som det önskade mediet för att träna sina anställda, engagera sina kunder och dokumentera affärs funktioner. Molnbaserad data behandling är ett sätt att lagra, strömma och komma åt dessa stora mediafiler. Men i takt med att ett företags bibliotek med video innehåll växer, behöver det ett lika effektivt sätt att extrahera insikter från innehållet. 

Azure Media Services erbjuder Azure-medieanalys för att lösa det växande behovet. Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som det går att direkt agera utifrån från sina videofiler. Med hjälp av plattforms komponenterna för kärn Media Services kan Medieanalys hantera medie bearbetning i skala på dag ett.

Med Medieanalys kan utvecklare snabbt få avancerade video funktioner i program. Den ger företags miljöer med fullständig skalning, efterlevnad, säkerhet och global räckvidd som krävs av stora organisationer.

I följande diagram visas Medieanalys och andra större delar av Media Servicess plattformen. 

![VoD-arbetsflöde](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics-medieprocessorer producerar MP4-filer eller JSON-filer. Om en medie processor skapar en MP4-fil kan du hämta filen progressivt. Om en medie processor skapar en JSON-fil kan du ladda ned filen från Azure Blob Storage. 

## <a name="media-analytics-services"></a>Medieanalys tjänster

### <a name="indexer"></a>Indexerare
Med Azure Media Indexer kan du göra innehåll sökbart och generera spår med dold textning. Detaljerad information och exempel finns i [Indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

### <a name="motion-detector"></a>Rörelsedetektor
Du kan använda rörelse detektor för att identifiera rörelse i en video med Station ära bakgrunder. På så sätt kan du kontrol lera om det finns falska positiva identifieringar för rörelse händelser som identifieras av övervaknings kameror. Detaljerad information och exempel finns i [rörelse identifiering för Azure-medieanalys](media-services-motion-detection.md).

### <a name="face-detector"></a>Ansiktsigenkänning
Genom att använda ansikts detektor kan du identifiera folkets ansikten och deras känslor, inklusive glädje, ledsenhet och överraskningar. Detta har flera användbara bransch program, som beskrivs senare, inklusive agg regering och analys av reaktioner hos personer som deltar i en händelse. Detaljerad information och exempel finns i [identifiering av ansikts-och känslo för Azure-medieanalys](media-services-face-and-emotion-detection.md).

### <a name="video-summarization"></a>Video Sammanfattning
Video sammanfattningen kan hjälpa dig att skapa sammanfattningar av långa videor genom att automatiskt välja intressanta kodfragment från käll videon. Den här funktionen är användbar när du vill ge en snabb översikt över vad som förväntas i en lång video. Detaljerad information och exempel finns i [använda Azure Media Video thumbnails för att skapa video Sammanfattning](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optisk teckenläsning
Med Azure Media OCR (optisk tecken läsning) kan du konvertera text innehåll i videofiler till redigerbar, sökbar digital text. Du kan sedan automatisera extraheringen av meningsfulla metadata från video signalen på mediet.
### <a name="scalable-face-redaction"></a>Skalbara ansikts bortredigering
Azure Media Redactor är en Medieanalys medie processor som erbjuder skalbara ansikts bortredigering i molnet. Genom att använda ansikts bortredigering kan du ändra videon så att den oskarpa ytan av valda individer blir oskarp. Du kanske vill använda ansikts bortredigering-tjänsten i nyhets medier eller när offentlig säkerhet är involverad. Några minuter av tagningar som innehåller flera ansikten kan ta timmar till bortredigering manuellt, men med den här tjänsten tar ansikts bortredigering bara några enkla steg. Mer information finns i avsnittet [bortredigering-ansikten med Azure-medieanalys](media-services-face-redaction.md) artikeln.

### <a name="content-moderation"></a>Innehållsmoderering
Med Azure Content Moderator kan du använda dator redigering för dina videor. Du vill kanske exempelvis identifiera möjligt innehåll som är olämpligt för barn eller olämpligt innehåll i videor och granska innehåll som flaggats av det mänskliga modereringsteamet. Att manuellt konfigurera videor för oönskat innehåll är en tids krävande och kostsam uppgift. Med den här tjänsten och tillhör ande gransknings verktyg kombinerar du datorbaserade moderatorer med funktioner för mänsklig ineffektivhet för bästa resultat och kostnads effektivt. Mer information finns i artikeln [bearbeta videor med Azure Content moderator](media-services-content-moderation.md) .

## <a name="common-scenarios"></a>Vanliga scenarier
Medieanalys kan hjälpa organisationer och företag att få nya insikter från video och hantera stora mängder video innehåll på ett mer effektivt sätt. Här följer flera scenarier:

* **Anropa Center**. Även med ankomsten av sociala medier underlättar kund samtals centret en stor del av kund service transaktionerna. Att koda i dessa ljud data är en stor mängd kund information som kan analyseras för att uppnå högre kund nöjdhet. Genom att använda Media Indexer kan organisationer Extrahera text och bygga Sök index och instrument paneler. De kan sedan extrahera information kring vanliga klagomål, klagomåls källor och andra relevanta data.
* **Användardefinierad innehålls moderator**. Från nyhets mediets avsändningar till polis myndigheter har många organisationer offentligt riktade portaler som accepterar användar genererade medier, till exempel videor och bilder. Mängden innehåll kan vara en topp på grund av oväntade händelser. I dessa scenarier är det svårt att utföra effektiva manuella granskningar av innehåll för lämplighet. Kunder kan lita på att innehålls moderator tjänsten fokuserar på innehåll som är lämpligt.

## <a name="media-analytics-media-processors"></a>Medieanalys medie processorer
I det här avsnittet visas Medieanalys medie processorer och hur du använder .NET eller REST för att hämta ett MP-objekt (Media processor).

### <a name="mp-names"></a>MP-namn

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
Följande funktion använder ett av de angivna MP-namnen och returnerar ett MP-objekt.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Begäran:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
    Host: media.windows.net

Svar:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demos
Se [Azure-medieanalys-demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
Se [Media Services Analytics-meddelande](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
