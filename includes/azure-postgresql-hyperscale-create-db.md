---
title: ta med fil
description: ta med fil
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66154498"
---
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Skapa en Azure Database för PostgreSQL – hyperskala (Citus)

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Välj **databaser** från sidan **Nytt** och välj **Azure Database för PostgreSQL** från sidan **databaser**.
3. För alternativ för distribution, klickar du på den **skapa** knappen **hyperskala (Citus) servergrupp - FÖRHANDSVERSION.**
4. Fyll i formuläret om den nya servern och uppge följande information:
   - Resursgrupp: Klicka på den **Skapa nytt** länken under textrutan för det här fältet. Ange ett namn som **myresourcegroup**.
   - Servergruppnamn: Ange ett unikt namn för den nya servergruppen som också används för en server underdomän.
   - Användarnamn för administratör: för närvarande måste vara värdet **citus**, och kan inte ändras.
   - Lösenord: måste vara minst åtta tecken långt och innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv.)
   - Plats: Använd den plats som är närmast dina användare att ge dem snabbast åtkomst till data.

   > [!IMPORTANT]
   > Lösenordet för serveradministratören som du anger här krävs för att logga in på servern och databaserna. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

5. Klicka på **konfigurera servergrupp**. Lämna inställningarna i den avsnittet oförändrade och klicka på **spara**.
6. Klicka på **granska + skapa** och sedan **skapa** att etablera servern. Etableringen tar några minuter.
7. Sidan omdirigerar för att övervaka distributionen. Då live status ändras från **distributionen pågår** till **distributionen är klar**, klickar du på den **utdata** menyalternativ till vänster på sidan.
8. Sidan utdata innehåller ett coordinator värdnamn med en knapp bredvid den att kopiera värdet till Urklipp. Registrera den här informationen för senare användning.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database för PostgreSQL – hyperskala (Citus) (förhandsgranskning)-tjänsten använder en brandvägg på servernivå. Som standard förhindrar brandväggen alla externa program och verktyg ansluter till koordinatornoden eller databaser i. Vi måste lägga till en regel som öppnar brandväggen för ett specifikt IP-adressintervall.

1. Från den **utdata** avsnitt där du tidigare kopierade coordinator noden värdnamn, klickar du på tillbaka till den **översikt** menyalternativ.

2. Hitta namnet på servern distributionsgruppen och klicka på den. (Namnet på servern kommer *inte* ha ett suffix. Objekt med namn som slutar på, till exempel ”-c” ”,-w0”, eller ”-w1” är inte servergruppen.)

3. Klicka på **brandväggen** under **Security** på den vänstra menyn.

4. Klicka på länken **+ Lägg till brandväggsregel för aktuella klientens IP-adress**.

5. Klicka slutligen på den **spara** knappen.

   > [!NOTE]
   > Azure PostgreSQL-servern kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 5432.
   >

## <a name="connect-to-the-database-using-psql"></a>Ansluta till databasen med psql

När du skapar din Azure Database for PostgreSQL-server, en standarddatabas med namnet **citus** har skapats. För att ansluta till din databasserver, behöver du en anslutningssträng och lösenordet för serveradministratören.

1. Hämta anslutningssträngen. I gruppsidan klickar du på den **anslutningssträngar** menyalternativ. (Det är **inställningar**.) Hitta den sträng som markerats  **C++ (libpq)**. Det ska vara i formatet:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Kopiera strängen. Du måste ersätta ”{ditt\_lösenord}” med det administrativa lösenordet som du valde tidigare. Systemet lagrar inte dina lösenord i klartext och så det går inte att visa det åt dig i anslutningssträngen.

2. Öppna ett terminalfönster på din lokala dator.

3. I prompten, ansluter du till din Azure Database for PostgreSQL-server med den [psql](https://www.postgresql.org/docs/current/app-psql.html) verktyget. Skicka din anslutningssträng i citattecken, som att den innehåller ditt lösenord:
   ```bash
   psql "{connection_string}"
   ```

   Till exempel följande kommando ansluter till koordinatornoden på servergruppen **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
