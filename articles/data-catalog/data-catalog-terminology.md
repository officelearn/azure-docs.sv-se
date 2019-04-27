---
title: Azure Data Catalog-terminologi
description: Den här artikeln innehåller en introduktion till begrepp och termer som används i Azure Data Catalog-dokumentationen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: a6f2cf1dcee6a85376c8d767e57c504b6b246e5d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580467"
---
# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog-terminologi

Den här artikeln innehåller en introduktion till begrepp och termer som används i Azure Data Catalog-dokumentationen.

## <a name="catalog"></a>Katalog

Azure Data Catalog är en molnbaserad metadata-databasen där källor och data tillgångar kan registreras. Katalogen fungerar som en central lagringsplats för strukturella metadata som extraherats från datakällorna och beskrivande metadata som lagts till av användare.

## <a name="data-source"></a>Datakälla

En datakälla är ett system eller behållare som hanterar datatillgångar. Exempel: SQL Server-databaser, Oracle-databaser, SQL Server Analysis Services-databaser (tabell eller flerdimensionella) och SQL Server Reporting Services-servrar.

## <a name="data-asset"></a>Datatillgång

Datatillgångar finns objekt som finns i datakällor som kan registreras i katalogen. Exempel innefattar SQL Server-tabeller och vyer, Oracle-tabeller och vyer, SQL Server Analysis Services mått, dimensioner och KPI: er och SQL Server Reporting Services-rapporter.

## <a name="data-asset-location"></a>Dataplats för tillgången

Katalogen lagrar platsen för en datakälla eller en datatillgång, som kan användas för att ansluta till källan med hjälp av ett klientprogram. Formatera och information om platsen varierar beroende på typ av datakälla. Till exempel framgår en SQL Server-tabell av fyra delar namnet – servernamnet, databasnamnet, schemanamnet, objektnamn – när en SQL Server Reporting Services-rapport kan identifieras av URL: en.

## <a name="structural-metadata"></a>Strukturella metadata

Strukturella metadata är metadata som extraherats från en datakälla som beskriver strukturen för en datatillgång. Detta inkluderar tillgångar plats, dess objektnamn och typ och ytterligare typspecifika egenskaper. Strukturella metadata för tabeller och vyer som innehåller till exempel namn och datatyper för objektets kolumner.

## <a name="descriptive-metadata"></a>Beskrivande metadata

Beskrivande metadata är metadata som beskriver syftet och syftet med en datatillgång. Vanligtvis beskrivande metadata läggs till av kataloganvändare med hjälp av Azure Data Catalog-portalen, men det kan också kan extraheras från datakällan under registreringen. Till exempel Azure Data Catalog-registreringsverktyget extraherar beskrivningar från egenskapen beskrivning på SQL Server Analysis Services och SQL Server Reporting Services och den [ms_description utökad egenskap](https://technet.microsoft.com/library/ms190243.aspx) i SQL Server-databaser, om de här egenskaperna är ifyllda med värden.

## <a name="request-access"></a>Begär åtkomst

En datatillgång beskrivande metadata kan innehålla information om hur du begär åtkomst till datatillgången eller datakälla. Den här informationen visas Dataplats för tillgången och kan innehålla en eller flera av följande alternativ:

* E-postadress för användaren eller teamet som ansvarar för att bevilja åtkomst till datakällan.
* URL till dokumenterade processen som användarna måste följa för att få åtkomst till datakällan.
* URL till en identitets- och hanteringsverktyg (till exempel Microsoft Identity Manager) som kan användas för att få åtkomst till datakällan.
* En post med fri text som beskriver hur användarna kan få åtkomst till datakällan.

## <a name="preview"></a>Förhandsversion

En förhandsversion i Azure Data Catalog är en ögonblicksbild av upp till 20 poster som kan extraheras från datakällan under registreringen och lagras i katalogen med data tillgångens metadata. Förhandsversionen hjälper användare som identifierar en datatillgång bättre förstå dess funktionen och syftet. Med andra ord visas exempeldata kan vara mer användbart än ser bara de kolumnnamn och datatyper.
Förhandsversioner stöds endast för tabeller och vyer och väljas uttryckligen av användaren under registreringen.

## <a name="data-profile"></a>Dataprofil

En dataprofil i Azure Data Catalog är en ögonblicksbild av tabell- och kolumnnivå metadata om en registrerade datatillgångar som kan extraheras från datakällan under registreringen och lagras i katalogen med data tillgångens metadata. Dataprofil kan användare som identifierar en datatillgång bättre förstå dess funktionen och syftet. På liknande sätt som gäller för förhandsversioner, data-profiler måste uttryckligen väljas av användaren under registreringen.

> [!NOTE]
> Extrahera en dataprofil kan vara en kostsam åtgärd för stora tabeller och vyer och kan du drastiskt minska den tid som krävs för att registrera en datakälla.


## <a name="user-perspective"></a>Användarperspektiv

I Azure Data Catalog kan alla användare ange beskrivande metadata för en registrerade datatillgångar. Alla användare har en distinkt perspektiv på data och dess användning. Exempelvis kan kan administratören ansvarar för en server ge information om dess serviceavtal (SLA) eller säkerhetskopieringsfönster; en data-steward kan innehålla länkar till dokumentation för företaget och bearbetar data stöder; och en analytiker kan ange en beskrivning i villkor som är mest relevant för andra analytiker och som kan vara mest värdefullt för de användare som behöver för att identifiera och förstå data.

Var och en av dessa perspektiv är sin natur värdefulla och med Azure Data Catalog varje användare kan ange den information som passar dem, även om alla användare kan använda informationen för att förstå data och dess syfte.

## <a name="expert"></a>Expert

En expert är en användare som har identifierats som har en välgrundade ”experten” perspektiv för en datatillgång. Alla användare kan lägga till sig själva eller en annan användare som en expert för en tillgång. Listas som en expert inte förmedlar ytterligare behörighet i Azure Data Catalog. tillåter användare att enkelt lokalisera dessa perspektiv som mest sannolikt ska vara användbar när du granskar beskrivande metadata för en tillgång.

## <a name="owner"></a>Ägare

En användare som har ytterligare behörigheter för att hantera en datatillgång i Azure Data Catalog är ägaren. Användare kan bli ägare av registrerade datatillgångar och ägare kan lägga till andra användare som Medägare. Mer information finns i [så här hanterar du datatillgångar](data-catalog-how-to-manage.md)  

> [!NOTE]
> Ägarskap och hantering finns bara i Standard Edition av Azure Data Catalog.

## <a name="registration"></a>Registrering

Registreringen är av extraherar data tillgångens metadata från en datakälla och kopierar den till Azure Data Catalog-tjänsten. Datatillgångar som har registrerats kan sedan kommenterade och identifieras.

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa en Azure Data Catalog](data-catalog-get-started.md) 
