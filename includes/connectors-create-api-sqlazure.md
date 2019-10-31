---
title: ta med fil
description: ta med fil
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: d60d7727e0674298fa6da7e7330221318da23efd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161667"
---
* Om du använder Azure SQL Database följer du stegen under [Anslut till Azure SQL Database](#connect-azure-sql-db).

* Om du använder SQL Server följer du stegen under [Anslut till SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Anslut till Azure SQL Database

När SQL-utlösaren eller åtgärden efterfrågar anslutnings information följer du dessa steg, som fungerar för både utlösare och åtgärder.

1. För **anslutnings namn**skapar du ett namn för anslutningen.

1. Under **SQL Server namn**väljer du din Azure SQL-Server. När listan **SQL Database namn** visas väljer du din databas. Ange användar namn och lösen ord för din Azure SQL-Server.

   Du kan också hitta den här informationen antingen i Azure Portal under egenskaper för SQL-databasen eller i anslutnings strängen:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Skapa anslutning till Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. När du är klar väljer du **Skapa**.

1. När du har skapat anslutningen fortsätter du med att [lägga till en SQL-utlösare](#add-sql-trigger) eller [lägga till en SQL-åtgärd](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Ansluta till SQL Server

När SQL-utlösaren eller åtgärden efterfrågar anslutnings information följer du dessa steg, som fungerar för både utlösare och åtgärder. Innan du börjar måste du dock se till att du redan har [konfigurerat din lokala datagateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Annars visas inte gatewayen i listan med gatewayer när du skapar anslutningen.

1. För **anslutnings namn**skapar du ett namn för anslutningen.

1. I utlösaren eller åtgärden väljer du **Anslut via lokal datagateway** så att SQL Server-alternativen visas.

1. För **SQL Server-namn** och **SQL Database-namn**anger du adressen till din SQL Server och ditt namn för din databas. För **användar** namn och **lösen ord**anger du användar namn och lösen ord för servern.

   Du kan också hitta den här informationen i anslutnings strängen:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Skapa anslutning till SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Om SQL-servern använder Windows eller grundläggande autentisering väljer du **autentiseringstypen**.

1. Under **gatewayer**väljer du den Azure-prenumeration som är associerad med din tidigare skapade lokala datagateway och väljer namnet på din lokala datagateway.

   Om din Gateway inte visas i listan kontrollerar du att du har konfigurerat [din gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)korrekt.

   ![Skapande av SQL Server anslutning slutförd](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. När du är klar väljer du **Skapa**.

1. När du har skapat anslutningen fortsätter du med [Lägg till SQL-utlösare](#add-sql-trigger) eller [Lägg till SQL-åtgärd](#add-sql-action).
