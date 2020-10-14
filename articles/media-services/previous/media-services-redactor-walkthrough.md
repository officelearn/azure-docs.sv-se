---
title: Bortredigering-ansikten med Azure-medieanalys genom gång | Microsoft Docs
description: Det här avsnittet innehåller stegvisa instruktioner om hur du kör ett fullständigt bortredigering-arbetsflöde med Azure Media Services Explorer (AMSE) och Azure Media Redactor visualiserare (verktyget med öppen källkod).
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
ms.openlocfilehash: 5fdec829ceeefce2426a5fd08b4245e66bd0a08c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016679"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Bortredigering-ansikten med Azure-medieanalys genom gång

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt

**Azure Media Redactor** är en [Azure-medieanalys](./legacy-components.md) medie processor (MP) som erbjuder skalbara ansikts bortredigering i molnet. Med ansikts bortredigering kan du ändra videon så att det blir oskarpa ytor på valda individer. Du kanske vill använda ansikts bortredigering i offentliga säkerhets-och nyhets medie scenarier. Några minuter av tagningar som innehåller flera ansikten kan ta timmar till bortredigering manuellt, men med den här tjänsten krävs bara några få enkla steg. Mer information finns i [den här](https://azure.microsoft.com/blog/azure-media-redactor/) bloggen.

Mer information om  **Azure Media Redactor**finns i avsnittet [Översikt över ansikts bortredigering](media-services-face-redaction.md) .

Det här avsnittet innehåller stegvisa instruktioner om hur du kör ett fullständigt bortredigering-arbetsflöde med Azure Media Services Explorer (AMSE) och Azure Media Redactor visualiserare (verktyget med öppen källkod).

Mer information finns i [den här](https://azure.microsoft.com/blog/redaction-preview-available-globally) bloggen.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer-arbetsflöde

Det enklaste sättet att komma igång med bortredigering är att använda AMSE-verktyget med öppen källkod på GitHub. Du kan köra ett förenklat arbets flöde via **kombinerat** läge om du inte behöver åtkomst till antecknings-JSON eller ansikts jpg-bilderna.

### <a name="download-and-setup"></a>Hämta och konfigurera

1. Hämta AMSE för AMS v2-verktyget härifrån [.](https://aka.ms/amseforv2)
1. Logga in på ditt Media Services-konto med hjälp av tjänst nyckeln.

    Hämta kontonamn och viktig information genom att gå till [Azure Portal](https://portal.azure.com/) och välja AMS-kontot. Välj sedan inställningar > nycklar. I fönstret Hantera nycklar visas kontonamnet och de primära och sekundära nycklarna. Kopiera värdena för kontonamnet och den primära nyckeln.

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Första pass-analys läge

1. Ladda upp medie filen via till gång – > Ladda upp eller dra och släpp. 
1. Högerklicka på och bearbeta medie filen med Medieanalys – > Azure Media Redactor – > analys läge. 


![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Utdata kommer att innehålla en antecknings-JSON-fil med ansikts plats data, samt en jpg för varje identifierad ansikte. 

![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Andra steget – bortredigering-läge

1. Ladda upp din ursprungliga video till gång till utdata från det första steget och ange som en primär till gång. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Valfritt Ladda upp en Dance_idlist.txt-fil som innehåller en blankstegsavgränsad lista med de ID: n som du vill redigera. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Valfritt Gör eventuella ändringar i annotations.jspå filen, till exempel att öka gränserna för avgränsnings rutan. 
4. Högerklicka på utmatnings till gången från det första steget, Välj bortredigering och kör med **bortredigering** -läget. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Hämta eller dela den slutliga avvisade utmatnings till gången. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Verktyget för öppen källkod för Azure Media Redactor-visualiserare

Ett [verktyg](https://github.com/Microsoft/azure-media-redactor-visualizer) med öppen källkod är utformat för att hjälpa utvecklare som bara börjar med antecknings formatet med parsning och användning av utdata.

När du har klonat lagrings platsen måste du ladda ned FFMPEG från sin [officiella plats](https://ffmpeg.org/download.html)för att kunna köra projektet.

Om du är utvecklare som försöker parsa JSON-antecknings data tittar du i modeller. MetaData för exempel kod exempel.

### <a name="set-up-the-tool"></a>Konfigurera verktyget

1.  Hämta och bygg hela lösningen. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Hämta FFMPEG härifrån [.](https://ffmpeg.org/download.html) Projektet utvecklades ursprungligen med version be1d324 (2016-10-04) med statisk länkning. 
3.  Kopiera ffmpeg.exe och ffprobe.exe till samma målmapp som AzureMediaRedactor.exe. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Kör AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Använda verktyget

1. Bearbeta videon i ditt Azure Media Services-konto med bortredigering MP i analys läge. 
2. Ladda ned både den ursprungliga video filen och utdata från jobbet för att analysera bortredigering. 
3. Kör visualisera program och välj filerna ovan. 

    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Förhandsgranska filen. Välj vilka ansikten du vill göra oskärpa via sid panelen till höger. 
    
    ![Ansiktsredigering](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Det nedre textfältet uppdateras med ansikts-ID: n. Skapa en fil med namnet "idlist.txt" med dessa ID: n som en avgränsad lista för rad matningar. 

    >[!NOTE]
    > idlist.txt ska sparas i ANSI. Du kan använda anteckningar för att spara i ANSI.
    
6.  Överför filen till den utgående till gången från steg 1. Ladda upp den ursprungliga videon till den här till gången och ange som primär till gång. 
7.  Kör bortredigering-jobbet på den här till gången med läget "bortredigering" för att hämta den slutliga bortredigering-videon. 

## <a name="next-steps"></a>Nästa steg 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Översikt över Azure Media Services Analytics](./legacy-components.md)

[Azure-medieanalys demonstrationer](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Vi presenterar ansikts bortredigering för Azure-medieanalys](https://azure.microsoft.com/blog/azure-media-redactor/)