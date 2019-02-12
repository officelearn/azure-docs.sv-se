---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008526"
---
## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>Skapa en tom SQL-databas

Azure SQL-databasen finns i en definierad uppsättning [beräknings-och lagringsresurser](../articles/sql-database/sql-database-service-tiers-dtu.md). Databasen fungerar under en [Azure-resursgrupp](../articles/azure-resource-manager/resource-group-overview.md) och i en [logisk Azure SQL Database-server](../articles/sql-database/sql-database-features.md).

Följ de här stegen om du vill skapa en tom SQL-databas.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.

1. På sidan **Nytt** väljer du på **Databaser** > **SQL Database**.

   ![skapa tom databas](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. I fönstret **SQL Database** anger eller väljer du följande värden:

   | Inställning       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Databasnamn** | *yourDatabase* | För giltiga databasnamn, se [databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
   | **Prenumeration** | *yourSubscription*  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | *yourResourceGroup* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/naming-conventions). |
   | **Välj källa** | Tom databas | Anger att en tom databas ska skapas. |

   ![skapa databas](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. Välj **Server** för att konfigurera en server för den nya databasen. Ange eller välj sedan följande värden:

      | Inställning       | Föreslaget värde | Beskrivning |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/naming-conventions). |
      | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](/sql/relational-databases/databases/database-identifiers).|
      | **Lösenord** | Valfritt giltigt lösenord | Lösenordet måste innehålla minst 8 tecken och måste ha tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
      | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

      Välj **Välj**.

      ![skapa databas-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. Välj **Prisnivå** för att ange tjänstnivå, antalet DTU:er och mängden lagring. Undersök alternativen för DTU:er och lagringsutrymme som du har tillgång till på varje tjänstnivå.

      När du har valt tjänstenivå, antalet DTU:er och mängden lagring väljer du **Tillämpa**.

   1. Ange en **sortering** för den tomma databasen (använd standardvärdet för de här självstudierna). Mer information om sorteringar finns i [Sorteringar](/sql/t-sql/statements/collations).

1. Nu när du har fyllt i**SQL Database**-formuläret väljer du **Skapa** så att databasen skapas. Det här steget kan ta upp till en och en halv minut att slutföra.

1. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

     ![avisering](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Skapa en brandväggsregel

Tjänsten SQL Database skapar en brandvägg på servernivå som hindrar externa program och verktyg från att ansluta till servern eller databaser på servern. Följ dessa steg för att skapa en [brandväggsregel på servernivå i SQL Database](../articles/sql-database/sql-database-firewall-configure.md) för klientens IP-adress. Den här processen gör det möjligt med extern anslutning via brandväggen i SQL Database endast för din IP-adress.

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till Azure SQL Database-servern om inte din administratör öppnar port 1433.

1. När distributionen är klar väljer du **SQL-databaser** på menyn till vänster och väljer sedan *yourDatabase* på sidan **SQL-databaser**. Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade **servernamnet** (till exempel *yourserver.database.windows.net*) tillsammans med alternativ för ytterligare konfiguration.

1. Kopiera det fullständigt kvalificerade servernamnet. Du behöver det när du ansluter till servern och dess databaser i senare steg.

   ![servernamn](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. Välj **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas.

   ![brandväggsregler för server](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. Välj **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

   1. Välj **Spara**. En brandväggsregel på servernivå för att öppna port 1433 på den logiska servern skapas för din aktuella IP-adress.

   1. Välj **OK** och stäng sedan sidan **Brandväggsinställningar**.

Din IP-adress kan nu passera brandväggen och ansluta till SQL-databasservern och dess databaser med hjälp av SSMS eller något annat verktyg. Se till att använda serverns administratörskonto som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Välj **AV** på den här sidan om du vill inaktivera åtkomsten för alla Azure-tjänster.
