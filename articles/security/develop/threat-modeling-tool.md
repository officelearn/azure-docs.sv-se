---
title: Microsoft Threat Modeling Tool översikt – Azure
description: Översikt över Microsoft Threat Modeling Tool som innehåller information om att komma igång med verktyget, inklusive Threat Modeling-processen.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 02/16/2017
ms.openlocfilehash: 0d800102b6f6ff77944a2b625d3bcecef69c1ee9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75548744"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsoft Threat Modeling Tool

Threat Modeling Tool är ett kärn element i Microsoft Security Development Lifecycle (SDL). Det gör att Software Architects kan identifiera och minska potentiella säkerhets problem tidigt, när de är relativt enkla och kostnads effektiva att lösa. Det innebär att den totala kostnaden för utveckling minskar avsevärt. Dessutom utformade vi verktyget med icke-säkerhetsexperter i åtanke, vilket gör det enklare för alla utvecklare att skapa och analysera hot modeller. 

Verktyget gör det möjligt för alla att:

* Kommunicera om systemets säkerhets design
* Analysera dessa designer för potentiella säkerhets problem med en beprövad metod
* Föreslå och hantera begränsningar av säkerhets problem

Här följer några verktygs funktioner och innovationer, bara namn på några:

* **Automation:** Vägledning och feedback vid ritning av en modell
* **Kliv per element:** Guidad analys av hot och begränsningar
* **Rapportering:** Säkerhets aktiviteter och testning i verifierings fasen
* **Unik metod:** Gör att användare kan visualisera och förstå hot bättre
* **Utformad för utvecklare och centrerade på program vara:** många metoder är centrerade på till gångar eller angripare. Vi är centrerade på program vara. Vi bygger på aktiviteter som alla program varu utvecklare och arkitekter är bekanta med – till exempel rit bilder för deras program varu arkitektur
* **Fokuserat på design analys:** Termen "Hot modellering" kan referera till antingen ett krav eller en konstruktions analys teknik. Ibland refererar den till en komplex blandning av de två. Microsoft SDL-metoden för hot modellering är en fokuserad design analys teknik

## <a name="next-steps"></a>Nästa steg

Tabellen nedan innehåller viktiga länkar för att komma igång med Threat Modeling Tool:

| Steg  | Beskrivning                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [Ladda ned Threat Modeling Tool](https://aka.ms/threatmodelingtool)                                |
| **2** | [Läs vår kom igång-guide](threat-modeling-tool-getting-started.md)    |
| **3** | [Bekanta dig med funktionerna](threat-modeling-tool-feature-overview.md)   |
| **4** | [Lär dig mer om genererade hot kategorier](threat-modeling-tool-threats.md)   |
| **5** | [Hitta begränsningar för genererade hot](threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>Resurser

Här är några äldre artiklar som är relevanta för hot modellering idag:

* [Artikel om vikten av hot modellering](https://docs.microsoft.com/archive/msdn-magazine/2009/january/security-briefs-getting-started-with-the-sdl-threat-modeling-tool)
* [Utbildning Publicerad av tillförlitlig data behandling](https://www.microsoft.com/download/details.aspx?id=16420)

Ta en titt på vad några Threat Modeling Tool experter har gjort:

* [Threat Manager](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Säkerhets blogg om Magnusson Curzi](https://simoneonsecurity.com/)
