---
title: Redigera bort personerna bakom Azure Media Analytics genomgången | Microsoft Docs
description: Det här avsnittet innehåller steg-för-steg-instruktioner på hur du kör en fullständig bortredigering arbetsflöde med hjälp av Azure Media Services Explorer (AMSE) och Azure Media Redactor Visualizer (Verktyg för öppen källkod).
services: media-services
documentationcenter: ''
author: Lichard
manager: cfowler
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: rli; juliako;
ms.openlocfilehash: 6b33da21a32373196d86ec8c93f180b3d40b1c6e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redigera bort personerna bakom Azure Media Analytics genomgång

## <a name="overview"></a>Översikt

**Azure Media Redactor** är en [Azure Media Analytics](media-services-analytics-overview.md) medieprocessor (HP) som erbjuder skalbara ansikte bortredigering i molnet. Framsidan bortredigering kan du ändra videon för att kunna oskärpa ytor valda enskilda användare. Du kanske vill använda tjänsten ansikte bortredigering i offentliga säkerhet och nyheter media scenarier. Några minuter med material som innehåller flera ytor kan ta timmar att redigera bort manuellt, men med den här tjänsten ansikte bortredigering processen tar bara några få enkla steg. Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-redactor/) blogg.

Mer information om **Azure Media Redactor**, finns det [ansikte bortredigering översikt](media-services-face-redaction.md) avsnittet.

Det här avsnittet innehåller steg-för-steg-instruktioner på hur du kör en fullständig bortredigering arbetsflöde med hjälp av Azure Media Services Explorer (AMSE) och Azure Media Redactor Visualizer (Verktyg för öppen källkod).

Mer information finns i [detta](https://azure.microsoft.com/en-us/blog/redaction-preview-available-globally) blogg.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer-arbetsflöde

Det enklaste sättet att komma igång med Redactor är att använda verktyget AMSE för öppen källkod på github. Du kan köra en förenklad arbetsflöde via **kombinerade** läge om du inte behöver åtkomst till anteckningen json- eller jpg-bilder står inför.

### <a name="download-and-setup"></a>Hämtning och installation

1. Hämta verktyget AMSE från [här](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Logga in på ditt Media Services-konto med nyckeln för tjänsten.

    Hämta kontonamn och viktig information genom att gå till [Azure Portal](https://portal.azure.com/) och välja AMS-kontot. Välj Inställningar > nycklar. I fönstret Hantera nycklar visas kontonamnet och de primära och sekundära nycklarna. Kopiera värdena för kontonamnet och den primära nyckeln.

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Först skickar – analysera läge

1. Överför mediefiler via tillgången –> överföra, eller via dra och släpp. 
1. Högerklicka och bearbeta filen media med hjälp av Media Analytics –> Azure Media Redactor –> analysera läge. 


![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Utdata innehåller en anteckningar json-fil med min platsdata, samt jpg av varje identifierad står inför. 

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Andra skicka – redigera bort läge

1. Ladda upp din ursprungliga video tillgång till utdata från det första steget och ange som en primär tillgång. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Valfritt) Överför en 'Dance_idlist.txt'-fil som innehåller en ny rad avgränsad lista över ID: N som du vill redigera bort. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Valfritt) Gör alla ändringar i annotations.json-filen, till exempel öka omgivande rutan gränserna. 
4. Högerklicka på utdatatillgången från det första steget, Välj Redactor och kör med den **Redact** läge. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Hämta eller dela den omarbetade slutversionen tillgången. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Visualizer öppen källkod verktyget

En öppen källkod [visualizer verktyget](https://github.com/Microsoft/azure-media-redactor-visualizer) är utformat för att hjälpa utvecklare som precis har startats med formatet anteckningar med hjälp av utdata och parsning.

När du klona lagringsplatsen, för att kunna köra projektet du behöver hämta FFMPEG från deras [officiella platsen](https://ffmpeg.org/download.html).

Om du utvecklar skulle parsas anteckningen JSON-data, kan du titta på Models.MetaData exempel kodexempel.

### <a name="set-up-the-tool"></a>Konfigurera verktyget

1.  Hämta och skapa hela lösningen. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Hämta FFMPEG från [här](https://ffmpeg.org/download.html). Det här projektet ursprungligen utvecklades med version be1d324 (2016-10-04) med statisk länkning. 
3.  Kopiera ffmpeg.exe och ffprobe.exe till samma utdatamapp som AzureMediaRedactor.exe. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Run AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Använd verktyget

1. Bearbeta videon i Azure Media Services-kontot med Redactor MP på Analysera läge. 
2. Hämta både den ursprungliga videofilen och utdata från bortredigering - analysera jobb. 
3. Kör programmet visualizer och välj filerna som ovan. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Förhandsgranska din fil. Välj vilka står du skulle vilja oskärpa via sidopanelen till höger. 
    
    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Fältet längst ned uppdateras med de står inför ID: N. Skapa en fil med namnet ”idlist.txt” med dessa ID: N som en ny rad avgränsad lista. 

    >[!NOTE]
    > Idlist.txt ska sparas i ANSI. Du kan använda anteckningar för att spara i ANSI.
    
6.  Överför den här filen till utdatatillgången från steg 1. Ladda upp den ursprungliga videon samt tillgången och ange som primär tillgång. 
7.  Kör jobb för bortredigering på tillgången med ”Redact” läge borttagen för att få sista video. 

## <a name="next-steps"></a>Nästa steg 

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Media Analytics demonstrationer](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Om ansikte bortredigering för Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
