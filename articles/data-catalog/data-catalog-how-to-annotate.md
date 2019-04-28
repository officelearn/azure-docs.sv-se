---
title: Så här kommenterar du datakällor i Azure Data Catalog
description: Artikel markering så här kommenterar du datatillgångar i Azure Data Catalog, inklusive egna namn, taggar, beskrivningar och experter.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 3a66c8c5963972828723dd74ffe560a0e2240165
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001944"
---
# <a name="how-to-annotate-data-sources"></a>Så här kommenterar du datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för företagsdatakällor. Data Catalog är med andra ord om hjälper användarna att identifiera, förstå och använda datakällor och att hjälpa organisationer för att få ut mer av sina befintliga data. När en datakälla har registrerats med Data Catalog, dess metadata kopieras och indexeras av tjänsten, men budskapet slutar inte det. Data Catalog kan användarna ange sina egna beskrivande metadata – till exempel beskrivningar och taggar – att komplettera metadata som extraheras från datakällan och för att göra datakällan mer begriplig för fler personer.

## <a name="annotation-and-crowdsourcing"></a>Anteckning och gemensamt skapade
Alla har en åsikt. Och det är bra.
Data Catalog identifierar att olika användare har olika perspektiv på företagets datakällor och var och en av dessa perspektiv kan vara användbart. Föreställ dig följande:

* Systemadministratören vet servicenivåavtalet för servrar eller tjänster som är värdar för datakällan.
* Databasadministratören vet schemat för säkerhetskopiering för varje databas och windows-tillåtna ETL-bearbetningen.
* Systemets huvudägare vet processen för användare att begära åtkomst till datakällan.
* Data steward vet hur tillgångar och attribut i datakällan mappar i enterprise-datamodellen.
* Analytikern vet hur de används i samband med affärsprocesser som han har stöd för.

Var och en av dessa perspektiv är värdefulla och Data Catalog använder en användargenererade metadata som gör att varje som ska samlas in och används för att ge en heltäckande bild av registrerade datakällor. Med Data Catalog-portalen kan varje användare lägga till och redigera egna anteckningar samtidigt kunna visa kommentarer som tillhandahålls av andra användare.

## <a name="different-types-of-annotations"></a>Olika typer av kommentarer
Data Catalog stöder följande typer av kommentarer:

| Anteckning | Anteckningar |
| --- | --- |
| Eget namn |Användarvänliga namn kan anges på nivån data tillgång till gör datatillgångarna mer lätt att förstå. Egna namn är mest användbara när det underliggande objektnamnet kryptisk, förkortat eller annars inte meningsfulla för användare. |
| Beskrivning |Beskrivningar kan anges på datatillgång och attributet / kolumn nivåer. Beskrivningar är anteckningar i fritt format kortfattad text som beskriver användarens perspektiv på datatillgången eller dess användning. |
| Taggar (användartaggar) |Taggar kan anges på datatillgång och attributet / kolumn nivåer. Användartaggar är en användardefinierad etiketter som kan användas för att kategorisera datatillgångar eller attribut. |
| Taggar (ordlista taggar) |Taggar kan anges på datatillgång och attributet / kolumn nivåer. Ordlista taggar är centralt definierade ordlistetermer som kan användas för att kategorisera datatillgångar eller attribut med hjälp av en gemensam taxonomi för företag. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md) |
| Experter |Experter kan anges på nivån data tillgången. Experter kan identifiera användare eller grupper med expertkunskaper om data hur och fungera som kontaktpunkter för användare som identifierar de registrerade datakällorna och har frågor som inte besvaras i de befintliga kommentarer. |
| Begär åtkomst |Information om tjänstbegäran åtkomst kan anges på nivån data tillgången. Den här informationen är för användare som identifierar en datakälla som de inte har behörighet att komma åt. Användare kan ange den e-postadressen till användare eller grupp som beviljar åtkomst, URL-Adressen till processen eller verktyg som användarna behöver för att få åtkomst till, eller kan ange själva processen som text. |
| Dokumentation |Dokumentationen kan anges på nivån data tillgången. Dokumentation för tillgången är formaterad textinformation som kan innehålla länkar och bilder, och som kan tillhandahålla information som inte överförs via beskrivningar och taggar. |

## <a name="annotating-multiple-assets"></a>Kommentera flera tillgångar
När du väljer flera datatillgångar i Data Catalog-portalen, kan användare kommentera alla markerade tillgångar i en enda åtgärd. Anteckningar gäller för alla markerade tillgångar, vilket gör det enkelt att välja och ger en konsekvent beskrivning och uppsättningar med taggar och experter för relaterade datatillgångar.

> [!NOTE]
> Taggar och experter kan också anges när registrera datatillgångar med hjälp av Data Catalog-data source registreringsverktyget.
>
>

När att välja flera tabeller och vyer, endast kolumner alla markerade tillgångar har gemensamt data visas i Data Catalog-portalen. På så sätt kan användarna måste ange taggar och beskrivningar för alla kolumner med samma namn för alla markerade tillgångar.

## <a name="annotations-and-discovery"></a>Anteckningar och identifiering
Precis som de metadata som extraherats från datakällan under registreringen har lagts till Data Catalog search-index kan indexeras anges av användaren metadata också. Detta innebär att inte bara anteckningar gör det enklare för användarna att förstå de data som de upptäcker anteckningar också gör det enklare för användarna att identifiera de kommenterade datatillgångarna genom att söka med hjälp av de villkor som passar dem.

## <a name="summary"></a>Sammanfattning
Registrera en datakälla med Data Catalog gör dessa data kan upptäckas genom att kopiera strukturella och beskrivande metadata från datakällan till Catalog-tjänsten. När en datakälla har registrerats, kan användarna ange anteckningar så att det blir lättare att identifiera och förstå från i Data Catalog-portalen.

## <a name="see-also"></a>Se också
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md) självstudierna stegvis information om hur du kommenterar du datakällor.
