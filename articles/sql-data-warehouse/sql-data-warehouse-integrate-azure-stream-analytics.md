---
title: Använd Azure Stream Analytics med SQL Data Warehouse | Microsoft Docs
description: Tips för att använda Azure Stream Analytics med Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
origin.date: 03/22/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 3a260102e9964cfc248ed3dbabab487bd1c5dfc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474342"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Använd Azure Stream Analytics med SQL Data Warehouse
Azure Stream Analytics är en helt hanterad tjänst som ger låg latens, hög tillgänglighet och skalbar komplex händelsebearbetning över strömmande data i molnet. Du kan lära dig grunderna genom att läsa [introduktion till Azure Stream Analytics][Introduction to Azure Stream Analytics]. Sedan kan du lära dig hur du skapar en slutpunkt till slutpunkt-lösning med Stream Analytics genom att följa den [komma igång med Azure Stream Analytics] [ Get started using Azure Stream Analytics] självstudien.

I den här artikeln får du lära dig hur du använder din Azure SQL Data Warehouse-databas som kanalmottagare för Stream Analytics-jobb.

## <a name="prerequisites"></a>Nödvändiga komponenter
Först gå igenom följande steg i den [komma igång med Azure Stream Analytics] [ Get started using Azure Stream Analytics] självstudien.  

1. Skapa en Event Hub
2. Konfigurera och starta händelsegeneratorprogrammet
3. Etablera ett Stream Analytics-jobb
4. Ange jobbindata och fråga

Skapa sedan en Azure SQL Data Warehouse-databas

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Ange jobbutdata: Azure SQL Data Warehouse-databas
### <a name="step-1"></a>Steg 1
I ditt Stream Analytics-jobb klickar du på **utdata** högst upp på sidan och klicka sedan på **lägga till**.

### <a name="step-2"></a>Steg 2
Välj SQL-databas.

### <a name="step-3"></a>Steg 3
Ange följande värden på nästa sida:

* *Utdata Alias*: Ange ett eget namn för den här jobbutdata.
* *Prenumeration*:
  * Om SQL Data Warehouse-databasen är i samma prenumeration som Stream Analytics-jobb väljer du Använd SQL-databas från aktuella prenumerationen.
  * Om databasen är i en annan prenumeration väljer du Använd SQL-databas från en annan prenumeration.
* *Databasen*: Ange namnet på en måldatabas.
* *Servernamn*: Ange namnet på servern för databasen som du precis angav. Du kan använda Azure-portalen för att hitta detta.

![][server-name]

* *Användarnamn*: Ange användarnamnet för ett konto som har skrivbehörighet för databasen.
* *Lösenord*: Ange lösenordet för det angivna användarkontot.
* *tabellen*: Ange namnet på måltabellen i databasen.

![][add-database]

### <a name="step-4"></a>Steg 4
Klicka på knappen Kontrollera för att lägga till den här jobbutdata och kontrollera att Stream Analytics kan ansluta till databasen.

När anslutningen till databasen lyckas visas ett meddelande i portalen. Du kan klicka på Testa för att testa anslutningen till databasen.

## <a name="next-steps"></a>Nästa steg
En översikt över integration finns i [översikt över SQL Data Warehouse-integration][SQL Data Warehouse integration overview].

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
[Azure Stream Analytics documentation]: /stream-analytics/

<!-- Update_Description: update meta properties, wording update -->