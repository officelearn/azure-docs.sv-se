---
title: "SQL Data Warehouse-klientversioner har stöd för granskning av data | Microsoft Docs"
description: "Lär dig mer om SQL Data Warehouse äldre klienter stöd för granskning av data"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: dfe29ff3-dfeb-4309-83c0-c1a300f4f44e
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: a7ea6141285a0098339f1e071af2592dd4535c12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sql-data-warehouse----downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>SQL Data Warehouse - klientversioner stöd för granskning och dynamisk Datamaskning
[Granskning](sql-data-warehouse-auditing-overview.md) fungerar med SQL-klienter som stöder TDS-omdirigering.

Alla klienter som implementerar TDS 7.4 bör också stöd för omdirigering. Undantag till detta inkluderar JDBC 4.0 där funktionen för omdirigering av stöds inte fullt ut och Tedious för Node.JS i vilka omdirigering har inte implementerats.

För ”äldre klienter”, ska d.v.s. vilka stöd TDS 7.3 och nedanför - serverns FQDN i anslutningen versionssträng ändras:

Ursprungliga serverns FQDN i anslutningssträngen: <*servernamn*>. database.windows.net

Ändrade serverns FQDN i anslutningssträngen: <*servernamn*> .database. **säker**. windows.net

Innehåller en lista över ”klientversioner”:

* .NET 4.0 och nedan.
* ODBC-10.0 och nedan.
* JDBC (medan JDBC stöder TDS 7.4, funktion för TDS-omdirigering fullständigt stöds inte)
* Tråkigt (för Node.JS)

**Kommentar:** servern ovan FDQN ändring kan vara användbara också för att tillämpa en princip för SQL Server-nivå granskning utan behov av en konfiguration steg i varje databas (tillfällig lösning).     

