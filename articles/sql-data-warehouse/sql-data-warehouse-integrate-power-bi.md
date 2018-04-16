---
title: "Använda Powerbi med SQL Data Warehouse | Microsoft Docs"
description: "Tips för att använda Power BI med Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: 4ea9a2ff0c95a73b348d3b48e9e62957d5cce31c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="use-power-bi-with-sql-data-warehouse"></a>Använda Powerbi med SQL Data Warehouse
Som med Azure SQL Database tillåter SQL Data Warehouse Direct Connect användare att utnyttja kraftfulla logiska pushdown tillsammans med analytiska funktionerna i Power BI.  Med Direct Connect skickas frågor tillbaka till din Azure SQL Data Warehouse i realtid att utforska data.  Detta kan kombineras med skalan för SQL Data Warehouse, kan användare skapa dynamiska rapporter i minuter mot terabyte data.  Dessutom tillåter introduktionen av funktionen Öppna i Power BI-knappen användare att ansluta Power BI direkt till deras SQL Data Warehouse utan att samla in information från andra delar av Azure.

När du använder Direct Connect du Observera:

* Ange det fullständigt kvalificerade servernamnet vid anslutning (se nedan för mer information)
* Se till att brandväggsreglerna för databasen är konfigurerade för ”Tillåt åtkomst till Azure-tjänster.
* Varje åtgärd som att markera en kolumn eller lägger till ett filter kommer direkt söka i datalagret
* Paneler uppdateras ungefär var 15 minut (uppdatera inte behöver schemaläggas)
* Frågor och svar är inte tillgängliga för datauppsättningar som Direct Connect
* Schemaändringar har inte plockats automatiskt
* Alla frågor med Direct Connect gör timeout efter två minuter

Dessa begränsningar och anteckningar kan ändras när vi fortsätta att förbättra upplevelser. Nedan beskrivs stegen för att ansluta.  

## <a name="using-the-open-in-power-bi-button"></a>Med knappen ”Öppna i Power BI-
Det enklaste sättet att flytta mellan din SQL Data Warehouse och Power BI är öppna i Power BI-knappen. Den här knappen kan du sömlöst börjar skapa nya instrumentpaneler i Power BI.  

1. Du kommer igång går du till din SQL Data Warehouse-instans i Azure-portalen.
2. Klicka på knappen Öppna i Power BI.
3. Om vi kan inte logga in dig direkt, eller om du inte har en Power BI-konto behöver du logga in.  
4. Du kommer till sidan SQL Data Warehouse-anslutning med information från ditt SQL Data Warehouse ifylld.
5. När du har angett dina autentiseringsuppgifter ansluts du fullständigt till SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>Ansluta Power BI-portalen
Förutom att använda funktionen Öppna i Power BI-knappen kan ansluta användarna också till sina SQL Data Warehouse på Power BI-portalen.

1. Klicka på ”Hämta uppgifter” längst ned i navigeringsfönstret.
2. Välj 'Databaser'.
3. Markera en gång ”Azure SQL Data Warehouse' på sidan databaser och klicka på” Anslut ”.
4. Ange nödvändig anslutningsinformation.  Din servernamnet och databasnamnet kan hittas i Azure Portal.
5. Du kommer tillbaka till huvudsidan för Power BI och när anslutningen görs en ny post under 'Datauppsättningar' visas med namnet på din instans.  
6. Du kan klicka på ny datauppsättning och utforska alla tabeller och vyer i databasen. Att markera en kolumn kommer att skicka en fråga till källan, att dynamiskt skapa din visual. Dessa visuell information kan sparas i en ny rapport och tillbaka fäst på instrumentpanelen.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
