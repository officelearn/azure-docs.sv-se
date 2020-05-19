---
title: Spåra data ändringar i Azure SQL Edge (för hands version)
description: Lär dig mer om ändrings spårning och registrering av ändrings data i Azure SQL Edge (för hands version)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597270"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>Spåra data ändringar i Azure SQL Edge (för hands version)

Azure SQL Edge stöder de två SQL Server funktioner som spårar ändringar av data i en databas:[ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) och [registrering av ändrings data](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture). Dessa funktioner gör det möjligt för program att fastställa DML-ändringar (Insert-, Update-och Delete-åtgärder) som har gjorts till användar tabeller i en databas. Registrering av ändrings data och ändrings spårning kan aktive ras på samma databas. Inga särskilda överväganden krävs.

Möjligheten att fråga efter data som har ändrats i en databas är ett viktigt krav för att vissa program ska vara effektiva. För att fastställa data ändringar måste programutvecklare implementera en anpassad spårnings metod i sina program med hjälp av en kombination av utlösare, tidsstämpel-kolumner och ytterligare tabeller. Att skapa dessa program omfattar vanligt vis mycket arbete att implementera, leder till schema uppdateringar och har ofta en hög prestanda. I händelse av IoT-lösning, där det finns behov av att regelbundet flytta data från gränsen till ett moln eller ett Data Center, kan ändrings spårning vara mycket användbart. Detta kan användas för att snabbt och effektivt fråga endast delta ändringar från den senaste synkroniseringen och överföra ändringarna till molnet eller data Center målet. För ytterligare information om fördelarna med att använda Ändringsspårning och registrering av ändrings data, se [fördelarna med att använda registrering av ändrings data eller ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Om du vill förstå funktions skillnaderna mellan Ändringsspårning och registrering av ändrings data, kan du se [funktions skillnader mellan avbildning av ändrings data och ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Sammanställning av ändringsdata

Information om hur registrering av ändrings data fungerar finns i [registrering av ändrings data](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

Information om hur du aktiverar eller inaktiverar registrering av ändrings data finns i [Aktivera och inaktivera registrering av ändrings data](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)

Information om hur du administrerar och övervakar registrering av ändrings data finns i [administrera och övervaka registrering av ändrings data](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

Information om hur du frågar och arbetar med ändrade data finns i [arbeta med ändrings data](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)

## <a name="change-tracking"></a>Spårning av ändringar

Information om hur Ändringsspårning fungerar finns i [ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server).

Information om hur du aktiverar eller inaktiverar Ändringsspårning finns i [Aktivera och inaktivera ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

Information om hur du administrerar, övervakar och hanterar Ändringsspårning finns i [administrera och övervaka ändringsspårning](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

Information om hur du frågar och arbetar med ändrade data finns i [arbeta med ändrings data](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>Temporala tabeller

Förutom stöd Ändringsspårning och ändra data insamlings funktionerna i SQL Server stöder Azure SQL Edge också funktionen temporala tabeller i SQL Server. Temporala tabeller (även kallade system versions temporala tabeller) som en databas funktion som ger inbyggt stöd för att tillhandahålla information om data som lagras i tabellen vid en viss tidpunkt snarare än bara de data som är korrekta vid aktuell tidpunkt.

En temporal system versions tabell är en typ av användar tabell som är utformad för att bevara en fullständig historik över data ändringar och tillåta enkel tidpunkts analys. Den här typen av temporal tabell kallas en temporal system versions tabell, eftersom giltighets perioden för varje rad hanteras av systemet (det vill säga databas motorn).

Varje temporal tabell har två explicit definierade kolumner, var och en med data typen datetime2. Dessa kolumner kallas för period kolumner. Dessa period kolumner används exklusivt av systemet för att registrera giltighets perioden för varje rad när en rad ändras.

Förutom dessa period kolumner innehåller en temporal tabell även en referens till en annan tabell med ett speglat schema. Systemet använder den här tabellen för att automatiskt lagra den tidigare versionen av raden varje gången en rad i den temporala tabellen uppdateras eller tas bort. Denna ytterligare tabell kallas historik tabellen, medan huvud tabellen som lagrar aktuella (faktiska) rad versioner kallas den aktuella tabellen eller helt enkelt som den temporala tabellen. Under skapandet av temporala tabeller kan användare ange befintlig historik tabell (måste vara schema kompatibelt) eller låta systemet skapa standard historik tabell.

Mer information om temporala tabeller finns i [temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)

## <a name="see-also"></a>Se även

- [Data strömning i Azure SQL Edge (för hands version)](stream-data.md)
- [Machine Learning och AI med ONNX i Azure SQL Edge (för hands version)](onnx-overview.md)
- [Konfigurera replikering till Azure SQL Edge (för hands version)](configure-replication.md)
- [Säkerhetskopiera och återställa databaser i Azure SQL Edge (för hands version)](backup-restore.md)



