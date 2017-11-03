---
title: "Terminologi för Azure Data Catalog | Microsoft Docs"
description: "Den här artikeln innehåller en introduktion till viktiga begrepp och termer som används i Azure Data Catalog-dokumentationen."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6fec74d9-4a3c-4b4b-88ba-cad5ad143331
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 557b529f45c7fbc286b7e1893d4b4688e088ed91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-terminology"></a>Terminologi för Azure Data Catalog
## <a name="catalog"></a>Katalogen
Azure Data Catalog är en molnbaserad metadata-databasen där källor och data tillgångar kan registreras. Katalogen fungerar som en central lagringsplats för strukturella metadata som extraherats från datakällorna och beskrivande metadata som lagts till av användare.

## <a name="data-source"></a>Datakälla
En datakälla är ett system eller en behållare som hanterar datatillgångar. Exempel: SQL Server-databaser, Oracle-databaser, SQL Server Analysis Services-databaser (tabell eller flerdimensionella) och SQL Server Reporting Services-servrar.

## <a name="data-asset"></a>Datatillgång
Datatillgångar är objekt inom datakällor som kan registreras i katalogen. Exempel är SQL Server-tabeller och vyer, Oracle-tabeller och vyer, SQL Server Analysis Services åtgärder, mått och KPI: er och SQL Server Reporting Services-rapporter.

## <a name="data-asset-location"></a>Plats för tillgångsinformation
Katalogen lagrar platsen för en datakälla eller en datatillgång, som kan användas för att ansluta till datakällan med ett klientprogram. Formatera och information om platsen variera beroende på typ av datakälla. Till exempel framgår en SQL Server-tabell av dess fyra delnamn – servernamnet, databasnamnet, schemanamnet, objektnamn – när en SQL Server Reporting Services-rapport kan identifieras av dess URL.

## <a name="structural-metadata"></a>Strukturella metadata
Strukturella metadata är metadata som extraherats från en datakälla som beskriver strukturen i en datatillgång. Detta inkluderar tillgångar plats, dess namn och typ och ytterligare egenskaper för typspecifika. Strukturella metadata för tabeller och vyer innehåller till exempel namn och datatyper för objektets kolumner.

## <a name="descriptive-metadata"></a>Beskrivande metadata
Beskrivande metadata är metadata som beskriver syftet och syftet med en datatillgång. Vanligtvis beskrivande metadata läggs till av katalogens användare med hjälp av Azure Data Catalog-portalen, men den även kan extraheras från datakällan under registreringen. Till exempel Azure Data Catalog registreringsverktyget extrahera beskrivningar från egenskapen beskrivning i SQL Server Analysis Services och SQL Server Reporting Services och från den [ms_description utökad egenskap](https://technet.microsoft.com/library/ms190243.aspx) i SQL Server-databaser, om de här egenskaperna är ifyllda med värden.

## <a name="request-access"></a>Begär åtkomst
En datatillgång beskrivande metadata kan innehålla information om hur du begär åtkomst till datatillgång eller datakälla. Den här informationen visas Dataplats för tillgångsinformation och kan innehålla en eller flera av följande alternativ:

* E-postadressen för användaren eller gruppen som ansvarar för att bevilja åtkomst till datakällan.
* URL till den dokumenterade process som användarna måste följa för att få åtkomst till datakällan.
* URL till en identitets- och hanteringsverktyg (till exempel Microsoft Identity Manager) som kan användas för att få åtkomst till datakällan.
* En fritext-post som beskriver hur användarna kan få åtkomst till datakällan.

## <a name="preview"></a>Förhandsversion
En förhandsgranskning i Azure Data Catalog är en ögonblicksbild av upp till 20 poster som kan extraheras från datakällan under registreringen och lagras i katalogen med data tillgångens metadata. Förhandsgranskningen hjälper användare som identifierar en datatillgång bättre förstå dess funktionen och syftet. Med andra ord ser exempeldata kan vara mer användbart än ser bara kolumnnamn och datatyper.
Förhandsversioner stöds endast för tabeller och vyer och du måste uttryckligen välja av användaren under registreringen.

## <a name="data-profile"></a>Data-profil
En profil för data i Azure Data Catalog är en ögonblicksbild av tabellen nivå och på kolumnnivå metadata om en registrerade datatillgångar som kan extraheras från datakällan under registreringen och lagras i katalogen med data tillgångens metadata. Profilen data hjälper användare som identifierar en datatillgång bättre förstå dess funktionen och syftet. Liknar förhandsgranskningar, data-profiler måste uttryckligen välja av användaren under registreringen.

> [!NOTE]
> Extrahera en data-profil kan vara en kostsam åtgärd för stora tabeller och vyer och ökar den tid som krävs för att registrera en datakälla.
>
>

## <a name="user-perspective"></a>Användarperspektiv
I Azure Data Catalog kan alla användare ange beskrivande metadata för en registrerad datatillgång. Varje användare har olika perspektiv data och dess användning. Till exempel kan administratören ansvarar för en server tillhandahålla information om dess servicenivåavtal (SLA) eller windows säkerhetskopiering. en data-steward kan innehålla länkar till dokumentation för företaget och bearbetar data stöder; och en analytiker kan ange en beskrivning av villkoren som är mest relevant för andra analytiker och som kan vara mest värdefullt för de användare som behöver för att identifiera och förstå informationen.

Var och en av dessa perspektiv är natur värdefulla och med Azure Data Catalog varje användare kan ange den information som är meningsfullt för dem, även om alla användare kan använda informationen för att förstå data och dess syfte.

## <a name="expert"></a>Expert
En expert är en användare som har identifierats som har ett välgrundat ”expert” perspektiv för en datatillgång. Alla användare kan lägga till sig själva eller en annan användare som en expert för en tillgång. Som listats som en expert inte förmedla ytterligare behörighet i Azure Data Catalog. tillåter användare att enkelt lokalisera dessa perspektiv som är mest kan vara användbar när du granskar beskrivande metadata för en tillgång.

## <a name="owner"></a>Ägare
En användare som har ytterligare behörigheter för att hantera en datatillgång i Azure Data Catalog är ägaren. Användare kan bli ägare till registrerade datatillgångar och ägare kan lägga till andra användare som delägare. Mer information finns i [så här hanterar du datatillgångar](data-catalog-how-to-manage.md)  

> [!NOTE]
> Ägarskap och Datorhantering är endast tillgänglig i Standard Edition av Azure Data Catalog.
>
>

## <a name="registration"></a>Registrering
Registreringen är att extrahera data tillgångens metadata från en datakälla och kopiera den till Azure Data Catalog-tjänsten. Datatillgångar som har registrerats kan sedan kommenterats och identifieras.

## <a name="see-also"></a>Se även
* [Vad är Azure Data Catalog?](data-catalog-what-is-data-catalog.md) -Den här artikeln innehåller en översikt över tjänsten Azure Data Catalog och dess värde scenarier som stöds.
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md) -den här artikeln innehåller en vägledning för slutpunkt-till-slutpunkt som visar hur du använder Azure Data Catalog för upptäckt av datakälla.  
