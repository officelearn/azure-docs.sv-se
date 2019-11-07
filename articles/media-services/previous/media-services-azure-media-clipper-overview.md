---
title: Skapa klipp med Azure Media Clipper | Microsoft Docs
description: Översikt över Azure Media Clipper, ett verktyg för att skapa medie klipp från till gångar
services: media-services
keywords: klipp; subclip; encoding; Media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685010"
---
# <a name="create-clips-with-azure-media-clipper"></a>Skapa klipp med Azure Media Clipper 

Azure Media Clipper är ett kostnads fritt JavaScript-bibliotek som gör det möjligt för webbutvecklare att tillhandahålla sina användare ett gränssnitt för att skapa medie klipp. Det här verktyget kan integreras på alla webb sidor och innehåller API: er för att läsa in till gångar och skicka urklipps jobb.

Med Azure Media Clipper kan du:
- Trimma i förväg och efter Skriv åtgärder från Live-Arkiv 
- Skapa video höjd punkter från AMS Live-händelser, Live-Arkiv eller fMP4 VOD-filer 
- Sammanfoga videor från flera källor 
- Skapa sammanfattnings klipp från dina AMS media-tillgångar 
- Klipp videor med ram precision 
- Generera dynamiska manifest filter över befintliga Live-och VOD-tillgångar med GOP-precision (Group-of-Pictures) 
- Skapa kodnings jobb mot till gångarna i ditt Media Services-konto

För att begära nya funktioner, ge idéer eller feedback, skicka till [UserVoice för Azure Media Services](https://aka.ms/amsvoice/). Om du har och specifika problem, frågor eller hittar några buggar, tar du bort Media Services-teamet på amcinfo@microsoft.com.

Följande bild illustrerar Clipper-gränssnittet: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Viktig information
Se följande lista för blogg inlägget Clipper, olika kända problem och ändringsloggen för den senaste versionen av Clipper:
- [Blogg inlägg](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Lista med kända problem](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Ändringsloggen](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Webb läsar stöd
Azure Media Clipper har skapats med modern HTML5-teknik och stöder följande webbläsare:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Det finns för närvarande inte stöd för HTML5-uppspelning av strömmar från Azure Media Services.

## <a name="language-support"></a>Stöd för språk
Widgeten Clipper är tillgänglig på följande 18 språk:
- Kinesiska (förenklad)
- Kinesiska (traditionell)
- Tjeckiska
- Holländska, flamländska
- Svenska
- Franska
- Tyska
- Ungerska
- Italienska
- Japanska
- Koreanska
- Polska
- Portugisiska (Brasilien)
- Portugisiska (Portugal)
- Ryska
- Spanska
- Svenska
- Turkiska

## <a name="next-steps"></a>Nästa steg
Om du vill komma igång med Azure Media Clipper läser du artikeln [komma igång](media-services-azure-media-clipper-getting-started.md) för att få mer information om hur du distribuerar widgeten.
