---
title: Media Analytics på Media Services-plattformen | Microsoft Docs
description: Översikt över offentliga förhandsversionen av Media Analytics, en samling tal- och visuellt innehåll-tjänster på företagsnivå, efterlevnad, säkerhet och global räckvidd
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
ms.date: 02/08/2019
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: c60fd90adda4c362b15fe2e324aa55a581c9e59a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003780"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics på Media Services-plattformen 

## <a name="overview"></a>Översikt
Fler och fler organisationer använder video som önskade medium att träna sina anställda, engagera kunderna och dokumentera företagsfunktioner. Molnbaserad databehandling är ett sätt att lagra, strömma och komma åt dessa stora mediafiler. Men eftersom ett bibliotek med videoinnehåll växer, måste en lika effektiva sättet att extrahera insikter från innehållet. 

Azure Media Services erbjuder Azure Media Analytics för att tillgodose det växande behovet. Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som det går att direkt agera utifrån från sina videofiler. Skapat med hjälp av kärnplattformskomponenter för Media Services kan kan Media Analytics hantera media i stor skala från dag ett.

Med Media Analytics kan utvecklare snabbt ta med avancerade funktioner för video i program. Det ger företagsmiljöer fullskalig, efterlevnad, säkerhet och globala räckvidd som krävs av stora organisationer.

Följande diagram visar Media Analytics och andra viktiga delar av Media Services-plattformen. 

![VoD-arbetsflöde](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics-medieprocessorer producerar MP4-filer eller JSON-filer. Om en medieprocessor producerar en MP4-fil, kan du hämta filen progressivt. Om en medieprocessor producerar en JSON-fil, kan du hämta filen från Azure Blob storage. 

## <a name="media-analytics-services"></a>Medieanalys-tjänster

### <a name="indexer"></a>Indexerare
Du kan göra innehåll sökbart och generera textning spårar med Azure Media Indexer. Förhandsversion av Azure Media Indexer 2 har jämfört med den tidigare versionen stöd för snabbare indexering och bredare språk. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska, portugisiska och arabiska. Mer information och exempel finns i [bearbeta videor med Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Videostabilisering
Microsoft Hyperlapse kombinerar videostabiliserings och tidsfördröjning funktionen för att skapa snabb, använda videor från ditt innehåll för långa. Förutom att skapa tidsfördröjning video kan använda du Videostabilisering för att skapa stabil videor från skakiga videor som hämtats via mobiltelefoner och kameror. Mer information och exempel finns i [Hyperlapse mediefiler med Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Rörelsedetektor
Du kan använda Rörelseidentifiering för att identifiera rörelse i en video med stillastående bakgrunder. Detta gör det möjligt att söka efter falska positiva identifieringar på rörelsehändelser som identifieras av övervakningskameror. Mer information och exempel finns i [Rörelseidentifiering för Azure Media Analytics](media-services-motion-detection.md).
### <a name="face-detector"></a>Ansiktsigenkänning
Med Ansiktsigenkänning kan identifiera du människors ansikten och deras känslor, inklusive lycka, ledsenhet och förvåning. Detta har flera användbara branschspecifika program, som beskrivs senare, inklusive aggregering och analys av reaktioner människor kommer du till en händelse. Mer information och exempel finns i [Ansikts- och känslo-identifiering för Azure Media Analytics](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Sammanfattning av video
Videosammanfattning kan hjälpa dig att skapa sammanfattningar av långa videofilmer genom att automatiskt välja intressanta kodavsnitt från källvideo. Den här möjligheten är användbart när du vill ge en snabb överblick över vad som händer i en lång video. Mer information och exempel finns i [Använd Azure Media Video Thumbnails för att skapa videosammanfattning](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optisk teckenigenkänning
Du kan konvertera textinnehåll i videofiler till redigerbara och sökbara digitala text med Azure Media OCR (optisk teckenläsning). Sedan kan du automatisera extrahering av användbara metadata från video signalen medieinnehåll.
### <a name="scalable-face-redaction"></a>Skalbar ansiktsredigering
Azure Media Redactor är en Media Analytics-mediebearbetare som erbjuder skalbara ansiktsredigering i molnet. Med hjälp av ansiktsredigering kan ändra du videon om du vill minska ansikten för valda individer. Du kanske vill använda tjänsten ansiktsredigering i nyhetsmedier eller om allmän säkerhet är inblandad. Några minuter med material som innehåller flera ansikten kan ta timmar att redigera manuellt, men i den här tjänsten ansiktsredigering tar bara några få enkla steg. Mer information finns i den [redigera ansikten med Azure Media Analytics](media-services-face-redaction.md) artikeln.
### <a name="content-moderation"></a>Innehållsmoderering
Azure Content Moderator kan du använda datorstödd moderering för dina videor. Du kanske exempelvis vill identifiera möjliga vuxet eller olämpligt innehåll i videor och granska flaggade innehållet av mänskliga moderering-team. Manuellt kontrollera videor för oönskade innehåll är en tidskrävande och dyrt aktivitet. Med den här tjänsten och associerade med granskningsverktygen kan kombinera du datorstödd moderering med human-i-the-loop funktioner för bästa resultat på ett effektivt och kostnadseffektivt sätt. Mer information finns i den [bearbeta videor med Azure Content Moderator](media-services-content-moderation.md) artikeln.

## <a name="common-scenarios"></a>Vanliga scenarier
Media Analytics kan hjälpa organisationer och företag få djupare förståelse från nya insikter från video och mer hantera effektivt stora volymer av videoinnehåll. Här följer flera scenarier:

* **Anropa Datacenter**. Även med ankomsten av sociala medier underlätta kunden call Center fortfarande en stor del av kundservice transaktioner. Kodning i den här ljuddata är en stor mängd kundinformation som kan analyseras för att uppnå högre kundnöjdhet. Med hjälp av Media Indexer kan organisationer extrahera text och skapa sökindex och instrumentpaneler. De kan sedan extrahera intelligence runt vanligt klagomål, källor för klagomål och andra relevanta data.
* **Användargenererade innehållsmoderering**. Många organisationer har från nyhetsmedier nyhetskanaler att polis avdelningar, offentliga portaler som accepterar användargenererade media som videor och bilder. Mängden innehåll kan skalas på grund av oväntade händelser. Det är svårt att genomföra effektiva manuell granskning av innehåll efter lämplighet i dessa scenarier. Kunderna kan lita på tjänsten moderering av innehåll för att fokusera på att innehåll som är lämpligt.
* **Övervakning**. Kommer en växande inventering av övervakning video med en ökning i användning av IP-kameror. Manuell granskning övervakning video är tid intensiv och känslig för mänskliga fel. Medieanalys tillhandahåller tjänster som rörelsedetektering, ansiktsigenkänning och Videostabilisering till att förenkla processen att granska, hantera och skapa derivat enklare.

## <a name="media-analytics-media-processors"></a>Media Analytics-medieprocessorer
Det här avsnittet visar en lista över Media Analytics-medieprocessorer och visar hur du använder .NET eller REST för att få ett mediaobjekt för processor (MP).

### <a name="mp-names"></a>MP-namn
* Förhandsversion av Azure Media Indexer 2
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
Följande funktion tar ett av de angivna MP-namn och returnerar ett MP-objekt.

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

## <a name="demos"></a>Demos
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
