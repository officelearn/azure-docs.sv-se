---
title: "Så här kommenterar du datakällor | Microsoft Docs"
description: "Artikel syntaxmarkering så här kommenterar du datatillgångar i Azure Data Catalog, inklusive egna namn, taggar, beskrivningar och experter."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: f9dd68380773e3cdb93cf1033ccd1b55d9f69305
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-annotate-data-sources"></a>Så här kommenterar du datakällor
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en helt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Data Catalog är med andra ord om hjälper människor identifiera, förstå och använda datakällor och hjälpa organisationer för att få mer värde från deras befintliga data. När en datakälla har registrerats med Data Catalog kan dess metadata kopieras och indexeras av tjänsten, men artikeln det sluta inte. Data Catalog kan användarna ange sina egna beskrivande metadata – som beskrivningar och taggar – att komplettera de metadata som extraherats från datakällan och att göra datakällan lättare att fler användare.

## <a name="annotation-and-crowdsourcing"></a>Anteckningen och gemensamt skapade
Alla har en åsikt. Detta är en bra.
Data Catalog märker att olika användare har olika perspektiv på företagets datakällor och var och en av dessa perspektiv kan vara användbart. Studera följande scenario:

* Systemadministratören vet servicenivåavtalet för servrar eller tjänster som är värdar för datakällan.
* Databasadministratören vet schemat för säkerhetskopiering för varje databas och windows tillåtna ETL-bearbetningen.
* Systemets huvudägare vet processen för användare att begära åtkomst till datakällan.
* Data steward vet hur tillgångar och attribut i datakällan mappas till datamodellen enterprise.
* Analysen vet hur informationen används i samband med affärsprocesser som han har stöd för.

Var och en av dessa perspektiv är värdefullt och Data Catalog använder en tack vare metoden med metadata som gör att varje som ska samlas in och används för att ge en heltäckande bild av registrerade datakällor. Med Data Catalog-portalen kan varje användare lägga till och redigera egna anteckningar när att kunna visa anteckningar som tillhandahålls av andra användare.

## <a name="different-types-of-annotations"></a>Olika typer av anteckningar
Data Catalog stöder följande typer av kommentarer:

| Anteckning | Anteckningar |
| --- | --- |
| Eget namn |Eget namn kan anges på nivån data tillgång till gör datatillgångarna lättare att förstå. Eget namn är mest användbara när det underliggande objektnamnet är kryptiskt, förkortat eller på annat sätt inte meningsfulla för användare. |
| Beskrivning |Beskrivningar som kan anges i datatillgång och attributet / kolumn nivåer. Beskrivningarna är Friform kort textanteckningar som beskriver användarens perspektiv på tillgången data eller dess användning. |
| Taggar (användare taggar) |Taggar kan anges på datatillgång och attributet / kolumn nivåer. Användaren taggar är användardefinierade etiketter som kan användas för att kategorisera datatillgångar eller attribut. |
| Taggar (ordlista taggar) |Taggar kan anges på datatillgång och attributet / kolumn nivåer. Ordlista taggar är centralt definierade ordlista som kan användas för att kategorisera datatillgångar eller attribut med hjälp av en gemensam taxonomi för företag. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md) |
| Experter |Experter kan anges på nivån data tillgången. Experter kan identifiera användare eller grupper med expertkunskaper om data och fungera som kontaktpunkter för användare som identifierar registrerade datakällor och har frågor som inte besvaras av de befintliga kommentarer. |
| Begär åtkomst |Information om åtkomstbegäran åtkomst kan anges på nivån data tillgången. Den här informationen är avsedd för användare som identifierar en datakälla som de inte ännu har behörighet att komma åt. Användare kan ange e-postadressen för den användare eller grupp som beviljar åtkomst, URL-Adressen för processen eller verktyg som användarna behöver för att få åtkomst till, eller kan ange processen sig själv som text. |
| Dokumentation |Dokumentationen kan anges på nivån data tillgången. Tillgångsinformation dokumentation är RTF-information som kan innehålla länkar och bilder och som kan ge information som överförs inte via beskrivningar och taggar. |

## <a name="annotating-multiple-assets"></a>Kommentera flera tillgångar
När du väljer flera datatillgångar i Data Catalog-portalen kan användarna anteckna alla valda tillgångar i en enda åtgärd. Anteckningar gäller för alla valda tillgångar, vilket gör det enkelt att välja och ange en konsekvent beskrivning och uppsättningar med taggar och experter för relaterade datatillgångar.

> [!NOTE]
> Taggar och experter kan också anges när registrera datatillgångar med hjälp av Data Catalog data source registreringsverktyget.
>
>

När du väljer flera tabeller och vyer, endast kolumner alla markerade data som är gemensamma för tillgångar visas i Data Catalog-portalen. Detta ger användarna att ange taggar och beskrivningar för alla kolumner med samma namn för alla valda tillgångar.

## <a name="annotations-and-discovery"></a>Anteckningar och identifiering
Precis som de metadata som extraherats från datakällan under registreringen har lagts till i Data Catalog sökindex, indexeras användardefinierat metadata också. Detta innebär att inte bara anteckningar gör det enklare för användarna att förstå de data som de identifiera anteckningar även gör det enklare för användarna att identifiera kommenterade datatillgångar genom att söka med villkoren som gör att dem.

## <a name="summary"></a>Sammanfattning
Registrera en datakälla med Data Catalog gör att data kan identifieras genom att kopiera strukturella och beskrivande metadata från datakällan till katalogtjänsten. När en datakälla har registrerats, kan användarna ange anteckningar blir enklare att identifiera och förstå från i Data Catalog-portalen.

## <a name="see-also"></a>Se också
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md) självstudiekursens steg för steg-information om hur du kommenterar du datakällor.
