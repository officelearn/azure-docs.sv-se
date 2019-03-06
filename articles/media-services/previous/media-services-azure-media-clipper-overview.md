---
title: Skapa klipp med Azure Media Clipper | Microsoft Docs
description: Översikt över Azure Media Clipper, ett verktyg för att skapa media klipp från tillgångar
services: media-services
keywords: Clip, underklipp, kodning, media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 578818c6c140253bfb6a30530cee6a915eb385c3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450438"
---
# <a name="create-clips-with-azure-media-clipper"></a>Skapa klipp med Azure Media Clipper 

Azure Media Clipper är ett kostnadsfritt JavaScript-bibliotek som gör det möjligt för webbutvecklare att ge användarna ett gränssnitt för att skapa media klipp. Det här verktyget kan integreras i en webbsida och tillhandahåller API: er för inläsning av tillgångar och skicka urklippningsjobb.

Azure Media Clipper kan du:
- Trimma före bakgrundsbild och efter bakgrundsbild från live-Arkiv 
- Compose video höjdpunkter från AMS direktsändningar, direktsända strömningar eller fMP4 VOD-filer 
- Sammanfoga videor från flera källor 
- Skapa sammanfattning klipp från AMS medietillgångar 
- Clip videor med ramens noggrannhet 
- Generera dynamiska manifestfilter över befintliga live- och VOD-tillgångar med gruppera bilder (GOP) noggrannhet 
- Skapa kodningsjobb mot tillgångar i media services-konto

För att efterfråga nya funktioner, tillhandahåller förslag eller kommentarer, skicka till [UserVoice för Azure Media Services](https://aka.ms/amsvoice/). Om du har och specifika problem, frågor eller hitta eventuella buggar, släpp mediet Services team en linje vid amcinfo@microsoft.com.

Följande bild illustrerar Clipper-gränssnittet: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>Viktig information
Se listan nedan för blogginlägget Clipper, olika kända problem och Ändringslogg för den senaste versionen av Clipper:
- [Blogginlägg](https://azure.microsoft.com/blog/azure-media-clipper/)
- [Lista över kända problem](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Ändringslogg](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Webbläsarstöd
Azure Media Clipper byggs med moderna HTML5-tekniker och har stöd för följande webbläsare:

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54 +
- Safari 10+
- Firefox 50+

> [!NOTE]
> Endast HTML5 uppspelning av dataströmmar från Azure Media Services stöds för närvarande.

## <a name="language-support"></a>Stöd för språk
Widgeten Clipper är tillgänglig på följande 18 språk:
- Förenklad kinesiska
- Traditionell kinesiska
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
Kom igång med Azure Media Clipper, läsa den [komma igång](media-services-azure-media-clipper-getting-started.md) nedan för information om hur du distribuerar widgeten.
