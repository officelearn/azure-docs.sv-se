---
title: Hur du använder data som Profileringen datakällor i Azure Data Catalog
description: Artikel fokus på hur du lägger till tabell - och kolumnnivå data profiler när du registrerar datakällor i Azure Data Catalog och hur du använder data profiler för att förstå datakällor.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 64185a951b25b4e04ea5fc65aeede9b0e617d0c5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004320"
---
# <a name="data-profile-data-sources"></a>Datakällor för dataprofil
## <a name="introduction"></a>Introduktion
**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett registrerings- och identifieringssystem för företagsdatakällor. Med andra ord **Azure Data Catalog** handlar om hjälper användarna att identifiera, förstå och använda datakällor och hjälper organisationer att få ut mer av sina befintliga data. När en datakälla har registrerats med **Azure Data Catalog**, dess metadata kopieras och indexeras av tjänsten, men budskapet slutar inte det.

Den **Data profilering** funktion i **Azure Data Catalog** undersöker data från datakällor som stöds i din katalog och samlar in statistik och information om dessa data. Det är enkelt att inkludera en profil för dina datatillgångar. När du registrerar en datatillgång väljer **inkludera Dataprofil** i registreringsverktyget.

## <a name="what-is-data-profiling"></a>Vad är Data profilering
Data profilering undersöker data i datakällan som registreras och samlar in statistik och information om dessa data. Under identifieringen av datakällor, statistiken kan hjälpa dig att fastställa lämpligheten för data som lösa sina affärsproblem.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Följande datakällor stöder profilering av data:

* SQL Server (inklusive Azure SQL DB och Azure SQL Data Warehouse) tabeller och vyer
* Oracle-tabeller och vyer
* Teradata-tabeller och vyer
* Hive-tabeller

Inklusive data profiler när registrera datatillgångar hjälper användare att besvara frågor om datakällor, inklusive:

* Kan den användas för att lösa problemet med mitt företag?
* Data följer särskilda standarder eller mönster?
* Vilka är några av avvikelser för datakällan?
* Vad är möjliga utmaningarna med att integrera dessa data i mitt program?

> [!NOTE]
> Du kan också lägga till dokumentationen för en tillgång som beskriver hur data kan integreras i ett program. Se [så här dokumenterar du datakällor](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Inkludera en dataprofil när du registrerar en datakälla
Det är enkelt att inkludera en profil för datakällan. När du registrerar en datakälla i den **objekt som ska registreras** panelen registreringsverktyget, väljer **inkludera Dataprofil**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Mer information om hur du registrerar datakällor finns [så här registrerar du datakällor](data-catalog-how-to-register.md) och [Kom igång med Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrera efter datatillgångar som innefattar data-profiler
För att identifiera datatillgångar som innehåller en dataprofil, kan du inkludera `has:tableDataProfiles` eller `has:columnsDataProfiles` som en av dina söktermer.

> [!NOTE]
> Att välja **inkludera Dataprofil** i datakällan registreringsverktyget innehåller både tabell- och kolumnnivå profilinformation. Data Catalog-API: et kan dock datatillgångar som ska registreras med endast en uppsättning profilinformation som ingår.
>
>

## <a name="viewing-data-profile-information"></a>Profilinformation för visning av data
När du har hittat en lämplig datakälla med en profil kan du visa profilinformation data. Om du vill visa dataprofilen, Välj en datatillgång och välj **Dataprofil** i fönstret Data Catalog-portalen.

![](media/data-catalog-data-profile/data-catalog-view.png)

En dataprofil i **Azure Data Catalog** visar tabellen och kolumnen profil information inklusive:

### <a name="object-data-profile"></a>Objektet dataprofil
* Antal rader
* Tabellens storlek
* När objektet uppdaterades senast

### <a name="column-data-profile"></a>Kolumnen dataprofil
* Kolumnens datatyp
* Antalet distinkta värden
* Antal rader med NULL-värden
* Lägsta, högsta, genomsnitt och standardavvikelsen för kolumnvärden

## <a name="summary"></a>Sammanfattning
Data som Profileringen tillhandahåller statistik och information om registrerade datatillgångar som hjälper dig att fastställa lämpligheten för data som lösa affärsproblem. Tillsammans med Kommentering och dokumentera datakällor, kan data-profiler ge användare en djupare förståelse för dina data.

## <a name="see-also"></a>Se även
* [Så här registrerar du datakällor](data-catalog-how-to-register.md)
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md)
