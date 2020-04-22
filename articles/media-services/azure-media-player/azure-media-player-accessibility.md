---
title: Hjälpmedel för Azure Media Player
description: Läs mer om tillgänglighetsinställningarna för Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726559"
---
# <a name="accessibility"></a>Hjälpmedel #

Azure Media Player fungerar med skärmläsare funktioner som Windows Skärmläsaren och Apple OSX / iOS VoiceOver. Alternativa taggar är tillgängliga för gränssnittsknapparna och skärmläsaren kan läsa dessa alternativa taggar när användaren navigerar till dem. Ytterligare konfigurationer kan ställas in på operativsystemsnivå.

## <a name="captions-and-subtitles"></a>Bildtexter och undertexter ##

För närvarande stöder Azure Media Player bildtexter för endast on-demand-händelser med WebVTT-format och live-händelser med CEA 708. TTML-format stöds för närvarande inte. Med undertextning kan spelaren tillgodose och hjälpa en bredare publik, till exempel personer med nedsatt hörsel eller användare som vill kunna läsa med på ett annat språk. Undertexter även öka engagemanget video, förbättra förståelsen och göra det möjligt att visa videon i ljudkänsliga miljöer, t.ex. på en arbetsplats.  

## <a name="high-contrast-mode"></a>Högkontrastläge ##

Standardgränssnittet i Azure Media Player är kompatibelt med de flesta lägen för hög kontrastlägen för enheter/webbläsare. Konfigurationer kan ställas in på operativsystemsnivå.

## <a name="mobility-options"></a>Alternativ för rörlighet ##

### <a name="tabbing-focus"></a>Tabbing fokus ###

Tabbing fokus, som tillhandahålls av allmänna HTML-standarder, är tillgänglig i Azure Media Player. För att aktivera flikfokusering måste du lägga till `tabindex=0` (eller ett annat värde `<video>` om du `<video ... tabindex=0>...</video>`förstår hur tabbordning påverkas i HTML) i HTML så här: . På vissa plattformar kan fokus för kontrollerna endast finnas om kontrollerna är synliga och om plattformen stöder dessa funktioner.

När tabbing fokus är aktiverat, slutanvändaren kan effektivt navigera och styra videospelaren utan att vara beroende av deras mus. Varje snabbmeny eller kontrollerbart element kan navigeras till genom att trycka på flikknappen och markeras med enter eller mellanslag. Genom att trycka enter eller mellanslag på en snabbmeny utökas den så att slutanvändaren kan fortsätta tabbning genom att välja ett menyalternativ. När du befinner dig i det sammanhang där det önskade objektet finns trycker du på retur eller blanksteg för att välja det.

### <a name="hotkeys"></a>Snabbtangenter ###

Azure Media Player stöder styrning via tangentbordstangentbord. I en webbläsare är det enda sättet att kontrollera det underliggande videoelementet genom att fokusera på spelaren. När det finns fokus på spelaren kan het tangenter styra spelarens funktionalitet.  I tabellen nedan beskrivs olika snabbtangenter och deras associerade beteende:

| Snabbtangent              | Beteende                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Player startar/avslutar helskärmsläge                                  |
| M/m                  | Slå på/av ljud för spelaren                                          |
| Upp- och nedpil.    | Spelarens volym kommer öka/minska                                    |
| Vänster och höger pil | Videoförloppet ökar/minskar                                  |
| 0,1,2,3,4,5,6,7,8,9  | Videoframsteg ändras till\- 0% 90% beroende på vilken tangent som trycks |
| Klicka på Åtgärd         | Videon spelas upp/pausar                                                   |

## <a name="next-steps"></a>Nästa steg

<!---Some context for the following links goes here--->
- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)