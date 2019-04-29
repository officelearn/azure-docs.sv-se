---
title: ta med fil
description: ta med fil
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541151"
---
* Om du använder Azure SQL Database, följer du anvisningarna under [Anslut till Azure SQL Database](#connect-azure-sql-db). 

* Om du använder SQL Server, följer du anvisningarna under [Anslut till SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Ansluta till Azure SQL Database

1. När SQL-utlösaren eller åtgärden efterfrågar anslutningsinformation, Följ dessa steg:

   1. Skapa ett namn för anslutningen.

   2. Välj din SQLServer och välj sedan databasen. 

      Databaslistan visas bara när du har valt SQL-servern.
 
   3. Ange ditt användarnamn och lösenord för din server.

      Du hittar den här informationen i Azure-portalen under Egenskaper för SQL-databas eller i anslutningssträngen: 
      
      ”Användar-ID = <*användarnamn*>”
      <br>
      "Password=<*yourPassword*>"

   Det här exemplet visar anslutningsinformationen för en utlösare, men de här stegen fungerar även för åtgärder.

   ![Skapa Azure SQL Database-anslutning](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Asterisker (*) anger nödvändiga värden.

   | Egenskap  | Värde | Information | 
   |----------|-------|---------| 
   | Anslutningsnamn | <*my-sql-connection*> | Namn för anslutningen | 
   | SQL Server-namn | <*my-sql-server*> | Namn för din SQL-server |
   | SQL Database-namn | <*my-sql-database*>  | Namn för din SQL-databas | 
   | Användarnamn | <*my-sql-username*> | Användarnamnet för åtkomst till din databas |
   | Lösenord | <*my-sql-password*> | Lösenord för att komma åt din databas | 
   |||| 

2. När du är klar väljer du **Skapa**.

3. När du skapar anslutningen, fortsätter du med [Lägg till SQL-utlösare](#add-sql-trigger) eller [Lägg till SQL-åtgärd](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ansluta till SQL Server

Innan du kan välja din gateway, kontrollerar du att du redan [ställa in din datagateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). På så sätt kan din gateway visas i listan över gatewayer när du skapar din anslutning.

1. När SQL-utlösaren eller åtgärden efterfrågar anslutningsinformation, Följ dessa steg:

   1. I utlösaren eller åtgärden Välj **Anslut via lokal datagateway** så att SQL server-alternativ visas.

   2. Skapa ett namn för anslutningen.

   3. Anger du adressen för din SQLServer, och ange sedan namnet på din databas.
   
      Du hittar den här informationen i anslutningssträngen: 
      
      * "Server=<*yourServerAddress*>"
      * "Database=<*yourDatabaseName*>"

   4. Ange ditt användarnamn och lösenord för din server.

      Du hittar den här informationen i anslutningssträngen: 
      
      * ”Användar-ID = <*användarnamn*>”
      * "Password=<*yourPassword*>"

   5. Om din SQLServer använder Windows eller grundläggande autentisering, väljer du autentiseringstypen.

   6. Välj namnet på din lokala datagateway som du skapade tidigare.
   
      Om din gateway inte visas i listan, kontrollerar du att du korrekt [ställa in din gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Det här exemplet visar anslutningsinformationen för en utlösare, men de här stegen fungerar även för åtgärder.

   ![Skapa SQL Server-anslutning](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Asterisker (*) anger nödvändiga värden.

   | Egenskap  | Värde | Information | 
   |----------|-------|---------| 
   | Ansluta via en lokal gateway | Välj det här alternativet först inställningar för SQL Server. | | 
   | Anslutningsnamn | <*my-sql-connection*> | Namn för anslutningen | 
   | SQL Server-namn | <*my-sql-server*> | Namn för din SQL-server |
   | SQL Database-namn | <*my-sql-database*>  | Namn för din SQL-databas |
   | Användarnamn | <*my-sql-username*> | Användarnamnet för åtkomst till din databas |
   | Lösenord | <*my-sql-password*> | Lösenord för att komma åt din databas | 
   | Autentiseringstyp | Windows- eller Basic | Valfritt: Den autentiseringstyp som används av SQLServer | 
   | Gateways | <*my-data-gateway*> | Namnet på din lokala datagateway | 
   |||| 

2. När du är klar väljer du **Skapa**. 

3. När du skapar anslutningen, fortsätter du med [Lägg till SQL-utlösare](#add-sql-trigger) eller [Lägg till SQL-åtgärd](#add-sql-action).
