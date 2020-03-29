---
title: Riktlinjer för ansvarsfull användning av syntetisk röstteknik
titleSuffix: Azure Cognitive Services
description: Microsofts allmänna designriktlinjer för användning av syntetisk röstteknik. Dessa har utvecklats i studier som Microsoft genomfört med röst talang, konsumenter, samt personer med talstörningar för att styra en ansvarsfull utveckling av syntetisk röst.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836768"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Riktlinjer för ansvarsfull användning av syntetisk röstteknik
Här är Microsofts allmänna designriktlinjer för användning av syntetisk röstteknik. Dessa har utvecklats i studier som Microsoft genomfört med röst talang, konsumenter, samt personer med talstörningar för att styra en ansvarsfull utveckling av syntetisk röst.

## <a name="general-considerations"></a>Generella saker att tänka på
För distribution av syntetisk talteknik gäller följande riktlinjer i de flesta scenarier.

### <a name="disclose-when-the-voice-is-synthetic"></a>Avslöja när rösten är syntetisk
Att avslöja att en röst är datorgenererad minimerar inte bara risken för skadliga resultat från bedrägeri utan ökar också förtroendet för organisationen att leverera rösten. Läs mer om [hur du avslöjar](concepts-disclosure-guidelines.md).

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Välj lämpliga rösttyper för ditt scenario
Noga överväga ramen för användning och de potentiella skador som är förknippade med att använda syntetisk röst. Till exempel kan högfif syntetiska röster inte vara lämpliga i högriskscenarier, till exempel för personliga meddelanden, finansiella transaktioner eller komplexa situationer som kräver mänsklig anpassningsförmåga eller empati. Användare kan också ha olika förväntningar på rösttyper. Till exempel, när du lyssnar på känsliga nyheter som läses av en syntetisk röst, vissa användare föredrar en mer empatisk och människoliknande läsning av nyheterna, medan andra föredrog en mer monoton, opartisk röst. Överväg att testa ditt program för att bättre förstå användarinställningar.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Var transparent om funktioner och begränsningar
Användare är mer benägna att ha högre förväntningar när de interagerar med high-fidelity syntetiska röstagenter. Följaktligen, när systemkapacitet inte uppfyller dessa förväntningar, förtroende kan lida, och kan resultera i obehagliga, eller till och med skadliga upplevelser.

### <a name="provide-optional-human-support"></a>Ge mänskligt stöd som tillval
I tvetydiga, transaktionsscenarier (till exempel ett supportcenter för samtal) litar användarna inte alltid på en datoragent för att svara på deras begäranden på rätt sätt. Mänskligt stöd kan vara nödvändigt i dessa situationer, oavsett den realistiska kvaliteten på rösten eller förmågan i systemet.

## <a name="considerations-for-voice-talent"></a>Överväganden för rösttalang
När man arbetar med rösttalanger, som röstskådespelare, för att skapa syntetiska röster gäller riktlinjen nedan.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Få meningsfullt samtycke från rösttalang
Röst talang förväntar sig att ha kontroll över sin röst teckensnitt (hur och var det kommer att användas) och kompenseras när som helst det används. Systemägare bör därför få uttryckligt skriftligt tillstånd från rösttalanger och ha tydliga avtalsenliga specifikationer för användningsfall, användningstid, ersättning och så vidare. Vissa röst talang är omedvetna om de potentiella skadliga användningsområden för tekniken och bör utbildas av systemägare om funktionerna i tekniken. För mer information om rösttalang och samtycke, läs vår [Disclosure for Voice Talent](https://aka.ms/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Överväganden för personer med talstörningar
När du arbetar med personer med talstörningar, för att skapa eller distribuera syntetisk röstteknik gäller följande riktlinjer.

### <a name="provide-guidelines-to-establish-contracts"></a>Ge riktlinjer för att upprätta avtal
Ge riktlinjer för att upprätta avtal med personer som använder syntetisk röst för hjälp att tala. Avtalet bör överväga att specificera de parter som äger rösten, användningstiden, kriterier för ägarbyte, förfaranden för att ta bort röstteckensnittet och hur man förhindrar obehörig åtkomst. Dessutom möjliggöra avtalsöverföring av röstteckensnittsägande efter dödsfall till familjemedlemmar om den personen har gett tillstånd.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Redogöra för inkonsekvenser i talmönster
För personer med talstörningar som spelar in sina egna röstteckensnitt kan inkonsekvenser i deras talmönster (sluddrande eller oförmåga att uttala vissa ord) komplicera inspelningsprocessen. I dessa fall bör syntetisk röstteknik och inspelningssessioner rymma dem (det vill säga ge raster och ytterligare antal inspelningssessioner).

### <a name="allow-modification-over-time"></a>Tillåt ändring över tid
Personer med talstörningar önskan att göra uppdateringar av sin syntetiska röst för att återspegla åldrande (till exempel ett barn når puberteten). Individer kan också ha stilistiska preferenser som ändras med tiden, och kanske vill göra ändringar i tonhöjd, accent eller andra röstegenskaper.


## <a name="reference-docs"></a>Referensdokument

* [Avslöjande för Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Gating Översikt](concepts-gating-overview.md)
* [Hur avslöja](concepts-disclosure-guidelines.md)
* [Designmönster för avslöjanden](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Nästa steg

* [Avslöjande för Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Hur avslöja](concepts-disclosure-guidelines.md)
* [Designmönster för avslöjanden](concepts-disclosure-patterns.md)
