---
title: Så här använder du dataprofileringsdatakällor i Azure Data Catalog
description: Hur du visar hur du inkluderar dataprofiler på tabell- och kolumnnivå när du registrerar datakällor i Azure Data Catalog och hur du använder dataprofiler för att förstå datakällor.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 04ac6c2bf0137289221a4ae6ed58d5a71ad21739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950224"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Dataprofildatakällor i Azure Data Catalog

## <a name="introduction"></a>Introduktion

**Microsoft Azure Data Catalog** är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och identifieringssystem för företagets datakällor. Med andra ord handlar **Azure Data Catalog** om att hjälpa människor att upptäcka, förstå och använda datakällor och hjälpa organisationer att få mer värde från sina befintliga data. När en datakälla registreras med **Azure Data Catalog**kopieras och indexeras dess metadata av tjänsten, men artikeln slutar inte där.

Funktionen **Dataprofilering** i **Azure Data Catalog** undersöker data från datakällor som stöds i katalogen och samlar in statistik och information om dessa data. Det är enkelt att inkludera en profil på dina datatillgångar. När du registrerar en datatillgång väljer du **Inkludera dataprofil** i registreringsverktyget för datakällan.

## <a name="what-is-data-profiling"></a>Vad är dataprofilering

Dataprofilering undersöker data i den registrerade datakällan och samlar in statistik och information om dessa uppgifter. Under identifiering av datakällor kan den här statistiken hjälpa dig att avgöra om data är lämpliga för att lösa deras affärsproblem.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Följande datakällor stöder dataprofilering:

* SQL Server-tabeller och -vyer (inklusive Azure SQL DB och Azure SQL Data Warehouse)
* Oracle-tabeller och -vyer
* Tabeller och vyer för Teradata
* Hive-tabeller

Genom att inkludera dataprofiler när datatillgångar registreras kan användarna svara på frågor om datakällor, bland annat:

* Kan den användas för att lösa mitt affärsproblem?
* Överensstämmer uppgifterna med särskilda standarder eller mönster?
* Vilka är några av datakällans avvikelser?
* Vilka är möjliga utmaningar med att integrera dessa data i mitt program?

> [!NOTE]
> Du kan också lägga till dokumentation till en tillgång för att beskriva hur data kan integreras i ett program. Se [Så här dokumenterar du datakällor](data-catalog-how-to-documentation.md).
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Så här tar du med en dataprofil när du registrerar en datakälla

Det är enkelt att inkludera en profil för din datakälla. När du registrerar en datakälla väljer du **Inkludera dataprofil**i den objekt **som ska registreras** på panelen För registrering av datakälla.

![Kryssrutan Inkludera dataprofil](media/data-catalog-data-profile/data-catalog-register-profile.png)

Mer information om hur du registrerar datakällor finns i [Så här registrerar du datakällor](data-catalog-how-to-register.md) och kommer igång med Azure Data [Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrering av datatillgångar som innehåller dataprofiler

Om du vill identifiera datatillgångar som `has:tableDataProfiles` innehåller `has:columnsDataProfiles` en dataprofil kan du inkludera eller som ett av dina söktermer.

> [!NOTE]
> Om du väljer **Inkludera dataprofil** i registreringsverktyget för datakällan finns profilinformation för både tabell- och kolumnnivå. Med API:et för datakatalogen kan datatillgångar dock registreras med endast en uppsättning profilinformation som ingår.
>

## <a name="viewing-data-profile-information"></a>Visa information om dataprofil

När du har hittat en lämplig datakälla med en profil kan du visa dataprofilinformationen. Om du vill visa dataprofilen väljer du en datatillgång och väljer **Dataprofil** i portalfönstret Datakatalog.

![Fliken Dataprofil](media/data-catalog-data-profile/data-catalog-view.png)

En dataprofil i **Azure Data Catalog** visar tabell- och kolumnprofilinformation, inklusive:

### <a name="object-data-profile"></a>Objektdataprofil

* Antal rader
* Tabellstorlek
* När objektet senast uppdaterades

### <a name="column-data-profile"></a>Kolumndataprofil

* Kolumndatatyp
* Antal distinkta värden
* Antal rader med NULL-värden
* Minsta, högsta, genomsnittliga och standardavvikelse för kolumnvärden

## <a name="summary"></a>Sammanfattning

Dataprofilering ger statistik och information om registrerade datatillgångar som hjälper dig att avgöra om data är lämpliga för att lösa affärsproblem. Tillsammans med anteckningar och dokumentering av datakällor kan dataprofiler ge användarna en djupare förståelse för dina data.

## <a name="see-also"></a>Se även

* [Så här registrerar du datakällor](data-catalog-how-to-register.md)
* [Kom igång med Azure Data Catalog](data-catalog-get-started.md)
