---
title: "Registrera datakällor i Azure Data Catalog | Microsoft Docs"
description: "Den här artikeln visar hur du registrerar datakällor i Azure Data Catalog, inklusive metadatafält extraheras under registreringen."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 48b13eef0960afb4aab68923fb97b5b9c14a3d9f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrera datakällor i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är en helt hanterad molntjänst som fungerar som ett system för registrering och identifiering för företagets datakällor. Med andra ord Data Catalog hjälper användare att identifiera, förstå och använda datakällor och det hjälper organisationer som ger mer värde ur befintliga data. Det första steget att som en datakälla kan identifieras via Data Catalog är att registrera datakällan.

## <a name="register-data-sources"></a>Registrera datakällor
Registrering är processen att extrahera metadata från datakällan och kopiera data till Data Catalog-tjänsten. Informationen ligger kvar där den är och är under kontroll av det aktuella systemets administratörer och principer.

Om du vill registrera en datakälla genom att göra följande:
1. Starta registreringsverktyget för datakällor i Data Catalog i Azure Data Catalog-portalen. 
2. Logga in med ditt arbets- eller skolkonto med samma Azure Active Directory-autentiseringsuppgifter som används för att logga in på portalen.
3. Välj den datakälla som du vill registrera.

Mer detaljerade information finns i [Kom igång med Azure Data Catalog](data-catalog-get-started.md) kursen.

När du har registrerat datakällan, katalogen spårar sin plats och indexerar dess metadata. Användare kan söka, bläddra, identifiera datakällan och använda dess plats för att ansluta till den med hjälp av programmet eller verktyget efter eget val.

## <a name="supported-data-sources"></a>Datakällor som stöds
En lista över datakällor som för närvarande stöds, se [Data Catalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturella metadata
När du registrerar en datakälla hämtar registreringsverktyget information om strukturen på de objekt du väljer. Den här informationen kallas strukturella metadata.

Den här strukturella metadata innehåller objektets plats så att användare som identifierar data som kan använda informationen för att ansluta till objektet i klientverktyg valfri för alla objekt. Andra strukturella metadata innehåller namn och typ och attributet/kolumnnamn och data.

## <a name="descriptive-metadata"></a>Beskrivande metadata
Förutom core strukturella metadata som extraherats från datakällan, extraherar registreringsverktyget för datakällor beskrivande metadata. Dessa metadata har hämtats från Beskrivningsegenskaper som exponeras av dessa tjänster för SQL Server Analysis Services och SQL Server Reporting Services. För SQL Server, värden med hjälp av ms\_beskrivning utökad egenskap ska extraheras. Oracle-databas extraherar registreringsverktyget datakälla kolumnen kommentarer från alla\_FLIKEN\_kommentarer vyn.

Förutom beskrivande metadata som extraherats från datakällan måste ange användare beskrivande metadata med hjälp av registreringsverktyget för datakällor. Användare kan lägga till taggar och de kan identifiera experter för objekt som registreras. Den här beskrivande metadata kopieras till datakatalogstjänsten tillsammans med strukturella metadata.

## <a name="include-previews"></a>Ta med förhandsvisningar
Som standard extraheras från datakällor och kopieras till Data Catalog-tjänsten, men så här fungerar som en datakälla ofta enklare när du kan visa ett prov av data som den innehåller endast metadata.

Du kan inkludera en ögonblicksbild förhandsgranskning av data i varje tabell- och som har registrerats med hjälp av registreringsverktyget för Data Catalog-datakälla. Om du vill ta med förhandsvisningar under registreringen innehåller registreringsverktyget upp till 20 poster från varje tabell och visa. Den här ögonblicksbilden kopieras sedan till katalogen tillsammans med strukturella och beskrivande metadata.

> [!NOTE]
> Wide tabeller med ett stort antal kolumner kan ha färre än 20 poster som ingår i deras preview.
>
>

## <a name="include-data-profiles"></a>Innefattar data profiler
Precis som inklusive förhandsversioner kan ge värdefull kontext för användare som söker efter datakällor i Data Catalog, kan inklusive en data-profil göra det lättare att förstå identifierade datakällor.

Du kan inkludera en data-profil för varje tabell- och som har registrerats med hjälp av registreringsverktyget för Data Catalog-datakälla. Om du vill inkludera en data-profil under registreringen registreringsverktyget innehåller sammanställd statistik om data i varje tabell och visa, inklusive:

* Antalet rader och storleken på data i objektet.
* Datum för senaste uppdatering av data och objekt-schemat.
* Antalet poster som är null och distinkta värden för kolumner.
* Lägsta, högsta, genomsnittlig och standardavvikelsen värdena för kolumner.

Statistiken kopieras sedan till katalogen tillsammans med strukturella och beskrivande metadata.

> [!NOTE]
> Text och datum kolumner ingår inte medelvärde eller standardavvikelse statistik i data-profilen.
>
>

## <a name="update-registrations"></a>Uppdatera registreringar
Registrera en datakälla gör det kan identifieras i Data Catalog när du använder metadata och valfria preview extraheras under registreringen. Om datakällan måste uppdateras i katalogen (till exempel om schemat för ett objekt har ändrats, ursprungligen exkluderade tabellerna ska tas med eller du vill uppdatera data som ingår i förhandsversioner), kan registreringsverktyget för datakällor köras igen.

Registrera en datakälla som redan har registrerats utför en ”upsert” merge-operation: befintliga objekt uppdateras, och nya objekt skapas. Eventuella metadata som tillhandahålls av användare via Data Catalog-portalen är kvar.

## <a name="summary"></a>Sammanfattning
Eftersom den kopierat strukturella och beskrivande metadata från en datakälla till katalogtjänsten gör registrering av datakällan i Data Catalog att blir lättare att identifiera och förstå. När du har registrerat datakällan, kan du kommentera, hantera och identifiera med hjälp av Data Catalog-portalen.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du registrerar datakällor finns i [Kom igång med Azure Data Catalog](data-catalog-get-started.md) kursen.
