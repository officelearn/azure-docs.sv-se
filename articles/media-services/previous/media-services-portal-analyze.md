---
title: Analysera dina media med Hjälp av Azure-portalen | Microsoft-dokument
description: I det här avsnittet beskrivs hur du bearbetar dina media med Media Analytics-medieprocessorer med hjälp av Azure-portalen.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 0e49e90209c7337081458b7c214d27b37d3b4da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74462610"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analysera dina media med Azure-portalen 

> [!IMPORTANT]
> Granska [pensionsplaner](media-services-analytics-overview.md#retirement-plans) för vissa medieprocessorer.

## <a name="overview"></a>Översikt
Azure Media Services Analytics är en samling tal- och visionskomponenter (i företagsskala, efterlevnad, säkerhet och global räckvidd) som gör det enklare för organisationer och företag att härleda användbara insikter från sina videofiler. Mer detaljerad översikt över Azure Media Services Analytics finns [i det här](media-services-analytics-overview.md) avsnittet. 

I det här avsnittet beskrivs hur du bearbetar dina media med Media Analytics-medieprocessorer med hjälp av Azure-portalen. Media Analytics-parlamentsledamöter producerar MP4-filer eller JSON-filer. Om en medieprocessor har skapat en MP4-fil hämtar du filen gradvis. Om en medieprocessor har skapat en JSON-fil hämtar du filen från Azure-blob-lagringen. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Välj en tillgång som du vill analysera
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I fönstret **Inställningar** väljer du **Tillgångar**.  
   
    ![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Välj den tillgång som du vill analysera och tryck på knappen **Analysera.**
   
    ![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Välj processorn i fönstret **Processmedia asset med Media Analytics.** 
   
    Resten av artikeln förklarar varför och hur man använder varje processor. 
5. Tryck på **Skapa** för att starta ett jobb.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Med medieprocessorn **För Azure Media Indexer** kan du göra mediefiler och innehåll sökbart samt skapa spår med dold textning. I det här avsnittet finns information om alternativ som du anger för den här mp-gruppen.

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Språk
Det naturliga språk som ska kännas igen i multimediafilen. Till exempel engelska eller spanska. 

### <a name="captions"></a>Undertexter
Du kan välja ett textningsformat som ska genereras från ditt innehåll. Ett indexeringsjobb kan generera filer med dold textning i följande format:  
 
* **TTML (TTML)**
* **WebVTT (olika)**

CC-filer (Closed Caption) i dessa format kan användas för att göra ljud- och videofiler tillgängliga för personer med nedsatt hörsel.

### <a name="keywords"></a>Nyckelord
Välj det här alternativet om du vill generera en XML-fil för nyckelord. Den här filen innehåller nyckelord som extraherats från talinnehållet, med frekvens- och offsetinformation.

### <a name="job-name"></a>Jobbnamn
Ett eget namn som låter dig identifiera jobbet. [I](media-services-portal-check-job-progress.md) den här artikeln beskrivs hur du kan övervaka hur ett jobb fortskrider. 

### <a name="output-file"></a>Utdatafil
Ett eget namn där du kan identifiera utdatainnehållet. 

### <a name="speed"></a>Hastighet
Ange hur snabbt indatavideon ska öka. Utdata är en stabiliserad och timelapsende återgivning av indatavideon.

### <a name="job-name"></a>Jobbnamn
Ett eget namn som låter dig identifiera jobbet. [I](media-services-portal-check-job-progress.md) den här artikeln beskrivs hur du kan övervaka hur ett jobb fortskrider. 

### <a name="output-file"></a>Utdatafil
Ett eget namn där du kan identifiera utdatainnehållet. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
**Med Azure Media Face Detector-medieprocessorn** (MP) kan du räkna, spåra rörelser och till och med mäta publikens deltagande och reaktion via ansiktsuttryck. Den här tjänsten innehåller två funktioner: 

* **Ansiktsigenkänning**
  
    Ansiktsigenkänning hittar och spårar mänskliga ansikten i en video. Flera ansikten kan identifieras och sedan spåras när de flyttas runt, med tid och plats metadata returneras i en JSON-fil. Under spårning, kommer det att försöka ge ett konsekvent ID till samma ansikte medan personen flyttar runt på skärmen, även om de är blockerade eller kort lämna ramen.
  
  > [!NOTE]
  > Dessa tjänster utför inte ansiktsigenkänning. En person som lämnar ramen eller blir blockerad för länge kommer att få ett nytt ID när de återvänder.
  > 
  > 
* **Emotion upptäckt**
  
    Emotion Detection är en valfri komponent i Face Detection Media Processor som returnerar analys på flera känslomässiga attribut från ansikten upptäckt, inklusive lycka, sorg, rädsla, ilska och mycket mer. 

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Identifieringsläge
Ett av följande lägen kan användas av processorn:

* ansiktsigenkänning
* per ansiktskänning av känslor
* aggregerad känsloiguppt

### <a name="job-name"></a>Jobbnamn
Ett eget namn som låter dig identifiera jobbet. [I](media-services-portal-check-job-progress.md) den här artikeln beskrivs hur du kan övervaka hur ett jobb fortskrider. 

### <a name="output-file"></a>Utdatafil
Ett eget namn där du kan identifiera utdatainnehållet. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
**Med Azure Media Motion Detector-medieprocessorn** (MP) kan du effektivt identifiera intressanta delar i en annars lång och händelselös video. Rörelsedetektering kan användas på statiska kamerabilder för att identifiera delar av videon där rörelse uppstår. Den genererar en JSON-fil som innehåller en metadata med tidsstämplar och det begränsningsområde där händelsen inträffade.

Riktad mot säkerhet videoflöden, denna teknik kan kategorisera rörelse i relevanta händelser och falska positiva såsom skuggor och ljusförändringar. Detta gör att du kan generera säkerhetsvarningar från kameraflöden utan att vara spammad med oändliga irrelevanta händelser, samtidigt som du kan extrahera stunder av intresse från extremt långa övervakningsvideor.

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Den här processorn kan hjälpa dig att skapa sammanfattningar av långa videor genom att automatiskt välja intressanta utdrag från källvideon. Detta är användbart när du vill ge en snabb översikt över vad som väntar i en lång video. Detaljerad information och exempel finns i [Använda Azure Media Video Thumbnails för att skapa en videouppsummering](media-services-video-summarization.md)

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Jobbnamn
Ett eget namn som låter dig identifiera jobbet. [I](media-services-portal-check-job-progress.md) den här artikeln beskrivs hur du kan övervaka hur ett jobb fortskrider. 

### <a name="output-file"></a>Utdatafil
Ett eget namn där du kan identifiera utdatainnehållet. 

## <a name="azure-media-content-moderator"></a>Moderator för Azure Media-innehåll
Den här processorn hjälper dig att upptäcka potentiellt vuxet och racyinnehåll i videor. Processorn identifierar automatiskt bilder och nyckelrutor i videon. Den betyg nyckelrutor för möjliga vuxna eller racy innehåll, och föreslår recensioner baserat på standard trösklar. Detaljerad information och exempel finns i [Använda Azure Media Content Moderator för moderera videor](media-services-content-moderation.md)

![Måttliga videor](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Använd "2.0".

### <a name="mode"></a>Läge
2.0-versionen ignorerar `Mode` inställningen.

## <a name="next-steps"></a>Nästa steg
Visa utbildningsvägar för Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
