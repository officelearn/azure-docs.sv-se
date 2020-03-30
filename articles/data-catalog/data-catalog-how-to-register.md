---
title: Registrera datakällor i Azure Data Catalog
description: I den här artikeln beskrivs hur du registrerar datakällor i Azure Data Catalog, inklusive metadatafält som extraherades under registreringen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736328"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrera datakällor i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog är en fullständigt hanterad molntjänst som fungerar som ett system för registrering och identifiering för företagets datakällor. Med andra ord hjälper Data Catalog människor att upptäcka, förstå och använda datakällor, och det hjälper organisationer att få mer värde från sina befintliga data. Det första steget för att göra en datakälla upptäckbar via Data Catalog är att registrera den datakällan.

## <a name="register-data-sources"></a>Registrera datakällor
Registrering är processen att extrahera metadata från datakällan och kopiera dessa data till datakatalogtjänsten. Informationen ligger kvar där den är och är under kontroll av det aktuella systemets administratörer och principer.

Så här registrerar du en datakälla:
1. Starta datakällregistreringsverktyget för datakatalogen i Azure Data Catalog-portalen. 
2. Logga in med ditt arbets- eller skolkonto med samma Azure Active Directory-autentiseringsuppgifter som du använder för att logga in på portalen.
3. Markera den datakälla som du vill registrera.

Mer steg-för-steg-information finns i självstudien [Komma igång med Azure Data Catalog.](data-catalog-get-started.md)

När du har registrerat datakällan spårar katalogen dess plats och indexerar dess metadata. Användare kan söka, bläddra i och upptäcka datakällan och sedan använda dess plats för att ansluta till den med hjälp av det program eller verktyg som de själva väljer.

## <a name="supported-data-sources"></a>Datakällor som stöds
En lista över datakällor som stöds finns i [Datakatalog DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturella metadata
När du registrerar en datakälla extraherar registreringsverktyget information om strukturen på de objekt du väljer. Den här informationen kallas strukturella metadata.

För alla objekt innehåller de här strukturella metadata objektets plats, så att användare som identifierar data kan använda den informationen för att ansluta till objektet i de klientverktyg de själva väljer. Andra strukturella metadata inkluderar objektnamn och typ samt attribut/kolumnnamn och datatyp.

## <a name="descriptive-metadata"></a>Beskrivande metadata
Förutom de grundläggande strukturella metadata som extraheras från datakällan extraherar registreringsverktyget för datakälla beskrivande metadata. För SQL Server Analysis Services och SQL Server Reporting Services hämtas dessa metadata från de beskrivningsegenskaper som exponeras av dessa tjänster. För SQL Server extraheras\_värden som tillhandahålls med hjälp av egenskapen ms description extended. För Oracle-databasen extraherar registreringsverktyget för datakällor\_kolumnen\_kommentar från vyn ALLA TABB-KOMMENTARER.

Förutom de beskrivande metadata som extraheras från datakällan kan användare ange beskrivande metadata med hjälp av registreringsverktyget för datakällan. Användare kan lägga till taggar och de kan identifiera experter för de objekt som registreras. Alla dessa beskrivande metadata kopieras till datakatalogtjänsten tillsammans med de strukturella metadata.

## <a name="include-previews"></a>Inkludera förhandsgranskningar
Som standard extraheras endast metadata från datakällor och kopieras till datakatalogtjänsten, men det blir ofta enklare att förstå en datakälla när du kan visa ett exempel på de data som den innehåller.

Genom att använda registreringsverktyget för datakatalogdatakäll kan du inkludera en förhandsgranskning av data i varje tabell och vy som är registrerad. Om du väljer att inkludera förhandsgranskningar under registreringen innehåller registreringsverktyget upp till 20 poster från varje tabell och vy. Den här ögonblicksbilden kopieras sedan till katalogen tillsammans med de strukturella och beskrivande metadata.

> [!NOTE]
> Breda tabeller med ett stort antal kolumner kan ha färre än 20 poster som ingår i förhandsgranskningen.
>
>

## <a name="include-data-profiles"></a>Inkludera dataprofiler
På samma sätt som exempel på förhandsgranskningar kan ge värdefulla sammanhang för användare som söker efter datakällor i Data catalog, inklusive en dataprofil, kan göra det lättare att förstå identifierade datakällor.

Genom att använda registreringsverktyget för datakatalogdatakäll kan du inkludera en dataprofil för varje tabell och vy som är registrerad. Om du väljer att inkludera en dataprofil under registreringen innehåller registreringsverktyget aggregerad statistik om data i varje tabell och vy, inklusive:

* Antalet rader och storleken på data i objektet.
* Datumet för den senaste uppdateringen av data och objektschemat.
* Antalet null-poster och distinkta värden för kolumner.
* Lägsta, högsta, genomsnittliga och standardavvikelsevärden för kolumner.

Denna statistik kopieras sedan till katalogen tillsammans med de strukturella och beskrivande metadata.

> [!NOTE]
> Text- och datumkolumner innehåller inte genomsnittlig eller standardavvikelsestatistik i sin dataprofil.
>
>

## <a name="update-registrations"></a>Uppdatera registreringar
Om du registrerar en datakälla kan den identifieras i Data catalog när du använder metadata och valfri förhandsgranskning som extraherats under registreringen. Om datakällan behöver uppdateras i katalogen (till exempel om schemat för ett objekt har ändrats, tabeller som ursprungligen uteslöts ska inkluderas eller om du vill uppdatera de data som ingår i förhandsgranskningarna) kan registreringsverktyget för datakällan köras igen.

Om du registrerar en redan registrerad datakälla utförs en "upsert"-åtgärd: befintliga objekt uppdateras och nya objekt skapas. Alla metadata som tillhandahålls av användare via datakatalogportalen behålls.

## <a name="summary"></a>Sammanfattning
Eftersom den kopierar strukturella och beskrivande metadata från en datakälla till katalogtjänsten gör registreringen av datakällan i Data Catalog datan lättare att identifiera och förstå. När du har registrerat datakällan kan du kommentera, hantera och upptäcka den med hjälp av datakatalogportalen.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du registrerar datakällor finns i självstudien [Komma igång med Azure Data Catalog.](data-catalog-get-started.md)
