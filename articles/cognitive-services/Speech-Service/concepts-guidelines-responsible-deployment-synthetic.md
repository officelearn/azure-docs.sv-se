---
title: Rikt linjer för ansvarig distribution av syntetisk röst teknik
titleSuffix: Azure Cognitive Services
description: Microsofts allmänna design rikt linjer för att använda syntetisk röst teknik. De här utvecklades i studier som Microsoft genomförde med röst personal, konsumenter, och för användare med tal sjukdomar för att vägleda utvecklingen av syntetisk röst.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 7d80ffb575c6aa15695279584b58288cbc16be43
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024984"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Rikt linjer för ansvarig distribution av syntetisk röst teknik
Här följer Microsofts allmänna design rikt linjer för att använda syntetisk röst teknik. Dessa utvecklades i studier som Microsoft genomförde med röst personal, konsumenter, samt individer med tal sjukdomar för att vägleda utvecklingen av syntetisk röst.

## <a name="general-considerations"></a>Generella saker att tänka på
För distribution av syntetisk tal teknik gäller följande rikt linjer för de flesta scenarier.

### <a name="disclose-when-the-voice-is-synthetic"></a>Lämna ut när rösten är syntetisk
Om du avmarkerar att en röst är en dator som genereras kan du inte bara minimera risken för skadliga resultat från bedrägeri, men det ökar också förtroendet i organisationen som levererar rösten. Lär dig mer om [att lämna](concepts-disclosure-guidelines.md)ut.

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Välj lämpliga röst typer för ditt scenario
Överväg noga användnings sammanhanget och de potentiella skadan som är associerade med att använda syntetisk röst. Till exempel är det inte säkert att de syntetiska rösterna med hög kvalitet är lämpliga i högrisk scenarier, t. ex. för personliga meddelanden, ekonomiska transaktioner eller komplexa situationer som kräver mänsklig anpassning eller empati. Användarna kan också ha olika förväntningar för röst typer. Till exempel, när du lyssnar på känsliga nyheter som läses av en syntetisk röst, föredrar vissa användare en mer empatisk och mänsklig läsning av nyheterna, medan andra föredrar en mer enkel färgs visning av rösten. Överväg att testa ditt program för att bättre förstå användar inställningarna.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Var transparent om funktioner och begränsningar
Det är mer troligt att användarna har fler förväntningar när de använder syntetiska röst agenter med hög kvalitet. Det innebär att om system funktionerna inte uppfyller förväntningarna kan förtroendet bli lidande och kan resultera i krångligt eller till och med skadliga upplevelser.

### <a name="provide-optional-human-support"></a>Ge tillvals personal support
I tvetydiga, transaktions scenarier (till exempel ett Call Support Center) kan användarna inte alltid lita på en dator agent för att svara på lämpliga förfrågningar. Stöd för mänsklig personal kan vara nödvändigt i dessa fall, oberoende av den realistiska kvaliteten på systemets röst eller funktion.

## <a name="considerations-for-voice-talent"></a>Överväganden för röst personal
När du arbetar med röst personal, till exempel röst aktörer, för att skapa syntetiska röster, gäller rikt linjerna nedan.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Få ett meningsfullt godkännande från röst personal
Röst personal förväntar sig att ha kontroll över deras röst teckensnitt (hur och var den ska användas) och ersätts när den används. System ägare bör därför få uttryckligt skriftligt tillstånd från röst personal och ha tydliga avtals krav för användnings fall, varaktigheten för användning, kompensation och så vidare. Vissa röst personal är inte medvetna om den potentiella skadliga användningen av tekniken och bör vara sammanutbildad av systemets ägare om funktionerna i tekniken. Mer information om röst personal och medgivande finns i vår information [om röst personal](/legal/cognitive-services/speech-service/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Att tänka på med tal sjukdomar
När du arbetar med individer med tal sjukdomar, för att skapa eller distribuera syntetisk röst teknik gäller följande rikt linjer.

### <a name="provide-guidelines-to-establish-contracts"></a>Ange rikt linjer för att upprätta kontrakt
Ange rikt linjer för att skapa avtal med personer som använder syntetisk röst för att få hjälp med att tala. Kontraktet bör överväga att ange de parter som äger rösten, varaktigheten för användning, villkor för ägarskaps överföring, procedurer för att ta bort röst teckensnittet och hur du förhindrar obehörig åtkomst. Du kan också aktivera den avtalade överföringen av röst teckensnitts ägande efter dödsfall till familje medlemmar om den personen har fått behörighet.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Konto för inkonsekvenser i tal mönster
För enskilda användare med tal sjukdomar som registrerar sina egna röst teckensnitt kan inkonsekvenser i deras tal mönster (slurring eller oförmåga att uttala vissa ord) komplicera inspelnings processen. I dessa fall bör syntetisk röst teknik och inspelnings sessioner hantera dem (det vill säga ge raster och ytterligare antal inspelnings sessioner).

### <a name="allow-modification-over-time"></a>Tillåt ändring över tid
Individer med tal sjukdomar vill göra uppdateringar av sin syntetiska röst för att återspegla ålders fördelning (till exempel ett barn som når puberty). Individer kan också ha stilistiska inställningar som förändras över tid, och det kan vara bra att göra ändringar i bredd, dekor färg eller andra röst egenskaper.


## <a name="reference-docs"></a>Referens dokument

* [Utlämnande av röst personal](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Översikt över hantera](concepts-gating-overview.md)
* [Lämna ut](concepts-disclosure-guidelines.md)
* [Design mönster för utlämnande](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Nästa steg

* [Utlämnande av röst personal](/legal/cognitive-services/speech-service/disclosure-voice-talent)
* [Lämna ut](concepts-disclosure-guidelines.md)
* [Design mönster för utlämnande](concepts-disclosure-patterns.md)