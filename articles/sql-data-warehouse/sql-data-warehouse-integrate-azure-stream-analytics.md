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
ms.openlocfilehash: 63803f3ac477e48d8d1c14a72e2ee9b9d4860047
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685731"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Använda Azure Stream Analytics med SQL Data Warehouse
Azure Stream Analytics är en helt hanterad tjänst som tillhandahåller låg latens, skalbar komplex händelse behandling med hög tillgänglighet för strömning av data i molnet. Du kan lära dig grunderna genom [att läsa introduktion till Azure Stream Analytics][Introduction to Azure Stream Analytics]. Du kan sedan lära dig hur du skapar en lösning från slut punkt till slut punkt med Stream Analytics genom att följa själv studie kursen [komma igång med Azure Stream Analytics][Get started using Azure Stream Analytics] .

I den här artikeln får du lära dig hur du använder Azure SQL Data Warehouse-databasen som utgående mottagare för dina Stream Analytics jobb.

## <a name="prerequisites"></a>Nödvändiga komponenter
Börja med att köra följande steg i själv studie kursen [komma igång med Azure Stream Analytics][Get started using Azure Stream Analytics] .  

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

![][server-name]

* *Användar namn*: Ange användar namnet för ett konto som har Skriv behörighet för databasen.
* *Lösen ord*: Ange lösen ordet för det angivna användar kontot.
* *Tabell*: Ange namnet på mål tabellen i databasen.

![][add-database]

### <a name="step-4"></a>Steg 4
Klicka på knappen kontrol lera om du vill lägga till jobbets utdata och kontrol lera att Stream Analytics kan ansluta till databasen.

När anslutningen till databasen lyckas visas ett meddelande i portalen. Du kan klicka på testa för att testa anslutningen till databasen.

## <a name="next-steps"></a>Nästa steg
En översikt över integration finns i [Översikt över SQL Data Warehouse-integrering][SQL Data Warehouse integration overview].

För fler utvecklingstips, se [Översikt över SQL Data Warehouse-utveckling][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
