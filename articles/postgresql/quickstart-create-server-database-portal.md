---
title: 'Snabb start: skapa server-Azure Portal-Azure Database for PostgreSQL-enskild server'
description: Snabb starts guide för att skapa och hantera en Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal användar gränssnittet.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 9741e00cdcc4907c547ebfcc6f8e7df7a3b32891
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529698"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Snabbstart: Skapa en Azure Database for PostgreSQL-server i Azure Portal

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database for PostgreSQL-server med hjälp av Azure Portal på ungefär fem minuter.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå till [portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server

En Azure Database for PostgreSQL-server skapas med en konfigurerad uppsättning [beräknings- och lagringsresurser](./concepts-pricing-tiers.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > !["Azure Database for PostgreSQL" på menyn](./media/quickstart-create-database-portal/1-create-database.png)

3. Välj distributions alternativet **enskild server** .

   > [!div class="mx-imgBorder"]
   > ![Välj Azure Database for PostgreSQL – distributions alternativ för enskild server](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Fyll i formuläret **grundläggande** med följande information:

   > [!div class="mx-imgBorder"]
   > ![Skapa en server](./media/quickstart-create-database-portal/create-basics.png)

   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Prenumeration|Ditt prenumerationsnamn|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
   Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
   Servernamn |*mydemoserver*|Ett unikt namn som identifierar Azure Database för PostgreSQL-servern. Domännamnet *postgres.database.azure.com* läggs till i det servernamn du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla minst 3 och upp till 63 tecken.
   Datakälla | *Inga* | Välj *ingen* om du vill skapa en ny server från grunden. (Du väljer *Säkerhetskopiering* om du skapar en server från en geo-säkerhetskopia av en befintlig Azure Database for PostgreSQL-server).
   Administratörens användar namn |*myadmin*| Ett eget inloggningskonto att använda när du ansluter till servern. Inloggnings namnet för administratören får inte vara **azure_superuser**, **azure_pg_admin**, **admin**, **Administrator**, **root**, **Guest**eller **Public**. Den kan inte börja med **PG_**.
   lösenordsinställning |Ditt lösenord| Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till och med 9) och icke-alfanumeriska tecken (!, $, #, % osv.).
   Position|Den region som är närmast dina användare| Den plats som är närmast dina användare.
   Version|Senaste huvudversion| Den senaste PostgreSQL-huvudversionen, om du inte har andra särskilda krav.
   Beräkning och lagring | **Generell användning**, **Gen 5**, **2 virtuella kärnor**, **5 GB**, **7 dagar**, **Geografiskt redundant** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Konfigurera Server**. Välj sedan lämplig pris nivå för mer information, se [pris information](https://azure.microsoft.com/pricing/details/postgresql/server/). Om du vill aktivera server säkerhets kopieringar i Geo-redundant lagring väljer du **geografiskt redundant** från **alternativen för redundans för säkerhets kopiering**. Välj **OK**.

   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapas på prisnivån Basic inte senare kan skalas till Generell användning eller Minnesoptimerad. 
   
5. Välj **Granska + skapa** för att granska dina val. Välj **Skapa** för att etablera servern. Den här åtgärden kan ta några minuter.

6. Välj ikonen **Aviseringar** (en bjällra) i verktygsfältet för att övervaka distributionsprocessen. När distributionen är klar väljer du **gå till resurs** för att öppna serverns **översikts** sida.

En tom databas skapas **postgres** . Du hittar också **azure_maintenance** databas som används för att avgränsa hanterade tjänst processer från användar åtgärder. Du kan inte komma åt **azure_maintenance** databas.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå
Som standard är servern som skapats inte offentligt tillgänglig och du måste ge behörighet till din IP-adress. Om du vill ge åtkomst till din IP-adress går du till Server resursen i Azure Portal och väljer **anslutnings säkerhet** från menyn på vänster sida för Server resursen. Om du inte är säker på hur du hittar din resurs, se [så här öppnar du en resurs](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> ![Anslutningssäkerhet – Brandväggsregler](./media/quickstart-create-database-portal/add-current-ip-firewall.png)
  
Välj nu **Lägg till aktuell klient-IP-adress** och välj sedan **Spara**. Du kan lägga till ytterligare IP-adresser eller ange ett IP-intervall för att ansluta till servern från de IP-adresserna. Mer information finns i [Hantera brand Väggs regler](./concepts-firewall-rules.md)
   
> [!NOTE]
> Kontrol lera om nätverket tillåter utgående trafik över Port 5432 som används av Azure Database for PostgreSQL för att undvika anslutnings problem.  

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Ansluta till Azure Database for PostgreSQL server med psql

Du kan använda [psql](http://postgresguide.com/utilities/psql.html) eller [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html) som är populära postgresql-klienter. I den här snabb starten ska vi ansluta med psql i [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) i Azure Portal.

1. Anteckna Server namnet, inloggnings namnet för Server administratören, lösen ordet och prenumerations-ID: t för den nya servern från **översikts** avsnittet på servern som visas på bilden nedan.

2. Starta Azure Cloud Shell i portalen genom att välja ikonen på den övre vänstra sidan som marker ATS i bilden nedan.

   > [!NOTE]
   > Om du startar Cloud Shell för första gången visas en uppstarts fråga om att skapa en resurs grupp, ett lagrings konto. Det här är ett engångs steg och kommer automatiskt att bifogas för alla sessioner. 

   > [!div class="mx-imgBorder"]
   > ![Öppna Azure Cloud Shell](media/quickstart-create-database-portal/use-in-cloud-shell.png)

3. Kör det här kommandot på Azure Cloud Shell terminal. Ersätt värdena med det faktiska Server namnet och inloggnings namnet för administratörs användaren. Använd den tomma databasen **postgres** med administratörs användaren i det här formatet <admin-username> @ <servername> enligt nedan för Azure Database for PostgreSQL.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Så här ser upplevelsen ut som i Cloud Shell terminalen
   
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
4. I samma Azure Cloud Shell terminal skapar du en databas **gäst**
   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Nu ska du växla anslutning till den nyligen skapade databasen **gäst**

   ```bash
   \c guest
   ```
6. Skriv `\q` och välj sedan nyckeln Enter för att avsluta psql. 

## <a name="clean-up-resources"></a>Rensa resurser
Du har skapat en Azure Database for PostgreSQL-server i en resurs grupp.  Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort resurs gruppen eller bara ta bort PostgreSQL-servern. Följ dessa steg om du vill ta bort resurs gruppen:

1. I Azure Portal söker du efter och väljer **resurs grupper**. 
2. I listan resurs grupp väljer du namnet på din resurs grupp.
3. På sidan Översikt i resurs gruppen väljer du **ta bort resurs grupp**.
4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

Om du vill ta bort servern kan du klicka på knappen **ta bort** på sidan **Översikt** på servern enligt nedan:
> [!div class="mx-imgBorder"]
> ![Ta bort dina resurser](media/quickstart-create-database-portal/12-delete.png)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
