---
title: Så här kommenterar du datakällor i Azure Data Catalog
description: Instruktioner som visar hur du kommenterar datatillgångar i Azure Data Catalog, inklusive egna namn, taggar, beskrivningar och experter.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c1e022591ce1aee073330055744fbd78d97c0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950261"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Så här kommenterar du datakällor i Azure Data Catalog

## <a name="introduction"></a>Introduktion

**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Med andra ord handlar Data Catalog om att hjälpa människor att upptäcka, förstå och använda datakällor och hjälpa organisationer att få mer värde från sina befintliga data. När en datakälla registreras med Data Catalog kopieras metadata och indexeras av tjänsten, men artikeln slutar inte där. Data Catalog tillåter användare att ge sina egna beskrivande metadata – till exempel beskrivningar och taggar – för att komplettera metadata som extraherats från datakällan och göra datakällan mer begriplig för fler.

## <a name="annotation-and-crowdsourcing"></a>Anteckningar och crowdsourcing
Alla har en åsikt. Och det här är bra.
Data Catalog erkänner att olika användare har olika perspektiv på företagets datakällor och att vart och ett av dessa perspektiv kan vara värdefullt. Föreställ dig följande scenario:

* Systemadministratören känner till servicenivåavtalet för de servrar eller tjänster som är värdar för datakällan.
* Databasadministratören känner till säkerhetskopieringsschemat för varje databas och de tillåtna ETL-bearbetningsfönstren.
* Systemägaren känner till processen för användare att begära åtkomst till datakällan.
* Dataansvarig vet hur tillgångarna och attributen i datakällen mappas till företagets datamodell.
* Analytikern vet hur data används i samband med de affärsprocesser de stöder.

Vart och ett av dessa perspektiv är värdefullt, och Data Catalog använder en crowdsourcing-metod för metadata som gör att var och en kan fångas in och användas för att ge en fullständig bild av registrerade datakällor. Med hjälp av datakatalogportalen kan varje användare lägga till och redigera sina egna anteckningar, samtidigt som de kan visa anteckningar som tillhandahålls av andra användare.

## <a name="different-types-of-annotations"></a>Olika typer av anteckningar
Datakatalogen stöder följande typer av anteckningar:

| Anteckning | Anteckningar |
| --- | --- |
| Eget namn |Egna namn kan tillhandahållas på datatillgångsnivå, för att göra datatillgångarna lättare att förstå. Egna namn är mest användbara när det underliggande objektnamnet är kryptiskt, förkortat eller på annat sätt inte meningsfullt för användarna. |
| Beskrivning |Beskrivningar kan tillhandahållas på datatillgångs- och attribut-/kolumnnivåer. Beskrivningar är korta textanteckningar i fri form som beskriver användarens perspektiv på datatillgången eller dess användning. |
| Taggar (användartaggar) |Taggar kan levereras på datatillgång och attribut / kolumn nivåer. Användartaggar är användardefinierade etiketter som kan användas för att kategorisera datatillgångar eller attribut. |
| Taggar (ordlista taggar) |Taggar kan levereras på datatillgång och attribut / kolumn nivåer. Ordlistetaggar är centralt definierade ordlistor som kan användas för att kategorisera datatillgångar eller attribut med hjälp av en vanlig affärstaxonomi. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md) |
| Experter |Experter kan tillhandahållas på datatillgångsnivå. Experter identifierar användare eller grupper med expertperspektiv på data och kan fungera som kontaktpunkter för användare som upptäcker registrerade datakällor och har frågor som inte besvaras av de befintliga anteckningarna. |
| Begär åtkomst |Information om begärandeåtkomst kan tillhandahållas på datatillgångsnivå. Den här informationen är till för användare som upptäcker en datakälla som de ännu inte har behörighet att komma åt. Användare kan ange e-postadressen till den användare eller grupp som beviljar åtkomst, webbadressen till processen eller verktyget som användarna behöver för att få åtkomst till eller ange själva processen som text. |
| Dokumentation |Dokumentation kan tillhandahållas på datatillgångsnivå. Tillgångsdokumentation är RTF-information som kan innehålla länkar och bilder och som kan ge all information som inte förmedlas genom beskrivningar och taggar. |

## <a name="annotating-multiple-assets"></a>Kommentera flera tillgångar
När du väljer flera datatillgångar i datakatalogportalen kan användare kommentera alla valda tillgångar i en enda åtgärd. Anteckningar gäller för alla valda tillgångar, vilket gör det enkelt att välja och ge en konsekvent beskrivning och uppsättningar av taggar och experter för relaterade datatillgångar.

> [!NOTE]
> Taggar och experter kan också tillhandahållas när datatillgångar registreras med hjälp av registreringsverktyget för datakatalogen.
>
>

När du väljer flera tabeller och vyer visas endast kolumner som alla valda datatillgångar har gemensamt i datakatalogportalen. På så sätt kan användare ange taggar och beskrivningar för alla kolumner med samma namn för alla valda tillgångar.

## <a name="annotations-and-discovery"></a>Anteckningar och upptäckt
På samma sätt som metadata som extraheras från datakällan under registreringen läggs till i datakatalogens sökindex indexeras även metadata som tillhandahålls av användaren. Detta innebär att inte bara anteckningar gör det lättare för användare att förstå de data de upptäcker, anteckningar gör det också lättare för användare att upptäcka kommenterade datatillgångar genom att söka med hjälp av de termer som är meningsfulla för dem.

## <a name="summary"></a>Sammanfattning
Om du registrerar en datakälla med Data Catalog kan data identifieras genom att strukturella och beskrivande metadata kopieras från datakällan till katalogtjänsten. När en datakälla har registrerats kan användarna tillhandahålla anteckningar för att göra det lättare att upptäcka och förstå inifrån datakatalogportalen.

## <a name="see-also"></a>Se även
* [Komma igång med](data-catalog-get-started.md) azure datakatalogsjälvstudie för steg-för-steg-information om hur du kommenterar datakällor.
