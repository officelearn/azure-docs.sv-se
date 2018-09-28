---
title: Registrera datakällor i Azure Data Catalog
description: Den här artikeln visar hur du registrerar datakällor i Azure Data Catalog, inklusive metadatafälten som extraherats under registreringen.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 413f9340432f39d60ccdb43557616b4123132873
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404902"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrera datakällor i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och upptäckt för företagsdatakällor. Med andra ord Data Catalog hjälper människor att identifiera, förstå och använda datakällor och det hjälper organisationer att få ut mer av sina befintliga data. Det första steget att som en datakälla kan identifieras via Data Catalog är att registrera datakällan.

## <a name="register-data-sources"></a>Registrera datakällor
Registreringen är processen där du extraherar metadata från datakällan och kopierar dessa data till Data Catalog-tjänsten. Informationen ligger kvar där den är och är under kontroll av det aktuella systemets administratörer och principer.

Registrera en datakälla genom att göra följande:
1. Starta Data Catalog registreringsverktyget i Azure Data Catalog-portalen. 
2. Logga in med ditt arbets- eller skolkonto med samma Azure Active Directory-autentiseringsuppgifter som används för att logga in på portalen.
3. Välj den datakälla som du vill registrera.

Stegvisa mer i den [Kom igång med Azure Data Catalog](data-catalog-get-started.md) självstudien.

När du har registrerat datakällan, katalogen spårar dess plats och indexeras dess metadata. Användare kan söka, bläddra, och identifiera datakällan och sedan använda dess plats för att ansluta till den med hjälp av program eller deras verktyg.

## <a name="supported-data-sources"></a>Datakällor som stöds
En lista över datakällor som stöds för närvarande finns i [DSR för Data Catalog](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturella metadata
När du registrerar en datakälla extraherar registreringsverktyget information om strukturen för de objekt som du väljer. Den här informationen kallas strukturella metadata.

Den här strukturella metadata innehåller objektets plats, så att användare som identifierar data som kan använda informationen för att ansluta till objektet i klientverktyg för valfri för alla objekt. Andra strukturella metadata innehåller objektnamn och typ och attributet/kolumnnamn och data.

## <a name="descriptive-metadata"></a>Beskrivande metadata
Förutom core strukturella metadata som extraheras från datakällan, extraherar registreringsverktyget beskrivande metadata. För SQL Server Analysis Services och SQL Server Reporting Services hämtas metadata från beskrivning-egenskaper som exponeras av dessa tjänster. För SQL Server, värden med hjälp av ms\_beskrivning utökad egenskap ska extraheras. För Oracle Database, extraherar registreringsverktyget för datakällor kolumnen kommentarer från alla\_FLIKEN\_kommentarer vy.

Förutom de beskrivande metadata som extraheras från datakällan, kan användare ange beskrivande metadata med hjälp av registreringsverktyget för källan. Användare kan lägga till taggar och de kan identifiera experter för de objekt som håller på att registreras. Dessa beskrivande metadata kopieras till Data Catalog-tjänsten tillsammans med strukturella metadata.

## <a name="include-previews"></a>Ta med förhandsvisningar
Som standard extraheras från datakällor och kopieras till Data Catalog-tjänsten, men om en datakälla ofta enklare när du visar ett exempel på data som den innehåller endast metadata.

Du kan inkludera en ögonblicksbild förhandsgranskning av data i varje tabell och en vy som har registrerats med hjälp av registreringsverktyget för Data Catalog-datakällan. Om du väljer att inkludera förhandsversioner under registreringen, inkluderar registreringsverktyget upp till 20 poster från varje tabell och visa. Den här ögonblicksbilden kopieras sedan till katalogen tillsammans med strukturella och beskrivande metadata.

> [!NOTE]
> Breda tabeller med ett stort antal kolumner kan ha färre än 20 poster som ingår i deras förhandsversion.
>
>

## <a name="include-data-profiles"></a>Innefattar data-profiler
Precis som inklusive förhandsgranskningar kan ge värdefulla kontext för användare som söker efter datakällor i Data Catalog, kan inklusive en dataprofil göra det lättare att förstå identifierade datakällor.

Du kan inkludera en dataprofil för varje tabell och en vy som har registrerats med hjälp av registreringsverktyget för Data Catalog-datakällan. Om du väljer att inkludera en dataprofil vid registreringen, registreringsverktyget innehåller sammanställd statistik om hur data i varje tabell och vyn, inklusive:

* Antalet rader och storleken på data i objektet.
* Datum för senaste uppdatering av data och objekt-schemat.
* Antalet null-poster och distinkta värden för kolumner.
* Lägsta, högsta, genomsnitt och standardavvikelsen värden för kolumner.

Statistiken kopieras sedan till katalogen tillsammans med strukturella och beskrivande metadata.

> [!NOTE]
> Text och datum kolumner omfattar inte medelvärde eller standardavvikelse statistik i profilen för deras data.
>
>

## <a name="update-registrations"></a>Uppdatera registreringar
Registrering av datakälla gör det synliga i Data Catalog när du använder metadata och valfritt förhandsversion extraheras under registreringen. Om datakällan måste uppdateras i katalogen (till exempel om schemat för ett objekt har ändrats, tabeller som ursprungligen exkluderade ska inkluderas eller du vill uppdatera de data som ingår i förhandsgranskning), kan registreringsverktyget köras igen.

Registrera en datakälla som har redan registrerats utför en ”upsert” merge-operation: befintliga objekt uppdateras, och nya objekt skapas. Alla metadata som tillhandahålls av användare med hjälp av Data Catalog-portalen finns kvar.

## <a name="summary"></a>Sammanfattning
Eftersom den kopierat strukturella och beskrivande metadata från en datakälla till katalogtjänsten gör registrering av datakällan i Data Catalog data enklare att identifiera och förstå. När du har registrerat datakällan kan du lägga till anteckningar, hantera och identifierar dem med hjälp av Data Catalog-portalen.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du registrerar datakällor finns i den [Kom igång med Azure Data Catalog](data-catalog-get-started.md) självstudien.
