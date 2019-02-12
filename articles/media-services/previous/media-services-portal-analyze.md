---
title: Analysera dina medier med Azure-portalen | Microsoft Docs
description: 'Det här avsnittet beskrivs hur du bearbetar media med Media Analytics mediebearbetare (MP: er) med Azure portal.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 02b972c0591bb05f72887e3e1c3d057d373ff48c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004884"
---
# <a name="analyze-your-media-using-the-azure-portal-legacy"></a>Analysera dina medier med Azure-portalen (äldre)
> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information finns i [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Översikt
Azure Media Services Analytics är en samling komponenter för talinnehåll och visuellt innehåll (på företagsnivå, efterlevnad, säkerhet och globala räckvidd) som gör det enklare för organisationer och företag att dra användbara slutsatser utifrån videofiler. Mer detaljerad översikt över Azure Media Services Analytics finns i [detta](media-services-analytics-overview.md) avsnittet. 

Det här avsnittet beskrivs hur du bearbetar media med Media Analytics mediebearbetare (MP: er) med Azure portal. Media Analytics MPs producerar MP4-filer eller JSON-filer. Om en medieprocessor har producerat en MP4-fil kan hämta du filen progressivt. Om en medieprocessor har producerat en JSON-fil kan hämta du filen från Azure blob storage. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Välj en tillgång som du vill analysera
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I fönstret **Inställningar** väljer du **Tillgångar**.  
   
    ![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Välj den tillgång som du vill analysera och tryck på den **analysera** knappen.
   
    ![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. I den **bearbeta medietillgången med Medieanalys** fönstret Välj processorn. 
   
    Resten av artikeln förklarar varför och hur du använder varje processor. 
5. Tryck på **skapa** att starta ett jobb.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Den **Azure Media Indexer** medieprocessor kan du göra mediefiler och innehåll sökbart samt generera dold textning spår. Det här avsnittet ger viss information om alternativ som du anger för den här MP.

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Språk
Naturligt språk ska identifieras i multimediefilen. Till exempel engelska eller spanska. 

### <a name="captions"></a>Textning
Du kan välja en etikett-format som genereras från ditt innehåll. Ett indexeringsjobb kan generera filer med dold textning i följande format:  

* **SAMI**
* **TTML**
* **WebVTT**

Stängd textning (kopia) filer i formaten som kan användas för att göra det tillgängligt för personer med funktionshinder höra ljud- och bildfiler.

### <a name="aib-file"></a>AIB-fil
Välj det här alternativet om du vill generera en Audio Index-Blob-fil för användning med den anpassade SQL Server-IFilter. Mer information finns i [detta](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogg.

### <a name="keywords"></a>Nyckelord
Välj det här alternativet om du vill generera en XML-fil med nyckelord. Den här filen med nyckelord extraheras från talinnehållet med frekvens och förskjutning information.

### <a name="job-name"></a>Jobbnamn
Ett eget namn som hjälper dig identifiera jobbet. [Detta](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som hjälper dig identifiera utdata-innehåll. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Azure Media Hyperlapse är en HP som skapar smooth timelapse-video från första personen eller åtgärd kamera innehåll.  Mer information finns i [detta](media-services-hyperlapse-content.md) avsnitt. Det här avsnittet ger viss information om alternativ som du anger för den här MP.

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Hastighet
Ange den hastighet med vilken hastighet indatavideon ska ha. Utdata är en stabila och timelapse-återgivning av indatavideo.

### <a name="job-name"></a>Jobbnamn
Ett eget namn som hjälper dig identifiera jobbet. [Detta](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som hjälper dig identifiera utdata-innehåll. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Den **Ansiktsigenkänning i Azure Media** mediebearbetare (MP) kan du räkna, spåra förflyttningar och även för mätare målgrupp deltagande och reaktion via ansiktsuttryck. Den här tjänsten innehåller två funktioner: 

* **Ansiktsigenkänning**
  
    Ansiktsigenkänning söker efter och spårar ansikten i en video. Flera ansikten kan identifieras och spåras senare när de flyttas runt, med tid och plats metadata som returneras i en JSON-fil. Under spårning, görs ett försök att ge en konsekvent ID till samma sida när personen som flyttas på skärmen, även om de hindras eller kort lämna ramen.
  
  > [!NOTE]
  > Den här services utför inte ansiktsigenkänning. En person som lämnar ramen eller blir skymd för länge får ett nytt ID när de kommer tillbaka.
  > 
  > 
* **Känsloigenkänning**
  
    Känsloigenkänning är en valfri komponent i ansikte identifiering Mediebearbetare som returnerar analysis på flera känslomässig attribut från de ansikten har identifierats, inklusive lycka, sorg, rädsla, ilska, med mera. 

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Identifieringsläge
En av följande lägen kan användas av processorn:

* Ansiktsigenkänning
* känsloigenkänning per ansikte
* Samlad känsloigenkänning

### <a name="job-name"></a>Jobbnamn
Ett eget namn som hjälper dig identifiera jobbet. [Detta](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som hjälper dig identifiera utdata-innehåll. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Den **Rörelseidentifiering för Azure Media** mediebearbetare (MP) hjälper dig att effektivt identifiera avsnitt av intresse i ett annat sätt långa och primärdomänkontrollant video. Rörelser kan användas på statisk övervakningskameror för att identifiera avsnitt av videon där rörelse inträffar. Den genererar en JSON-fil som innehåller en metadata med tidsstämplar och den omgivande regionen där händelsen inträffade.

Riktade mot security video flöden, kan den här tekniken kategorisera rörelse i relevanta händelser och falska positiva identifieringar som skuggor och ljusförändringar. På så sätt kan du generera säkerhetsaviseringar från kameran feeds utan som skräppost med oändliga irrelevanta händelser samtidigt kunna extrahera stund intressanta från extremt långa övervakning videor.

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Den här processorn kan hjälpa dig att skapa sammanfattningar av långa videofilmer genom att automatiskt välja intressanta kodavsnitt från källvideo. Detta är användbart när du vill ge en snabb överblick över vad som händer i en lång video. Mer information och exempel finns i [Använd Azure Media Video Thumbnails för att skapa en Videosammanfattning](media-services-video-summarization.md)

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Jobbnamn
Ett eget namn som hjälper dig identifiera jobbet. [Detta](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som hjälper dig identifiera utdata-innehåll. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Den här processorn hjälper dig att identifiera potentiellt vuxet eller olämpligt innehåll i videor. Processorn identifierar automatiskt skärmbilder och nyckelbildrutor i videon. Den poängsätter nyckelrutorna för möjliga vuxet eller olämpligt innehåll och föreslår granskningar baserat på standardtröskelvärden. Mer information och exempel finns i [Använd Azure Media Content Moderator till måttlig videor](media-services-content-moderation.md)

![Måttlig videor](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Använd ”2.0”.

### <a name="mode"></a>Läge
Version 2.0 ignorera den `Mode` inställningen.

## <a name="next-steps"></a>Nästa steg
Visa Media Services utbildningsvägar.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
