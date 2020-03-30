---
title: Terminologi för Azure Data Catalog
description: Den här artikeln innehåller en introduktion till begrepp och termer som används i Azure Data Catalog-dokumentationen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736281"
---
# <a name="azure-data-catalog-terminology"></a>Terminologi för Azure Data Catalog

Den här artikeln innehåller en introduktion till begrepp och termer som används i Azure Data Catalog-dokumentationen.

## <a name="catalog"></a>Katalog

Azure Data Catalog är en molnbaserad metadatadatabas där datakällor och datatillgångar kan registreras. Katalogen fungerar som en central lagringsplats för strukturella metadata som extraherats från datakällor och för beskrivande metadata som lagts till av användare.

## <a name="data-source"></a>Datakälla

En datakälla är ett system eller en behållare som hanterar datatillgångar. Exempel på detta är SQL Server-databaser, Oracle-databaser, SQL Server Analysis Services-databaser (tabell- eller flerdimensionella) och SQL Server Reporting Services-servrar.

## <a name="data-asset"></a>Datatillgång

Datatillgångar är objekt som finns i datakällor som kan registreras i katalogen. Exempel på detta är SQL Server-tabeller och -vyer, Oracle-tabeller och -vyer, SQL Server Analysis Services-mått, dimensioner och KPI:er samt SQL Server Reporting Services-rapporter.

## <a name="data-asset-location"></a>Plats för datatillgång

Katalogen lagrar platsen för en datakälla eller datatillgång, som kan användas för att ansluta till källan med hjälp av ett klientprogram. Platsens format och information varierar beroende på datakälltypen. En SQL Server-tabell kan till exempel identifieras med dess fyra delnamn – servernamn, databasnamn, schemanamn, objektnamn – medan en SQL Server Reporting Services-rapport kan identifieras med url:en.

## <a name="structural-metadata"></a>Strukturella metadata

Strukturella metadata är metadata som extraheras från en datakälla som beskriver strukturen för en datatillgång. Detta inkluderar tillgångsplatsen, dess objektnamn och typ samt ytterligare typspecifika egenskaper. De strukturella metadata för tabeller och vyer innehåller till exempel namn och datatyper för objektets kolumner.

## <a name="descriptive-metadata"></a>Beskrivande metadata

Beskrivande metadata är metadata som beskriver syftet eller avsikten med en datatillgång. Vanligtvis läggs beskrivande metadata till av kataloganvändare som använder Azure Data Catalog-portalen, men det kan också extraheras från datakällan under registreringen. Registreringsverktyget för Azure Data Catalog extraherar till exempel beskrivningar från egenskapen Description i SQL Server Analysis Services och SQL Server Reporting Services och från [den ms_description utökade egenskapen](https://technet.microsoft.com/library/ms190243.aspx) i SQL Server-databaser, om dessa egenskaper har fyllts i med värden.

## <a name="request-access"></a>Begär åtkomst

En datatillgångs beskrivande metadata kan innehålla information om hur du begär åtkomst till datatillgången eller datakällan. Den här informationen visas med datatillgångsplatsen och kan innehålla ett eller flera av följande alternativ:

* E-postadressen till den användare eller det team som ansvarar för att bevilja åtkomst till datakällan.
* URL:en för den dokumenterade processen som användarna måste följa för att få tillgång till datakällan.
* URL:en för ett identitets- och åtkomsthanteringsverktyg (till exempel Microsoft Identity Manager) som kan användas för att få åtkomst till datakällan.
* En fritextpost som beskriver hur användare kan få tillgång till datakällan.

## <a name="preview"></a>Förhandsversion

En förhandsgranskning i Azure Data Catalog är en ögonblicksbild av upp till 20 poster som kan extraheras från datakällan under registreringen och lagras i katalogen med metadata för datatillgång. Förhandsgranskningen kan hjälpa användare som upptäcker en datatillgång att bättre förstå dess funktion och syfte. Med andra ord kan det vara mer värdefullt att se exempeldata än att bara se kolumnnamn och datatyper.
Förhandsgranskningar stöds endast för tabeller och vyer och måste uttryckligen väljas av användaren under registreringen.

## <a name="data-profile"></a>Dataprofil

En dataprofil i Azure Data Catalog är en ögonblicksbild av metadata på tabellnivå och kolumnnivå om en registrerad datatillgång som kan extraheras från datakällan under registreringen och lagras i katalogen med metadata för datatillgång. Dataprofilen kan hjälpa användare som upptäcker en datatillgång att bättre förstå dess funktion och syfte. I likhet med förhandsgranskningar måste dataprofiler uttryckligen väljas av användaren under registreringen.

> [!NOTE]
> Att extrahera en dataprofil kan vara en kostsam åtgärd för stora tabeller och vyer och kan avsevärt öka den tid som krävs för att registrera en datakälla.


## <a name="user-perspective"></a>Användarperspektiv

I Azure Data Catalog kan alla användare tillhandahålla beskrivande metadata för en registrerad datatillgång. Varje användare har ett tydligt perspektiv på data och dess användning. Administratören som ansvarar för en server kan till exempel ange information om dess servicenivåavtal (SLA) eller säkerhetskopieringsfönster. En dataansvarig får tillhandahålla länkar till dokumentation för de affärsprocesser som data stöder. och en analytiker kan ge en beskrivning i de termer som är mest relevanta för andra analytiker, och som kan vara mest värdefulla för de användare som behöver upptäcka och förstå data.

Vart och ett av dessa perspektiv är till sin natur värdefulla, och med Azure Data Catalog kan varje användare tillhandahålla den information som är meningsfull för dem, medan alla användare kan använda den informationen för att förstå data och dess syfte.

## <a name="expert"></a>Expert

En expert är en användare som har identifierats som har en informerad "expert" perspektiv för en datatillgång. Alla användare kan lägga till sig själva eller en annan användare som expert för en tillgång. Att listas som en expert ger inte några ytterligare privilegier i Azure Data Catalog. Det gör det möjligt för användare att enkelt hitta de perspektiv som mest sannolikt är användbara när de granskar en tillgångs beskrivande metadata.

## <a name="owner"></a>Ägare

En ägare är en användare som har ytterligare behörighet för att hantera en datatillgång i Azure Data Catalog. Användare kan bli ägare till registrerade datatillgångar och ägare kan lägga till andra användare som delägare. Mer information finns i [Så här hanterar du datatillgångar](data-catalog-how-to-manage.md)  

> [!NOTE]
> Ägarskap och hantering är endast tillgängliga i Standard Edition av Azure Data Catalog.

## <a name="registration"></a>Registrering

Registrering är handlingen att extrahera metadata för datatillgång från en datakälla och kopiera den till Azure Data Catalog-tjänsten. Datatillgångar som har registrerats kan sedan kommenteras och identifieras.

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa en Azure-datakatalog](data-catalog-get-started.md) 
