---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789212"
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

När SQL-utlösaren eller åtgärden efterfrågar anslutnings information följer du dessa steg, som fungerar för både utlösare och åtgärder. För scenarier som kräver att du installerar den lokala [datagatewayen](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) på en lokal dator och [skapar Azure data Gateway-resursen](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection), måste du först kontrol lera att du har slutfört dessa krav. Annars visas inte din gateway-resurs i listan med gatewayer när du skapar anslutningen.

Om du vill använda Windows-autentisering med SQL Server-anslutaren i en [integrerings tjänst miljö (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)använder du även anslutningens icke-ISE-version och den lokala datagatewayen. ISE-märkta versioner stöder inte Windows-autentisering.

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
