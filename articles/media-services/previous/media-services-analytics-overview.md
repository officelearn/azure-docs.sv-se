---
title: Media Analytics på Media Services-plattformen | Microsoft-dokument
description: Översikt över offentlig förhandsversion av Media Analytics, en samling tal- och datorseendetjänster i företagsskala, efterlevnad, säkerhet och global räckvidd
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
ms.openlocfilehash: ab1eba3de474d9ff985e62f491c24fa63be0fa63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069633"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics på Media Services-plattformen 

## <a name="retirement-plans"></a>Pensionsplaner

> [!IMPORTANT]
> Vissa medieprocessorer dras tillbaka. Information om avgångsdatum och mer information finns i avsnittet [äldre komponenter.](legacy-components.md) 

## <a name="overview"></a>Översikt

Fler organisationer använder video som det bästa mediet för att utbilda sina anställda, engagera sina kunder och dokumentera affärsfunktioner. Cloud computing är ett sätt att lagra, strömma och komma åt dessa stora mediefiler. Men i takt med att ett företags bibliotek med videoinnehåll växer behöver det ett lika effektivt sätt att extrahera insikter från innehållet. 

För att tillgodose detta växande behov erbjuder Azure Media Services Azure Media Analytics. Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som det går att direkt agera utifrån från sina videofiler. Media Analytics bygger på mediatjänsters kärnkomponenter och kan hantera mediebearbetning i stor skala dag ett.

Med Media Analytics kan utvecklare snabbt tillföra avancerade videofunktioner till program. Det ger företagsmiljöer den fullständiga skala, efterlevnad, säkerhet och globala räckvidd som krävs av stora organisationer.

Följande diagram visar Media Analytics och andra större delar av Media Services-plattformen. 

![VoD-arbetsflöde](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics-medieprocessorer producerar MP4-filer eller JSON-filer. Om en medieprocessor producerar en MP4-fil kan du hämta filen gradvis. Om en medieprocessor producerar en JSON-fil kan du hämta filen från Azure Blob-lagring. 

## <a name="media-analytics-services"></a>Media Analytics-tjänster

### <a name="indexer"></a>Indexerare
Med Azure Media Indexer kan du göra innehållet sökbart och generera spår med dold textning. Detaljerad information och exempel finns i [Indexera mediefiler med Azure Media Indexer](media-services-index-content.md).

### <a name="motion-detector"></a>Rörelsedetektor
Du kan använda rörelsedetektor för att upptäcka rörelse i en video med stationära bakgrunder. Detta gör det möjligt att kontrollera om det finns falska positiva om rörelsehändelser som upptäckts av övervakningskameror. Detaljerad information och exempel finns i [Rörelseidentifiering för Azure Media Analytics](media-services-motion-detection.md).

### <a name="face-detector"></a>Ansiktsigenkänning
Genom att använda Face Detector, kan du upptäcka människors ansikten och deras känslor, inklusive lycka, sorg och överraskning. Detta har flera användbara branschprogram, som beskrivs senare, inklusive aggregering och analys av reaktioner från personer som deltar i ett evenemang. Detaljerad information och exempel finns i [Ansikts- och känsloidentifiering för Azure Media Analytics](media-services-face-and-emotion-detection.md).

### <a name="video-summarization"></a>Videouppsummering
Videosummering kan hjälpa dig att skapa sammanfattningar av långa videor genom att automatiskt välja intressanta utdrag från källvideon. Den här möjligheten är användbar när du vill ge en snabb översikt över vad som väntar i en lång video. Detaljerad information och exempel finns i [Använda Azure Media Video Thumbnails för att skapa video summering](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optisk teckenigenkänning
Med Azure Media OCR (optisk teckenigenkänning) kan du konvertera textinnehåll i videofiler till redigerbar, sökbar digital text. Du kan sedan automatisera utvinning av meningsfulla metadata från videosignalen för dina media.
### <a name="scalable-face-redaction"></a>Skalbar ansiktsredigering
Azure Media Redactor är en Media Analytics-medieprocessor som erbjuder skalbar ansiktsredigering i molnet. Genom att använda ansiktsutredigering kan du ändra videon så att den blir suddiga för utvalda personer. Du kanske vill använda ansiktsutredigeringstjänsten i nyhetsmedier eller när den allmänna säkerheten är inblandad. Några minuter av bilder som innehåller flera ansikten kan ta timmar att redigera manuellt, men med den här tjänsten tar ansiktsredigering bara några enkla steg. Mer information finns i artikeln [Redigera ansikten med Azure Media Analytics.](media-services-face-redaction.md)

### <a name="content-moderation"></a>Moderering av innehåll
Med Azure Content Moderator kan du använda datorstödd moderering för dina videor. Du vill kanske exempelvis identifiera möjligt innehåll som är olämpligt för barn eller olämpligt innehåll i videor och granska innehåll som flaggats av det mänskliga modereringsteamet. Att manuellt moderera videor för oönskat innehåll är en tidskrävande och dyr uppgift. Med den här tjänsten och tillhörande granskningsverktyg kombinerar du maskinstödd moderering med mänskliga-in-the-loop-funktioner för bästa resultat effektivt och kostnadseffektivt. Mer information finns i artikeln [Bearbeta dina videor med Azure Content Moderator.](media-services-content-moderation.md)

## <a name="common-scenarios"></a>Vanliga scenarier
Media Analytics kan hjälpa organisationer och företag att få fram nya insikter från video och mer effektivt hantera stora mängder videoinnehåll. Här är flera scenarier:

* **Callcenter**. Även med tillkomsten av sociala medier, kund call centers fortfarande underlätta en stor andel av kundservice transaktioner. Kodade i dessa ljuddata är en stor mängd kundinformation som kan analyseras för att uppnå högre kundnöjdhet. Genom att använda Media Indexer kan organisationer extrahera text och skapa sökindex och instrumentpaneler. Sedan kan de extrahera information kring vanliga klagomål, källor till klagomål och andra relevanta uppgifter.
* **Moderering av användargenererat innehåll**. Från nyhetsmedier till polismyndigheter har många organisationer offentliga portaler som accepterar användargenererade medier som videor och bilder. Volymen av innehåll kan öka på grund av oväntade händelser. I dessa scenarier är det svårt att genomföra effektiva manuella granskningar av innehåll för lämplighet. Kunder kan lita på att innehållsmodereringstjänsten fokuserar på innehåll som är lämpligt.

## <a name="media-analytics-media-processors"></a>Medieanalysmedieprocessorer
I det här avsnittet visas Media Analytics-medieprocessorer och hur du använder .NET eller REST för att hämta ett MP-objekt (Media Analytics).

### <a name="mp-names"></a>MP-namn

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Moderator för Azure Media-innehåll

### <a name="net"></a>.NET
Följande funktion tar ett av de angivna MP-namnen och returnerar ett MP-objekt.

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
Se [Azure Media Analytics-demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
Se [meddelandet mediatjänstanalys](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
