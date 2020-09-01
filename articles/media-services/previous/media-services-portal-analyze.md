---
title: Analysera dina media med Azure Portal | Microsoft Docs
description: I det här avsnittet beskrivs hur du bearbetar media med Medieanalys Media processor (MPs) med hjälp av Azure Portal.
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
ms.openlocfilehash: 0bc8233110c02775e208470591c3e0c6eb619294
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260993"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analysera dina media med hjälp av Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Granska [indragnings planer](media-services-analytics-overview.md#retirement-plans) för vissa medie processorer.

## <a name="overview"></a>Översikt
Azure Media Services Analytics är en samling tal-och Visions komponenter (i företags skala, efterlevnad, säkerhet och global räckvidd) som gör det enklare för organisationer och företag att härleda användbara insikter från sina videofiler. Mer detaljerad översikt över Azure Media Services Analytics finns i [det här](media-services-analytics-overview.md) avsnittet. 

I det här avsnittet beskrivs hur du bearbetar media med Medieanalys Media processor (MPs) med hjälp av Azure Portal. Medieanalys MPs producerar MP4-filer eller JSON-filer. Om en medie processor har producerat en MP4-fil kan du hämta filen progressivt. Om en medie processor har producerat en JSON-fil, laddar du ned filen från Azure Blob Storage. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Välj en till gång som du vill analysera
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I fönstret **Inställningar** väljer du **Tillgångar**.  
   
    ![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Välj den till gång som du vill analysera och tryck på knappen **analysera** .
   
    ![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Välj processorn i fönstret **bearbeta medie till gång med medieanalys** . 
   
    I resten av artikeln förklaras varför och hur du använder varje processor. 
5. Tryck på **skapa** för att starta ett jobb.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Med **Azure Media Indexer** medie processor kan du göra mediefiler och innehåll sökbara, samt generera textning för dold textning. Det här avsnittet innehåller information om alternativ som du anger för det här hanterings paketet.

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Språk
Det naturliga språket som ska identifieras i multimedie filen. Till exempel engelska eller spanska. 

### <a name="captions"></a>Undertexter
Du kan välja ett text format som ska genereras från ditt innehåll. Ett indexerings jobb kan skapa filer med dold textning i följande format:  
 
* **TTML**
* **WebVTT**

Filer med dold textning (CC) i dessa format kan användas för att göra ljud-och videofiler tillgängliga för personer med hörsel funktions hinder.

### <a name="keywords"></a>Nyckelord
Välj det här alternativet om du vill skapa en nyckelords-XML-fil. Den här filen innehåller nyckelord som har extraherats från tal innehållet, med frekvens och förskjutnings information.

### <a name="job-name"></a>Jobbnamn
Ett eget namn som gör att du kan identifiera jobbet. I [den här](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som gör att du kan identifiera utdata för innehållet. 

### <a name="speed"></a>Hastighet
Ange hastigheten som Indataporten ska påskyndas med. Utdatan är en stabiliserad och tids fördröjning av indata-videon.

### <a name="job-name"></a>Jobbnamn
Ett eget namn som gör att du kan identifiera jobbet. I [den här](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som gör att du kan identifiera utdata för innehållet. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Med MP ( **Azure Media Face Detector** Media processor) kan du räkna, spåra förflyttningar och till och med mäta åhörarnas medverkan och reaktion via ansikts uttryck. Den här tjänsten innehåller två funktioner: 

* **Ansiktsspårning**
  
    Ansikts igenkänning hittar och spårar mänskliga ansikten i en video. Du kan identifiera flera ansikten och sedan spåras de när de flyttas runt, med tids-och platsens metadata som returneras i en JSON-fil. Under spårningen görs ett försök att ge ett konsekvent ID till samma ansikte medan personen rör sig på skärmen, även om de är blockerade eller om de ska lämna ramen.
  
  > [!NOTE]
  > Den här tjänsten utför inte ansikts igenkänning. En person som lämnar ramen eller som kan förhindras för länge får ett nytt ID när de returneras.
  > 
  > 
* **Känslo-identifiering**
  
    Känslo-identifiering är en valfri komponent i Ansiktsigenkänning medie processorn som returnerar analyser på flera känslomässig-attribut från de ansikten som identifierats, inklusive glädje, ledsenhet, frukt, ilska och mycket annat. 

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Identifierings läge
Ett av följande lägen kan användas av processorn:

* ansikts igenkänning
* identifiering av känslo per ansikts
* sammanställd identifiering av Känslo

### <a name="job-name"></a>Jobbnamn
Ett eget namn som gör att du kan identifiera jobbet. I [den här](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som gör att du kan identifiera utdata för innehållet. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Med hjälp av MP ( **Azure Media motion Detector** Media processor) kan du på ett effektivt sätt identifiera intresse områden inom en i övrigt lång och en video som inte är i händelse av händelse. Rörelse identifiering kan användas på statiska kamera tagningar för att identifiera delar av videon där rörelsen sker. Den genererar en JSON-fil som innehåller en metadata med tidsstämplar och den bindnings region där händelsen inträffade.

Den här tekniken är riktad mot säkerhets video flöden och kan kategorisera rörelsen i relevanta händelser och falska positiva identifieringar, till exempel skuggor och belysnings ändringar. På så sätt kan du generera säkerhets aviseringar från kamera flöden utan att skicka skräp post med oändliga irrelevanta händelser, samtidigt som du kan extrahera mycket intresse från extremt långa övervaknings videor.

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Den här processorn kan hjälpa dig att skapa sammanfattningar av långa videor genom att automatiskt välja intressanta kodfragment från käll videon. Detta är användbart när du vill ge en snabb översikt över vad som förväntas i en lång video. Detaljerad information och exempel finns i [använda Azure Media Video thumbnails för att skapa en video Sammanfattning](media-services-video-summarization.md)

![Analysera videor](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Jobbnamn
Ett eget namn som gör att du kan identifiera jobbet. I [den här](media-services-portal-check-job-progress.md) artikeln beskrivs hur du kan övervaka förloppet för ett jobb. 

### <a name="output-file"></a>Utdatafil
Ett eget namn som gör att du kan identifiera utdata för innehållet. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Den här processorn hjälper dig att identifiera potentiellt vuxna och vågat innehåll i videor. Processorn identifierar automatiskt bilder och nyckel rutor i videon. Den ger nyckel rutorna för möjlig vuxen eller vågat innehåll, och föreslår recensioner baserat på standard tröskelvärden. Detaljerad information och exempel finns i [använda Azure Media Content moderator för att få en medelhög video](media-services-content-moderation.md)

![Måttliga videor](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Använd "2,0".

### <a name="mode"></a>Läge
I 2,0-versionen ignoreras `Mode` inställningen.

## <a name="next-steps"></a>Nästa steg
Visa Media Services utbildnings vägar.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
