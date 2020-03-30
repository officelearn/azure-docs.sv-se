---
title: Översikt över Microsoft Threat Modeling Tool - Azure
description: Översikt över Microsoft Threat Modeling Tool, som innehåller information om hur du kommer igång med verktyget, inklusive hotmodelleringsprocessen.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 02/16/2017
ms.openlocfilehash: 0d800102b6f6ff77944a2b625d3bcecef69c1ee9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75548744"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsofts verktyg för hotmodellering

Hotmodelleringsverktyget är en central del av Microsoft Security Development Lifecycle (SDL). Det gör det möjligt för programvaruarkitekter att identifiera och minska potentiella säkerhetsproblem tidigt, när de är relativt enkla och kostnadseffektiva att lösa. Som ett resultat minskar det kraftigt den totala kostnaden för utveckling. Dessutom har vi utformat verktyget med icke-säkerhetsexperter i åtanke, vilket gör hotmodellering enklare för alla utvecklare genom att ge tydlig vägledning om hur du skapar och analyserar hotmodeller. 

Verktyget gör det möjligt för vem som helst att:

* Kommunicera om säkerhetsdesignen av sina system
* Analysera dessa konstruktioner för potentiella säkerhetsproblem med hjälp av en beprövad metod
* Föreslå och hantera mildrande åtgärder för säkerhetsproblem

Här är några verktygsfunktioner och innovationer, bara för att nämna några:

* **Automatisering:** Vägledning och feedback när du ritar en modell
* **STRIDE per element:** Guidad analys av hot och mildrande åtgärder
* **Rapportering:** Säkerhetsaktiviteter och provning i verifieringsfasen
* **Unik metod:** Gör det möjligt för användare att bättre visualisera och förstå hot
* **Utformad för utvecklare och centrerad på programvara:** många metoder är centrerade på tillgångar eller angripare. Vi är centrerade på programvara. Vi bygger vidare på aktiviteter som alla mjukvaruutvecklare och arkitekter är bekanta med - till exempel att rita bilder för sin mjukvaruarkitektur
* **Fokuserat på designanalys:** Termen "hotmodellering" kan referera till antingen ett krav eller en designanalysteknik. Ibland hänvisar det till en komplex blandning av de två. Microsoft SDL-metoden för hotmodellering är en fokuserad designanalysteknik

## <a name="next-steps"></a>Nästa steg

Tabellen nedan innehåller viktiga länkar för att komma igång med threat modeling Tool:

| Steg  | Beskrivning                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [Ladda ner verktyget för hotmodellering](https://aka.ms/threatmodelingtool)                                |
| **2** | [Läs vår komma igång guide](threat-modeling-tool-getting-started.md)    |
| **3** | [Bekanta dig med funktionerna](threat-modeling-tool-feature-overview.md)   |
| **4** | [Läs mer om genererade hotkategorier](threat-modeling-tool-threats.md)   |
| **5** | [Hitta mildrande åtgärder för genererade hot](threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>Resurser

Här är några äldre artiklar fortfarande relevanta för hot modellering idag:

* [Artikel om vikten av hotmodellering](https://docs.microsoft.com/archive/msdn-magazine/2009/january/security-briefs-getting-started-with-the-sdl-threat-modeling-tool)
* [Utbildning publicerad av Pålitlig Computing](https://www.microsoft.com/download/details.aspx?id=16420)

Kolla in vad några Hot Modeling Tool experter har gjort:

* [Hot Manager](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi Säkerhet Blogg](https://simoneonsecurity.com/)
