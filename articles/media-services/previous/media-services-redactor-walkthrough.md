---
title: Redigera ansikten med Azure Media Analytics genomgång | Microsoft-dokument
description: Det här avsnittet visar steg för steg instruktioner om hur du kör ett fullständigt arbetsflöde för bortredigeringsåtgärder med Azure Media Services Explorer (AMSE) och Azure Media Redactor Visualizer (open source-verktyg).
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
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: a8db8de6ef062dcf757f3d264379677d6550ea3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69997678"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redigera ansikten med Azure Media Analytics-genomgång

## <a name="overview"></a>Översikt

**Azure Media Redactor** är en Azure Media Analytics-medieprocessor (MP) som erbjuder skalbar ansiktsredigering i molnet. [Azure Media Analytics](media-services-analytics-overview.md) Med ansiktsutredigering kan du ändra videon för att göra vissa personer oskarpa. Du kanske vill använda ansiktsutredigeringstjänsten i scenarier för allmän säkerhet och nyhetsmedier. Några minuter av bilder som innehåller flera ansikten kan ta timmar att redigera manuellt, men med den här tjänsten ansikte bortredigeringsverktyg processen kommer att kräva bara några enkla steg. Mer information finns i [den här](https://azure.microsoft.com/blog/azure-media-redactor/) bloggen.

Mer information om **Azure Media Redactor**finns i översiktsavsnittet [Ansiktsigenkning.](media-services-face-redaction.md)

Det här avsnittet visar steg för steg instruktioner om hur du kör ett fullständigt arbetsflöde för bortredigeringsåtgärder med Azure Media Services Explorer (AMSE) och Azure Media Redactor Visualizer (open source-verktyg).

Mer information finns i [den här](https://azure.microsoft.com/blog/redaction-preview-available-globally) bloggen.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer-arbetsflöde

Det enklaste sättet att komma igång med Redactor är att använda AMSE-verktyget med öppen källkod på GitHub. Du kan köra ett förenklat arbetsflöde via **kombinerat** läge om du inte behöver tillgång till anteckningsjison eller ansikts jpg-bilder.

### <a name="download-and-setup"></a>Ladda ned och konfigurera

1. Ladda ner AMSE för AMS v2 verktyg [härifrån](https://aka.ms/amseforv2).
1. Logga in på ditt Media Services-konto med hjälp av din tjänstnyckel.

    Hämta kontonamn och viktig information genom att gå till [Azure Portal](https://portal.azure.com/) och välja AMS-kontot. Välj sedan Inställningar > tangenter. I fönstret Hantera nycklar visas kontonamnet och de primära och sekundära nycklarna. Kopiera värdena för kontonamnet och den primära nyckeln.

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Första passet – analysera läge

1. Ladda upp mediefilen via Asset – > Ladda upp eller via dra och släpp. 
1. Högerklicka och bearbeta mediefilen med Media Analytics – > Azure Media Redactor – > analysera läget. 


![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Utdata kommer att innehålla en anteckning json fil med ansikte platsdata, samt en jpg av varje upptäckt ansikte. 

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Andra passet – redact-läge

1. Ladda upp den ursprungliga videotillgången till utdata från det första passet och ange som en primär tillgång. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Valfritt) Ladda upp en "Dance_idlist.txt"-fil som innehåller en nyrad avgränsad lista över de ID:er du vill redigera. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Valfritt) Gör ändringar i filen annoteations.json, till exempel att öka begränsningsramgränserna. 
4. Högerklicka på utdatatillgången från det första passet, välj Redactor och kör med **redact-läget.** 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Ladda ned eller dela den slutliga redacted utdatatillgången. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Visualizer open source-verktyg

Ett [visualiserarverktyg](https://github.com/Microsoft/azure-media-redactor-visualizer) med öppen källkod är utformat för att hjälpa utvecklare som precis har börjat med anteckningsformatet med tolkning och användning av utdata.

När du klona repo, för att köra projektet, måste du ladda ner FFMPEG från deras [officiella webbplats](https://ffmpeg.org/download.html).

Om du är en utvecklare som försöker tolka JSON-anteckningsdata kan du titta i Models.MetaData efter exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på exempel på kod.

### <a name="set-up-the-tool"></a>Konfigurera verktyget

1.  Ladda ner och bygg hela lösningen. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Ladda ner FFMPEG [härifrån](https://ffmpeg.org/download.html). Detta projekt utvecklades ursprungligen med version be1d324 (2016-10-04) med statisk länkning. 
3.  Kopiera ffmpeg.exe och ffprobe.exe till samma utdatamapp som AzureMediaRedactor.exe. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Kör AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Använda verktyget

1. Bearbeta din video i ditt Azure Media Services-konto med Redactor MP i analysera läge. 
2. Ladda ner både den ursprungliga videofilen och utdata från redaction - Analysera jobbet. 
3. Kör visualizer-programmet och välj filerna ovan. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Förhandsgranska filen. Välj vilka ansikten du vill sudda ut via sidofältet till höger. 
    
    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Det nedre textfältet uppdateras med ansikts-ID: n. Skapa en fil som heter "idlist.txt" med dessa ID:er som en avgränsad lista med en ny rad. 

    >[!NOTE]
    > Idlist.txt ska sparas i ANSI. Du kan använda anteckningar för att spara i ANSI.
    
6.  Ladda upp den här filen till utdatatillgången från steg 1. Ladda upp den ursprungliga videon till den här tillgången och ange som primär tillgång. 
7.  Kör Redaction jobb på denna tillgång med "Redact" läge för att få den slutliga redigerade video. 

## <a name="next-steps"></a>Nästa steg 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

[Demos för Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Tillkännage ansiktsredigering för Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
