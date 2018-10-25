---
title: Ansluta Qlik Sense till Azure Cosmos DB och visualisera dina data | Microsoft Docs
description: Den här artikeln beskriver de steg som krävs för att ansluta Azure Cosmos DB till Qlik Sense och visualisera dina data.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: sngun
ms.openlocfilehash: e804ddec5f7aebf58352e87e483bf2bdeca0fce0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989237"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Ansluta Qlik Sense till Azure Cosmos DB och visualisera dina data

Qlik Sense är ett verktyg för visualisering av data som kombinerar data från olika källor till en enda vy. Qlik Sense indexerar alla möjliga relation i dina data så att du kan få omedelbar information till data. Du kan visualisera data i Azure Cosmos DB med hjälp av Qlik Sense. Den här artikeln beskriver de steg som krävs för att ansluta Azure Cosmos DB till Qlik Sense och visualisera dina data. 

> [!NOTE]
> Ansluta Azure Cosmos DB till Qlik Sense stöds för närvarande för Azure Cosmos DB SQL API-konton.

Du kan ansluta Qlik Sense till Azure Cosmos DB med:

* Cosmos DB SQL API med hjälp av ODBC-anslutningsprogram.

* Cosmos DB MongoDB API med hjälp av Qlik Sense MongoDB-anslutningen (för närvarande i förhandsversion).

* Cosmos DB MongoDB API och SQL-API med hjälp av REST API-kopplingen i Qlik Sense.

* Cosmos DB Mongo DB API med hjälp av gRPC-anslutningen för Qlik Core.
Den här artikeln beskriver vad för att ansluta till Cosmos DB SQL API med hjälp av ODBC-anslutningsprogram.

Den här artikeln beskriver vad för att ansluta till Cosmos DB SQL API med hjälp av ODBC-anslutningsprogram.

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande resurser som är redo innan du följer anvisningarna i den här artikeln:

* Ladda ned den [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) eller ställa in Qlik Sense i Azure genom [installerar Qlik Sense marketplace-objekt](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Ladda ned den [videospel data](https://www.kaggle.com/gregorut/videogamesales), dessa exempeldata är CSV-format. Du kan lagra dessa data i en Cosmos DB-konto och visualisera den i Qlik Sense.

* Skapa ett Azure Cosmos DB SQL API-konto med hjälp av stegen som beskrivs i [skapa ett konto](create-sql-api-dotnet.md#create-a-database-account) avsnittet i snabbstartsartikeln.

* [Skapa en databas och en samling](create-sql-api-dotnet.md#add-a-collection) – du kan använda värdet samling dataflöde till 1000 RU/s. 

* Läsa in videospel försäljning exempeldata till ditt Cosmos DB-konto. Du kan importera data med hjälp av Migreringsverktyget för Azure Cosmos DB-data, kan du göra en [sekventiella](import-data.md#SQLSeqTarget) eller en [massimport](import-data.md#SQLBulkTarget) av data. Det tar cirka 3 – 5 minuter för data som ska importeras till Cosmos DB-konto.

* Hämta, installera och konfigurera ODBC-drivrutinen med hjälp av stegen i den [Anslut till Cosmos DB med ODBC-drivrutin](odbc-driver.md) artikeln. Videospel-data är en enkel uppsättning data och du behöver inte redigera schemat, Använd bara standardschemat för mappning av samlingen.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Anslut Qlik Sense till Cosmos DB

1. Öppna Qlik Sense och välj **skapar en ny app**. Ange ett namn för din app och välj **skapa**.

   ![Skapa en ny Qlik Sense-app](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. När den nya appen har skapats väljer **öppna appen** och välj **lägga till data från filer och andra källor**. 

3. Datakällor, Välj **ODBC** att öppna fönstret för ny anslutning. 

4. Växla till **användar-DSN** och välj den ODBC-anslutning som du skapade tidigare. Ange ett namn för anslutningen och välj **skapa**. 

   ![Skapa en ny anslutning](./media/visualize-qlik-sense/create-new-connection.png)

5. När du har skapat anslutningen kan du förhandsgranska den väljer du databasen, samling där videospel data finns.

   ![Välj den databasen och samlingen](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Sedan väljer du **lägga till data** att läsa in data till Qlik Sense. När du läser in data till Qlik Sense, kan du generera insikter och analysera data. Du kan använda insikterna, eller så kan du bygga din egen app utforska videospel försäljning. Följande bild visar 

   ![Visualisera data](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Begränsningar när du ansluter med ODBC 

Cosmos DB är en distribuerad databas utan schema med drivrutiner modelleras runt developer behov. ODBC-drivrutinen kräver en databas med schemat skulle härleda kolumner, datatyperna och andra egenskaper. Den vanliga SQL-frågan eller DML-syntaxen med relationella funktioner gäller inte för Cosmos DB SQL API eftersom SQL API: T inte är ANSI SQL. SQL-uttryck som utfärdats via ODBC-drivrutinen har översatts till Cosmos DB specifika SQL-syntax som inte har kopplat alla konstruktioner på grund av detta skäl. Om du vill förhindra att problemen översättning, måste du aktivera ett schema när du konfigurerar en ODBC-anslutning. Den [ansluta med ODBC-drivrutin](odbc-driver.md) artikeln får du förslag och metoder för att konfigurera schemat. Se till att skapa den här mappningen för varje databas/samling i Cosmos DB-kontot.

## <a name="next-steps"></a>Nästa steg

Om du använder ett annat visualiseringsverktyg, till exempel Power BI kan ansluta du till den med hjälp av anvisningarna i följande dokument:

* [Visualisera data med Cosmos DB med hjälp av anslutningsprogrammet för Power BI](powerbi-visualize.md)
