---
title: Skapa klipp med Azure Media Clipper | Microsoft Docs
description: "Översikt över Azure Media Clipper, ett verktyg för att skapa media klipp från tillgångar"
services: media-services
keywords: Clip; underklipp; kodning; media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a5bb66db889dc1c52252773e3f129ba2c3d55f1
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Skapa klipp med Azure Media Clipper
Azure Media Clipper är ett gratis JavaScript-bibliotek som gör att webbutvecklare att ge användarna ett gränssnitt för att skapa media klipp. Det här verktyget kan integreras i en webbsida och innehåller API: er för inläsning av tillgångar och skickar urklippet jobben.

Azure Media Clipper kan du:
- Ta bort den äldre mallen och efter en från live Arkiv 
- Skapa video visar från AMS direktsända händelser, levande Arkiv eller fMP4 VOD filer 
- Sammanfoga videor från flera källor 
- Skapa sammanfattning klipp från ditt media AMS-tillgångar 
- Beskär videor med ram noggrannhet 
- Generera dynamiska manifestet filter över befintliga live och VOD tillgångar med noggrannhet gruppera bilder (GOP) 
- Skapa kodning jobb mot tillgångar i media services-konto

Ange idéer eller feedback om du vill begära nya funktioner, skicka den till [UserVoice för Azure Media Services](http://aka.ms/amsvoice/). Om du har och specifika problem, frågor eller Sök efter eventuella buggar släpp mediet Services-teamet en linje vid amcinfo@microsoft.com.

## <a name="release-notes"></a>Viktig information
I följande lista beskrivs olika kända problem och changelog för den senaste versionen av Clipper:
- [Lista över kända problem](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Changelog](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Stöd för webbläsare
Azure Media Clipper skapas med moderna HTML5-tekniker och stöder följande webbläsare:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Endast HTML5 uppspelning av dataströmmar från Azure Media Services stöds för närvarande.

## <a name="language-support"></a>Språkstöd
Widgeten Clipper finns i följande 18 språk:
- Kinesiska (förenklad)
- Kinesiska (traditionell)
- Tjeckiska
- Nederländska Flamländska
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
Om du vill komma igång med Azure Media Clipper, läsa den [komma igång](media-services-azure-media-clipper-getting-started.md) artikeln för information om hur du distribuerar widgeten.