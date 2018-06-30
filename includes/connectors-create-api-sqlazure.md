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
ms.openlocfilehash: 4ffda692da0ab7b63f7376c36dfab0bec914e334
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138073"
---
* Om du använder Azure SQL Database, följer du stegen under [Anslut till Azure SQL Database](#connect-azure-sql-db). 

* Om du använder SQL Server, följer du stegen under [Anslut till SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Anslut till Azure SQL Database

1. När SQL-utlösare eller åtgärd efterfrågar anslutningsinformation, gör du följande:

   1. Skapa ett namn för anslutningen.

   2. Välj den SQL-servern och välj sedan databasen. 

      Databaslistan visas endast när du väljer SQLServer.
 
   3. Ange ditt användarnamn och lösenord för servern.

      Du hittar den här informationen i Azure portal under Egenskaper för SQL-databas eller i anslutningssträngen: 
      
      ”Användar-ID = <*användarnamn*>”
      <br>
      ”Lösenordet = <*yourPassword*>”

   Det här exemplet visar anslutningsinformationen för en utlösare, men de här stegen fungerar för åtgärder för.

   ![Skapa Azure SQL Database-anslutning](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Asterisker (*) anger nödvändiga värden.

   | Egenskap  | Värde | Information | 
   |----------|-------|---------| 
   | Anslutningsnamn | <*Mina sql-anslutning*> | Namn för din anslutning | 
   | SQL Server-namn | <*Min sql server*> | Namnet på SQLServer |
   | SQL Database-namn | <*Mina sql database*>  | Namnet på din SQL-databas | 
   | Användarnamn | <*Mina sql användarnamn*> | Användarnamnet för åtkomst till databasen |
   | Lösenord | <*Mina sql lösenord*> | Lösenord för åtkomst till databasen | 
   |||| 

2. När du är klar väljer du **Skapa**.

3. När du skapar anslutningen fortsätta med [lägga till SQL-utlösare](#add-sql-trigger) eller [lägga till SQL-åtgärd](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ansluta till SQL Server

Innan du kan välja din gateway, kontrollerar du att du redan [ställa in din datagateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). På så sätt kan din gateway som visas i listan gateways när du skapar din anslutning.

1. När SQL-utlösare eller åtgärd efterfrågar anslutningsinformation, gör du följande:

   1. Markera i utlösare eller åtgärd **Anslut via lokala datagateway** så att SQL server-alternativ visas.

   2. Skapa ett namn för anslutningen.

   3. Ange adressen för SQLServer och ange namn för din databas.
   
      Du hittar den här informationen i anslutningssträngen: 
      
      * ”Server = <*yourServerAddress*>”
      * ”Databas = <*yourDatabaseName*>”

   4. Ange ditt användarnamn och lösenord för servern.

      Du hittar den här informationen i anslutningssträngen: 
      
      * ”Användar-ID = <*användarnamn*>”
      * ”Lösenordet = <*yourPassword*>”

   5. Om din SQL-servern använder Windows eller grundläggande autentisering, Välj autentiseringstyp.

   6. Välj namnet på din lokala datagateway som du skapade tidigare.
   
      Om din gateway inte visas i listan, kontrollera att du korrekt [ställa in din gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Det här exemplet visar anslutningsinformationen för en utlösare, men de här stegen fungerar för åtgärder för.

   ![Skapa SQL Server-anslutning](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Asterisker (*) anger nödvändiga värden.

   | Egenskap  | Värde | Information | 
   |----------|-------|---------| 
   | Anslut via lokala gateway | Välj det här alternativet först för SQL Server-inställningar. | | 
   | Anslutningsnamn | <*Mina sql-anslutning*> | Namn för din anslutning | 
   | SQL Server-namn | <*Min sql server*> | Namnet på SQLServer |
   | SQL Database-namn | <*Mina sql database*>  | Namnet på din SQL-databas |
   | Användarnamn | <*Mina sql användarnamn*> | Användarnamnet för åtkomst till databasen |
   | Lösenord | <*Mina sql lösenord*> | Lösenord för åtkomst till databasen | 
   | Autentiseringstyp | Windows- eller Basic | Valfritt: Autentiseringstypen används av SQLServer | 
   | Gateways | <*Min data gateway*> | Namnet på din lokala datagateway | 
   |||| 

2. När du är klar väljer du **Skapa**. 

3. När du skapar anslutningen fortsätta med [lägga till SQL-utlösare](#add-sql-trigger) eller [lägga till SQL-åtgärd](#add-sql-action).
