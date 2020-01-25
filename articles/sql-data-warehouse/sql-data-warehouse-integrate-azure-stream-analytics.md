---
title: Använd Azure Stream Analytics
description: Tips för att använda Azure Stream Analytics med Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721208"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Använda Azure Stream Analytics med Azure Synapse Analytics
Azure Stream Analytics är en helt hanterad tjänst som tillhandahåller låg latens, skalbar komplex händelse behandling med hög tillgänglighet för strömning av data i molnet. Du kan lära dig grunderna genom [att läsa introduktion till Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md). Du kan sedan lära dig hur du skapar en lösning från slut punkt till slut punkt med Stream Analytics genom att följa själv studie kursen [komma igång med Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .

I den här artikeln får du lära dig hur du använder informations lager databasen som utgående mottagare för dina Stream Analytics jobb.

## <a name="prerequisites"></a>Krav
Börja med att köra följande steg i själv studie kursen [komma igång med Azure Stream Analytics](../stream-analytics/stream-analytics-real-time-fraud-detection.md) .  

1. Skapa en Event Hub-inmatare
2. Konfigurera och starta Event Generator-programmet
3. Etablera ett Stream Analytics jobb
4. Ange inmatade jobb och fråga

Skapa sedan en Azure SQL Data Warehouse-databas

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Ange utdata för jobb: Azure SQL Data Warehouse databas
### <a name="step-1"></a>Steg 1
I ditt Stream Analytics jobb klickar du på **utdata** överst på sidan och sedan på **Lägg till**.

### <a name="step-2"></a>Steg 2
Välj SQL Database.

### <a name="step-3"></a>Steg 3
Ange följande värden på nästa sida:

* *Alias för utdata*: Ange ett eget namn för jobbets utdata.
* *Prenumeration*:
  * Om din SQL Data Warehouse databas finns i samma prenumeration som Stream Analyticss jobbet väljer du Använd SQL Database från den aktuella prenumerationen.
  * Om din databas finns i en annan prenumeration väljer du Använd SQL Database från en annan prenumeration.
* *Databas*: Ange namnet på en mål databas.
* *Server namn*: Ange Server namnet för den databas som du nyss angav. Du kan använda Azure Portal för att hitta detta.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *Användar namn*: Ange användar namnet för ett konto som har Skriv behörighet för databasen.
* *Lösen ord*: Ange lösen ordet för det angivna användar kontot.
* *Tabell*: Ange namnet på mål tabellen i databasen.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>Steg 4
Klicka på knappen kontrol lera om du vill lägga till jobbets utdata och kontrol lera att Stream Analytics kan ansluta till databasen.

När anslutningen till databasen lyckas visas ett meddelande i portalen. Du kan klicka på testa för att testa anslutningen till databasen.

## <a name="next-steps"></a>Nästa steg
En översikt över integrering finns i [integrera andra tjänster](sql-data-warehouse-overview-integrate.md).
Mer utvecklings tips finns i [design beslut och kod metoder för informations lager](sql-data-warehouse-overview-develop.md).

