---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789212"
---
* Om du använder Azure SQL Database följer du stegen under [Anslut till Azure SQL Database](#connect-azure-sql-db).

* Om du använder SQL Server följer du stegen under [Anslut till SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Ansluta till Azure SQL Database

När SQL-utlösaren eller åtgärden uppmanar dig att ange anslutningsinformation följer du dessa steg, som fungerar för både utlösare och åtgärder.

1. Skapa ett namn för anslutningen för **anslutningsnamn.**

1. Välj din Azure SQL-server under **SQL Server-namn.** När listan **Namn på SQL-databas** visas väljer du databasen. Ange användarnamn och lösenord för din Azure SQL-server.

   Du kan också hitta den här informationen antingen i Azure-portalen under sql-databasegenskaperna eller i anslutningssträngen:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Skapa anslutning till Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. När du är klar väljer du **Skapa**.

1. När du har skapat anslutningen fortsätter du med [Lägg till en SQL-utlösare](#add-sql-trigger) eller [Lägg till en SQL-åtgärd](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Anslut till SQL Server

När SQL-utlösaren eller åtgärden uppmanar dig att ange anslutningsinformation följer du dessa steg, som fungerar för både utlösare och åtgärder. För scenarier som kräver att du installerar den [lokala datagatewayen](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) på en lokal dator och [skapar Azure-datagatewayresursen,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)se till att du slutför dessa krav först. Annars visas inte gatewayresursen i gatewaylistan när du skapar anslutningen.

Om du vill använda Windows-autentisering med SQL Server-anslutningen i en [integrationstjänstmiljö (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)använder du även anslutningens icke-ISE-version och den lokala datagatewayen. Den ISE-märkta versionen stöder inte Windows-autentisering.

1. Skapa ett namn för anslutningen för **anslutningsnamn.**

1. I utlösaren eller åtgärden väljer du **Anslut via lokal datagateway** så att SQL-serveralternativen visas.

1. Ange adressen till SQL-servern och namnet på **SQL-servern**för **SQL-server** och namnet på databasen. För **användarnamn** och **lösenord**anger du serverns användarnamn och lösenord.

   Du kan också hitta den här informationen i anslutningssträngen:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Skapa anslutning till SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Om SQL-servern använder Windows- eller Basic-autentisering väljer du **autentiseringstypen**.

1. Under **Gateways**väljer du den Azure-prenumeration som är associerad med din tidigare skapade lokala datagateway och väljer namnet för din lokala datagateway.

   Om gatewayen inte visas i listan kontrollerar du att du [har konfigurerat gatewayen](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)korrekt .

   ![Skapa SQL Server-anslutningen klar](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. När du är klar väljer du **Skapa**.

1. När du har skapat anslutningen fortsätter du med [Lägg till SQL-utlösare](#add-sql-trigger) eller [Lägg till SQL-åtgärd](#add-sql-action).
