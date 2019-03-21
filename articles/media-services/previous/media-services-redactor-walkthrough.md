---
title: Redigera ansikten med Azure Media Analytics genomgången | Microsoft Docs
description: Det här avsnittet innehåller stegvisa instruktioner om hur du kör en fullständig ansiktsredigering arbetsflöde med hjälp av Azure Media Services Explorer (AMSE) och Azure Media Redactor Visualizer (verktyg med öppen källkod).
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: rli; juliako;
ms.openlocfilehash: 3e4844c3174e41ca7f6f5667a2777aba11f70f11
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58292996"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redigera ansikten med Azure Media Analytics genomgången

## <a name="overview"></a>Översikt

**Azure Media Redactor** är en [Azure Media Analytics](media-services-analytics-overview.md) mediebearbetare (MP) som erbjuder skalbara ansiktsredigering i molnet. Ansiktsredigering kan du ändra din video för att kunna oskärpa ansikten för valda individer. Du kanske vill använda tjänsten ansiktsredigering i offentliga säkerhet samt nyhetsmediescenarier. Några minuter med material som innehåller flera ansikten kan ta timmar att redigera manuellt, men med den här tjänsten ansikte ansiktsredigering processen tar bara några få enkla steg. Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-redactor/) blogg.

Mer information om **Azure Media Redactor**, finns i den [ansikte ansiktsredigering översikt](media-services-face-redaction.md) avsnittet.

Det här avsnittet innehåller stegvisa instruktioner om hur du kör en fullständig ansiktsredigering arbetsflöde med hjälp av Azure Media Services Explorer (AMSE) och Azure Media Redactor Visualizer (verktyg med öppen källkod).

Mer information finns i [detta](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogg.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer-arbetsflöde

Det enklaste sättet att komma igång med Redactor är att använda verktyget AMSE för öppen källkod på GitHub. Du kan köra en förenklad arbetsflöde via **kombineras** läge om du inte behöver åtkomst till anteckning-json- eller jpg-bilder ansikte.

### <a name="download-and-setup"></a>Hämtning och installation

1. Ladda ned verktyget AMSE från [här](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Logga in på ditt Media Services-konto med hjälp av din nyckel för tjänstens.

    Hämta kontonamn och viktig information genom att gå till [Azure Portal](https://portal.azure.com/) och välja AMS-kontot. Välj Inställningar > nycklar. I fönstret Hantera nycklar visas kontonamnet och de primära och sekundära nycklarna. Kopiera värdena för kontonamnet och den primära nyckeln.

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Först skickar – analysera läge

1. Ladda upp din mediefil till tillgången –> överföra, eller via dra och släpp. 
1. Högerklicka på och bearbeta dina media-fil med hjälp av Media Analytics –> Azure Media Redactor –> analysera läge. 


![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Utdata innehåller en json-fil för anteckningar med ansikts-platsdata, samt en jpg för varje identifierad ansikte. 

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Skicka andra – redigera läge

1. Ladda upp din ursprungliga video tillgång till utdata från det första steget och ange som en primär plats. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Valfritt) Överför en 'Dance_idlist.txt'-fil som innehåller en ny rad avgränsad lista med ID: N som du vill redigera. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Valfritt) Se alla ändringar i filen annotations.json, till exempel öka de omgivande box-gränserna. 
4. Högerklicka på utdatatillgången från det första steget, Välj Redactor och kör med den **Redact** läge. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Ladda ned eller dela den redigerade slutversionen tillgången. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Visualizer verktyg med öppen källkod

En öppen källkod [visualizer verktyget](https://github.com/Microsoft/azure-media-redactor-visualizer) har utformats för att hjälpa utvecklare som precis har startat med formatet anteckningar med hjälp av utdata och parsning.

När du klonar lagringsplatsen, för att kunna köra projektet du måste hämta FFMPEG från sina [officiella plats](https://ffmpeg.org/download.html).

Om du är utvecklare försök att parsa JSON-data som anteckningen kan du titta på Models.MetaData exempel kodexempel.

### <a name="set-up-the-tool"></a>Konfigurera verktyget

1.  Hämta och skapa hela lösningen. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Hämta FFMPEG från [här](https://ffmpeg.org/download.html). Det här projektet ursprungligen utvecklades be1d324 versionen (2016-10-04) med statisk länkning. 
3.  Kopiera ffmpeg.exe och ffprobe.exe till samma Utdatamappen som AzureMediaRedactor.exe. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Kör AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Använd verktyget

1. Bearbeta din video i ditt Azure Media Services-konto med Redactor MP analysera läge. 
2. Ladda ned både originalfilen video- och utdata från Ansiktsredigering – analysera jobbet. 
3. Kör programmet visualizer och välja filerna som ovan. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Förhandsgranska din fil. Välj vilka ansikten som du skulle vilja oskärpa via sidopanelen till höger. 
    
    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Nedre textfältet uppdateras med ansikts-ID: N. Skapa en fil med namnet ”idlist.txt” med dessa ID: N som en ny rad avgränsade lista. 

    >[!NOTE]
    > Idlist.txt ska sparas i ANSI. Du kan använda anteckningar för att spara i ANSI.
    
6.  Ladda upp den här filen för utdatatillgången från steg 1. Ladda upp den ursprungliga videon till den här tillgången samt och ange som primär plats. 
7.  Kör jobb för redigering på den här tillgången med ”Redact” läge borttagen för att få sista video. 

## <a name="next-steps"></a>Nästa steg 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Medieanalys-demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Vi presenterar Ansiktsredigering för Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
