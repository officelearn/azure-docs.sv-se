---
title: "Hur du datakällor för Data-profil"
description: "Artikel markering av hur du lägger till profiler för tabell - och på kolumnnivå data när du registrerar datakällor i Azure Data Catalog och hur du använder data profiler för att förstå datakällor."
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: spelluru
ms.openlocfilehash: 80181b729ffa6d6cbc2d17fe8a5ae8ee4e3d41ab
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="data-profile-data-sources"></a>Datakällor för dataprofil
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en helt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Med andra ord **Azure Data Catalog** handlar om hjälper människor identifiera, förstå och använda datakällor och hjälper organisationer att få ut mer av sina befintliga data. När en datakälla har registrerats med **Azure Data Catalog**, dess metadata kopieras och indexeras av tjänsten, men artikeln inte det avslutas.

Den **Data profilering** funktion i **Azure Data Catalog** undersöker data från datakällor som stöds i katalogen och samlar in information om dessa data och statistik. Det är enkelt att inkludera en profil för dina datatillgångar. När du registrerar en datatillgång, välja **inkludera Data profil** i registreringsverktyget för datakällor.

## <a name="what-is-data-profiling"></a>Vad är profilering av Data
Data profilering undersöker data i datakällan som registreras och samlar in information om dessa data och statistik. Datakällsidentifiering, kan statistiken hjälpa dig att avgöra lämpligheten av informationen som ska lösa sina affärsproblem.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Följande datakällor stöder profilering av data:

* SQL Server (inklusive Azure SQL DB och Azure SQL Data Warehouse) tabeller och vyer
* Oracle-tabeller och vyer
* Teradata-tabeller och vyer
* Hive-tabeller

Inklusive profiler data när du registrerar datatillgångar hjälper användarna att besvara frågor om datakällor, inklusive:

* Kan den användas för att lösa problemet mitt företag?
* Data följer särskilda standarder eller mönster?
* Vilka är några av avvikelser av datakällan?
* Vad är möjliga utmaningarna med att integrera informationen i mitt program?

> [!NOTE]
> Du kan också lägga till dokumentationen för en tillgång för att beskriva hur data kan integreras i ett program. Se [så här dokumenterar du datakällor](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Hur du lägger till en profil för data när du registrerar en datakälla
Det är enkelt att inkludera en profil av datakällan. När du registrerar en datakälla i den **objekt som ska registreras** panelen registreringsverktyget för datakällor väljer **inkludera Data profil**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Mer information om hur du registrerar datakällor finns [att registrera datakällor](data-catalog-how-to-register.md) och [Kom igång med Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrering på datatillgångar som innehåller data profiler
För att identifiera datatillgångar som innehåller en data-profil, kan du inkludera `has:tableDataProfiles` eller `has:columnsDataProfiles` som en av dina sökvillkor.

> [!NOTE]
> Att välja **inkludera Data profil** i datakällan registreringsverktyget innehåller både tabell och på kolumnnivå profilinformation. Data Catalog-API: et kan dock datatillgångar registreras med bara en uppsättning profilinformation som ingår.
>
>

## <a name="viewing-data-profile-information"></a>Visa information om profilen
Du kan visa information om profilen när du har hittat en lämplig datakälla med en profil. Om du vill visa data profil, markera en datatillgång och välj **Data profil** i fönstret som Data Catalog-portalen.

![](media/data-catalog-data-profile/data-catalog-view.png)

En data-profil i **Azure Data Catalog** visar tabellen och kolumnen profil information, bland annat:

### <a name="object-data-profile"></a>Objektet data profil
* Antal rader
* Tabellens storlek
* Då objektet senast uppdaterades

### <a name="column-data-profile"></a>Kolumnen data profil
* Kolumnens datatyp
* Antalet distinkta värden
* Antal rader med NULL-värden
* Lägsta, högsta, medelvärde och standardavvikelsen för kolumnvärden

## <a name="summary"></a>Sammanfattning
Data profilering ger statistik och information om registrerade datatillgångar för att bestämma lämpligheten av informationen som ska lösa affärsproblem. Tillsammans med kommentera och dokumentera datakällor, kan data-profiler ge användare en bättre förståelse av dina data.

## <a name="see-also"></a>Se även
* [Så här registrerar du datakällor](data-catalog-how-to-register.md)
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md)
