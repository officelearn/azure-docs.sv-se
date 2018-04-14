---
title: 'Migrera: Datalager migrationsverktyget | Microsoft Docs'
description: Migrera till SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: ''
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: e8a8a84153a950f2d1bc002b34c83dc5ed8a5eb8
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2018
---
# <a name="data-warehouse-migration-utility-preview"></a>Verktyg för migrering av data Warehouse (förhandsgranskning)
> [!div class="op_single_selector"]
> * [Hämta verktyg för migrering][Download Migration Utility]
> 
> 

Data Warehouse Migreringsverktyget är ett verktyg som utformats för att migrera schema och data från SQL Server och Azure SQL Database till Azure SQL Data Warehouse. Under migreringen av schemat mappas verktyget automatiskt motsvarande schemat från källan till målet. När schemat har migrerats, verktyg ger möjlighet att flytta data med automatiskt genererade skript.

Förutom schemat och datamigrering kan det här verktyget du välja att generera kompatibilitetsrapporter som sammanfattar inkompatibilitet mellan mål- och instanser som skulle kunna hindra effektiviserad migrering.

## <a name="get-started"></a>Kom igång
En förutsättning för installationen måste kommandoradsverktyget BCP att köra migreringsskript och Office för att visa kompatibilitetsrapporten. När du startar den körbara filen som har hämtats uppmanas du att acceptera en standard EULA innan verktyget kommer att installeras.

Om du vill köra Utiliy för migreringen, kommer du behöver det följande behörigheter på databasen som du vill migrera: CREATE DATABASE, ALTER ANY DATABASE eller ANY VYDEFINITIONEN.

### <a name="launching-the-tool-and-connecting"></a>Starta verktyget och ansluta
Starta verktyget genom att klicka på ikonen på skrivbordet som visas efter installation. När verktyget öppnas, uppmanas du att med en initial anslutning sida där du kan välja källan och målet för Migreringsverktyget. För närvarande kan stöder vi SQL Server och Azure SQL Database som källor och SQL Data Warehouse som mål. När du har valt detta blir du ombedd att ansluta till källservern genom att fylla i servernamnet och autentisering och sedan klicka på ”Anslut”.

Efter autentisering, visas en lista över databaser som finns på den server som du är ansluten till. Du kan börja migreringen genom att välja en databas som du vill migrera och sedan klicka på 'Migrera valt'.

## <a name="migration-report"></a>Rapporten migrering
Att välja ”Kontrollera databasen kompatibilitet” i verktyget genererar en rapport som sammanfattar objektinkompatibiliteter för alla i databasen du begärt för att migrera. En bredare lista över SQL Server-funktioner som inte finns i SQL Data Warehouse finns i vår [Migreringsdokumentation][migration documentation]. När rapporten har skapats kommer du att kunna spara och öppna rapporten i Excel.

Observera att när migreringen schemat, de flesta problem som har identifierats som 'Object' kommer att justeras för att tillåta att omedelbar datamigreringen. Granska ändringar för att säkerställa att du inte vill göra ytterligare anpassningar innan du tillämpar schemat.

## <a name="migrate-schema"></a>Migrera schema
Efter anslutning genererar välja migrera schemat ett skript för migrering av schemat för de markerade tabellerna. Det här skriptet portar strukturen i tabellen, maps inkompatibla data typer till fler kompatibla formulär och skapar säkerhetsreferenser och schema om detta anges av användaren i migreringsinställningarna för. Den här koden kan köras mot den avsedda SQL Data Warehouse-databasinstansen, sparas i en fil, kopieras till Urklipp eller redigeras även i rad innan du vidtar åtgärder.  

Som beskrivs ovan, när du migrerar schemat granska migreringen ändringar som verktyget har gjort för att säkerställa att att du förstår dem.  

## <a name="migrate-data"></a>Migrera data
Genom att klicka på alternativet Migrera Data kan du generera BCP-skript som flyttar data först till flat-filer på servern, och sedan direkt till ditt SQL Data Warehouse. Vi rekommenderar den här processen för att flytta små mängder data, och som nya försök inte är inbyggda och fel kan uppstå om det finns en förlust av nätverksanslutningen. För att kunna köra detta behöver du ha kommandoradsverktyget BCP installerat och att schemat innehåller data måste redan ha skapats.

När du har fyllt i parametrarna ovan behöver du helt enkelt klicka på Kör migrering och en uppsättning två paket kommer att genereras till den angivna platsen. Kör export-filen för att exportera data från din källa för migrering till flat-filer och kör importfilen för att importera data till SQL Data Warehouse.

## <a name="next-steps"></a>Nästa steg
Nu när du har migrerat data, kolla hur man [utveckla][develop].

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://www.microsoft.com/en-us/download/details.aspx?id=49100
