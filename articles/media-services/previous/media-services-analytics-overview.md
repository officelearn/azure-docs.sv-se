---
title: Media Analytics på plattformen Media Services | Microsoft Docs
description: Översikt över förhandsversion av Media Analytics, en samling tal- och vision tjänster på företagsnivå, kompatibilitet, säkerhet och globalt omfattande
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: 0ac8b9fad35267ceaec5b5acec4722b6005f68a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics på plattformen Media Services
## <a name="overview"></a>Översikt
Flera organisationer använder video som önskade normal träna sina anställda, engagera kunderna och dokumentera funktionerna. Molnet är ett sätt att lagra strömma och komma åt dessa stora mediefiler. Men eftersom ett bibliotek med videoinnehåll växer måste ett lika effektivt sätt att extrahera insikter från innehållet. 

Azure Media Services erbjuder Azure Media Analytics för att tillgodose det växande behovet. Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som det går att direkt agera utifrån från sina videofiler. Inbyggda med hjälp av Media Services-plattformen kärnkomponenter kan Media Analytics hantera media bearbetning i stor skala på dagen.

Med Media Analytics sätta utvecklare snabbt avancerade video funktionerna i program. Det ger företagsmiljöer full skala, kompatibilitet, säkerhet och globalt omfattande krävs för stora organisationer.

Följande diagram visar Media Analytics och andra viktiga delar i plattformen Media Services. 

![VoD-arbetsflöde](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics-medieprocessorer producerar MP4-filer eller JSON-filer. Om en medieprocessor producerar MP4-fil, kan du hämta filen progressivt. Om en medieprocessor producerar en JSON-fil, kan du hämta filen från Azure Blob storage. 

## <a name="media-analytics-services"></a>Media Analytics-tjänster

### <a name="indexer"></a>Indexerare
Med Azure Media Indexer ska göra du innehåll sökbara och generera-textning spår. Azure Media Indexer 2 Preview har jämfört med den tidigare versionen stöd för snabbare indexering och bredare språk. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska, portugisiska och arabiska. Mer detaljerad information och exempel finns [bearbeta videor med Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Videostabilisering
Microsoft Hyperlapse kombinerar videostabiliserings och tidsfördröjning möjlighet att snabbt skapa konsumeras videor från långa-innehåll. Förutom att skapa tidsfördröjning video kan använda du Videostabilisera för att skapa stabila videor från shaky videor som hämtats via mobiltelefoner och kameror. Mer detaljerad information och exempel finns [Videostabilisera mediefiler med Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Rörelsedetektor
Du kan använda rörelsedetektor för att identifiera rörelse i en video med stilla bakgrund. Detta gör det möjligt att söka efter falska positiva identifieringar på rörelse händelser som identifieras av övervakningskameror. Mer detaljerad information och exempel finns [Rörelseidentifiering för Azure Media Analytics](media-services-motion-detection.md).
### <a name="face-detector"></a>Ansiktsigenkänning
Med hjälp av framsidan detektor, kan du identifiera personer ytor och deras emotikoner, inklusive lycka, sadness och oväntat. Detta har flera användbara branschspecifika program, beskrivs senare, inklusive aggregering och analys av reaktion på personer som deltar i en händelse. Mer detaljerad information och exempel finns [Ansikts- och känslo identifiering för Azure Media Analytics](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Sammanfattning av video
Sammanfattning av video kan hjälpa dig skapa sammanfattningar av långa videor automatiskt genom att intressanta kodavsnitt från källvideo. Den här möjligheten är användbart när du vill ge en snabb överblick över vad som händer i en lång video. Mer detaljerad information och exempel finns [Använd Azure Media Video miniatyrer för att skapa sammanfattning av video](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optisk teckenigenkänning
Du kan konvertera textinnehåll i videofiler till redigerbar, sökbara digitala text med Azure Media OCR (OCR). Sedan kan du automatisera extrahering av beskrivande metadata från media video signalen.
### <a name="scalable-face-redaction"></a>Skalbar ansikte bortredigering
Azure Media Redactor är en Media Analytics medieprocessor som erbjuder skalbara ansikte bortredigering i molnet. Du kan ändra videon om du vill minska ytor valda personer med hjälp av framsidan bortredigering. Du kanske vill använda tjänsten ansikte bortredigering i Nyheter media eller allmän säkerhet ingår. Några minuter med material som innehåller flera ytor kan ta timmar att redigera bort manuellt, men med den här tjänsten ansikte bortredigering tar bara några få enkla steg. Mer information finns i [redigera bort personerna bakom Azure Media Analytics](media-services-face-redaction.md) artikel.
### <a name="content-moderation"></a>Innehållsmoderering
Azure innehåll kontrollanten kan du använda datorn stödd avbrottsmoderering för dina videor. Du kanske vill identifiera möjliga vuxna och dyr innehåll i videor och granska flaggade innehållet från ditt mänsklig måtta. Manuellt kontrollera videor för oönskade innehåll är en tidskrävande och dyrt åtgärd. Med den här tjänsten och associerade granska verktyg kombinera datorn stödd avbrottsmoderering med hr-i-the-loop funktioner för bästa resultat effektivt och kostnadseffektivt sätt. Mer information finns i [bearbeta videor med Azure innehåll kontrollant](media-services-content-moderation.md) artikel.

## <a name="common-scenarios"></a>Vanliga scenarier
Media Analytics hjälper organisationer och företag glean nya insikter från video och mer hantera effektivt stora volymer av videoinnehåll. Här följer några scenarier:

* **Anropa Datacenter**. Även med ankomsten av sociala medier underlätta kunden call Center fortfarande en stor del av kundservice transaktioner. Kodning i den här ljuddata är en stor mängd kundinformation som kan analyseras för att uppnå högre nöjda. Med hjälp av Media indexeraren organisationer extrahera text och skapa sökindex och instrumentpaneler. De kan sedan extrahera intelligence runt vanligt klagomål, källor klagomål och andra relevanta data.
* **Användargenererade innehåll avbrottsmoderering**. Många organisationer har från nyheter media uttag till polis avdelningar, offentliga portaler som accepterar användargenererade media, till exempel videoklipp och bilder. Mängden innehåll kan ökar på grund av oväntade händelser. I dessa fall är det svårt att genomföra effektiva manuell granskning av innehållet för lämplighet. Kunder kan förlitar sig på tjänsten innehåll avbrottsmoderering att fokusera på innehåll som är lämpligt.
* **Övervakning**. Kommer en växande inventering av övervakning video med IP-kameror tillväxt används. Manuell granskning övervakning video är tid intensiv och risken för mänskliga fel är stor. Media Analytics tillhandahåller tjänster som rörelseidentifiering, står inför identifiering och Videostabilisera att processen för granskning, hantera och skapa derivat enklare.

## <a name="media-analytics-media-processors"></a>Media Analytics-medieprocessorer
Det här avsnittet listar Media Analytics-medieprocessorer och visar hur du använder .NET eller REST för att hämta objektet en media-processor (HP).

### <a name="mp-names"></a>HP-namn
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media innehåll kontrollant

### <a name="net"></a>.NET
Följande funktion använder ett av de angivna MP-namn och returnerar ett HP-objekt.

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
    x-ms-version: 2.12
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

## <a name="demos"></a>Demonstrationer
Se [Azure Media Analytics demonstrationer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
Se [Media Services Analytics meddelande](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
