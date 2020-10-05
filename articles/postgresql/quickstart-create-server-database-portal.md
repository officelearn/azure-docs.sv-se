---
title: 'Snabb start: skapa server-Azure Portal-Azure Database for PostgreSQL-enskild server'
description: I den här snabb starts guiden ska du skapa och hantera en Azure Database for PostgreSQL-server med hjälp av Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 6e43d00722dd86934c8f95e06a3b8b590b263d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91705168"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Snabb start: skapa en Azure Database for PostgreSQL-server med hjälp av Azure Portal

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabb starten visar hur du skapar en enda Azure Database for PostgreSQL server på ungefär fem minuter med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå till [portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server

En Azure Database for PostgreSQL-server skapas med en konfigurerad uppsättning [beräknings- och lagringsresurser](./concepts-pricing-tiers.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

Så här skapar du en Azure Database for PostgreSQL Server:

1. Välj **skapa en resurs** i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Skärm bild som visar Azure Database for PostgreSQL på menyn.":::

3. Välj distributions alternativet **enskild server** .

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/select-deployment-option.png" alt-text="Skärm bild som visar Azure Database for PostgreSQL på menyn.":::

4. Fyll i formuläret **grunderna** med följande information.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Skärm bild som visar Azure Database for PostgreSQL på menyn.":::

   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Prenumeration|Ditt prenumerationsnamn|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
   Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
   Servernamn |*mydemoserver*|Ett unikt namn som identifierar Azure Database för PostgreSQL-servern. Domän namnet *postgres.Database.Azure.com* läggs till i det Server namn som du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla mellan 3 och 63 tecken.
   Datakälla | **Ingen** | Välj **Ingen** om du vill skapa en ny server från grunden. (Du väljer **säkerhets kopia** om du skapar en server från en geo-säkerhetskopia av en befintlig Azure Database for postgresql-server.)
   Användarnamn för administratör |*myadmin*| Ett eget inloggningskonto att använda när du ansluter till servern. Inloggnings namnet för administratören får inte vara **azure_superuser**, **azure_pg_admin**, **admin**, **Administrator**, **root**, **Guest**eller **Public**. Den kan inte börja med **PG_**.
   Lösenord |Ditt lösenord| Ett nytt lösenord för serverns administratörskonto. Det måste innehålla 8 till 128 tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till 9) och icke-alfanumeriska tecken (till exempel!, $, #,%).
   Plats|Den region som är närmast dina användare| Den plats som är närmast dina användare.
   Version|Senaste huvudversion| Den senaste PostgreSQL-huvudversionen, om du inte har andra särskilda krav.
   Beräkning och lagring | **Generell användning**, **Gen 5**, **2 virtuella kärnor**, **5 GB**, **7 dagar**, **Geografiskt redundant** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Konfigurera Server**. Välj sedan lämplig pris nivå. Mer information finns i [pris information](https://azure.microsoft.com/pricing/details/postgresql/server/). Om du vill aktivera server säkerhets kopieringar i Geo-redundant lagring väljer du **geografiskt redundant** från **alternativen för redundans för säkerhets kopiering**. Välj **OK**.

   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapats på grund pris nivån inte kan skalas senare till Generell användning eller Minnesoptimerade. 
   
5. Välj **Granska + skapa** för att granska dina val. Välj **Skapa** för att etablera servern. Den här åtgärden kan ta några minuter.

6. Välj ikonen **Aviseringar** (en bjällra) i verktygsfältet för att övervaka distributionsprocessen. När distributionen är klar väljer du **gå till resurs** för att öppna serverns **översikts** sida.

En tom databas, **postgres**, skapas. Du hittar också en **azure_maintenance** databas som används för att avgränsa hanterade tjänst processer från användar åtgärder. Du har inte åtkomst till **azure_maintenance** databasen.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå
Som standard är den server som du skapar inte offentligt tillgänglig. Du måste ge behörighet till din IP-adress. Gå till Server resursen i Azure Portal och välj **anslutnings säkerhet** på den vänstra menyn för Server resursen. Om du inte är säker på hur du hittar din resurs, se [Öppna resurser](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Skärm bild som visar Azure Database for PostgreSQL på menyn.":::
  
Välj **Lägg till aktuell klient-IP-adress**och välj sedan **Spara**. Du kan lägga till fler IP-adresser eller ange ett IP-intervall för att ansluta till servern från dessa IP-adresser. Mer information finns [i brand Väggs regler i Azure Database for PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> Undvik anslutnings problem genom att kontrol lera om nätverket tillåter utgående trafik via port 5432. Azure Database for PostgreSQL använder den porten.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Ansluta till Azure Database for PostgreSQL server med hjälp av psql

Du kan använda [psql](http://postgresguide.com/utilities/psql.html) eller [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), som är populära postgresql-klienter. I den här snabb starten ska vi ansluta med psql i [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) i Azure Portal.

1. Anteckna Server namnet, inloggnings namnet för Server administratören, lösen ordet och prenumerations-ID: t för den nya servern från **översikts** avsnittet på servern.

2. Öppna Azure Cloud Shell i portalen genom att välja ikonen på den övre vänstra sidan.

   > [!NOTE]
   > Om du öppnar Cloud Shell för första gången visas en uppfrågad om att skapa en resurs grupp och ett lagrings konto. Det här är ett engångs steg och kommer automatiskt att bifogas för alla sessioner. 

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Skärm bild som visar Azure Database for PostgreSQL på menyn.":::

3. Kör följande kommando i Azure Cloud Shell terminalen. Ersätt värdena med det faktiska Server namnet och inloggnings namnet för administratörs användaren. Använd den tomma databasen **postgres** med administratörs användaren i det här formatet: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Så här ser upplevelsen ut i Cloud Shell terminalen:
   
   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell
 
    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Skapa en databas med namnet **gäst**i samma Azure Cloud Shell terminal.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Växla anslutningar till den nyligen skapade **gäst** databasen.

   ```bash
   \c guest
   ```
6. Skriv `\q` och välj sedan Retur för att stänga psql. 

## <a name="clean-up-resources"></a>Rensa resurser
Du har skapat en Azure Database for PostgreSQL-server i en resurs grupp. Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort antingen resurs gruppen eller PostgreSQL-servern. 

Så här tar du bort resursgruppen:

1. I Azure Portal söker du efter och väljer **resurs grupper**. 
2. I listan resurs grupp väljer du namnet på din resurs grupp.
3. På sidan **Översikt** i resurs gruppen väljer du **ta bort resurs grupp**.
4. I bekräftelse dialog rutan anger du namnet på din resurs grupp och väljer sedan **ta bort**.

Om du vill ta bort servern väljer du knappen **ta bort** på sidan **Översikt** på servern:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Skärm bild som visar Azure Database for PostgreSQL på menyn.":::

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med export och import](./howto-migrate-using-export-and-import.md)
