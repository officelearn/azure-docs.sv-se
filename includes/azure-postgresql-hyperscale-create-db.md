---
title: ta med fil
description: ta med fil
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: e7a6f7b4ba4219483cd3eb8f4600bc94213df131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973430"
---
Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Skapa en Azure-databas för PostgreSQL - Hyperskala (Citus)

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Klicka på **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen.
2. Välj **databaser** från sidan **Nytt** och välj **Azure Database för PostgreSQL** från sidan **databaser**.
3. Klicka på knappen **Skapa** under **Servergrupp Hyperskala (Citus).**
4. Fyll i formuläret om den nya servern och uppge följande information:
   - Resursgrupp: Klicka på länken **Skapa ny** under textrutan för det här fältet. Ange ett namn som **minresursgrupp**.
   - Servergruppsnamn: Ange ett unikt namn för den nya servergruppen, som också kommer att användas för en serverunderdomän.
   - Administratörsanvändarnamn: krävs för närvarande för att vara värdet **citus**och kan inte ändras.
   - Lösenord: måste vara minst åtta tecken långt och innehålla tecken från tre av följande kategorier – engelska versaler, engelska gemener, siffror (0–9) och icke-alfanumeriska tecken (!, $, #, %och så vidare.)
   - Plats: Använd den plats som är närmast användarna för att ge dem den snabbaste åtkomsten till data.

   > [!IMPORTANT]
   > Lösenordet för serveradministratören som du anger här måste logga in på servern och dess databaser. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

5. Klicka på **Konfigurera servergrupp**. Lämna inställningarna i avsnittet oförändrade och klicka på **Spara**.
6. Klicka på **Nästa: Nätverk >** längst ned på skärmen.

7. Klicka på alternativknappen **Offentlig slutpunkt** på fliken **Nätverk.**
   ![Offentlig slutpunkt vald](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klicka på länken **+ Lägg till aktuell klient-IP-adress**.
   ![Lade till klient-IP](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Azure PostgreSQL-servern kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. Om så är fallet kan du inte ansluta till citus-klustret (Hyperscale) om inte IT-avdelningen öppnar port 5432.
   >

9. Klicka på **Granska + skapa** och sedan **skapa** för att etablera servern. Etableringen tar några minuter.
10. Sidan omdirigeras för att övervaka distributionen. När livestatusändringarna från **distributionen är klar** till **Din distribution är klar**klickar du på menyalternativet **Utdata** till vänster på sidan.
11. Utdatasidan innehåller ett koordinatorvärdnamn med en knapp bredvid för att kopiera värdet till Urklipp. Registrera den här informationen för senare användning.

## <a name="connect-to-the-database-using-psql"></a>Ansluta till databasen med psql

När du skapar din Azure-databas för PostgreSQL-server skapas en standarddatabas med namnet **citus.** Om du vill ansluta till databasservern behöver du en anslutningssträng och administratörslösenordet.

1. Hämta anslutningssträngen. Klicka på menyalternativet **Anslutningssträngar** på servergruppssidan. (Det är under **Inställningar**.) Leta reda på strängen märkt **psql**. Det kommer att vara av formen:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Kopiera strängen. Du måste ersätta "{ditt\_lösenord}" med det administrativa lösenord som du valde tidigare. Systemet lagrar inte ditt lösenord med klartext och kan därför inte visa det åt dig i anslutningssträngen.

2. Öppna ett terminalfönster på den lokala datorn.

3. Anslut till Azure Database för PostgreSQL-server med [psql-verktyget](https://www.postgresql.org/docs/current/app-psql.html) i prompten. Skicka din anslutningssträng inom citationstecken och se till att den innehåller ditt lösenord:
   ```bash
   psql "host=..."
   ```

   Följande kommando ansluter till exempel koordinatornoden för servergruppen **mydemoserver:**

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
