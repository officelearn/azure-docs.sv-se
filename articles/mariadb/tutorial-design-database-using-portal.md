---
title: 'Självstudie: Utforma en Azure Database for MariaDB med Azure-portalen'
description: I den här självstudien beskrivs hur du skapar och hanterar en Azure Database for MariaDB-server och -databas med Azure-portalen.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: tutorial
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 2e1cd0d28c544b2e4c5dc86c7a6db39a5d20c1ed
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991019"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-using-the-azure-portal"></a>Självstudie: skapa en Azure Database for MariaDB-databas med Azure-portalen
Azure Database for MariaDB är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. I Azure-portalen kan du enkelt hantera servern och utforma en databas.

I den här självstudien använder du Azure-portalen till att:

> [!div class="checklist"]
> * Skapa en Azure Database for MariaDB
> * Konfigurera serverbrandväggen
> * Använda kommandoradsverktyget mysql till att skapa en databas
> * Läsa in exempeldata
> * Frågedata
> * Uppdatera data
> * Återställa data

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna din webbläsare och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server
En Azure Database for MariaDB-server skapas med en definierad uppsättning compute- och beräkningsresurser <!--[compute and storage resources](./concepts-compute-unit-and-storage.md)-->. Servern skapas inom en [Azure-resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Skriv in **Azure Database for MariaDB** i sökrutan för att hitta tjänsten.
   
   ![Navigera till MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Klicka på ikonen **Azure Database for MariaDB** och klicka sedan på **Skapa**. Fyll i formuläret för Azure Database for MariaDB.
   
   ![Skapa formulär](./media/tutorial-design-database-using-portal/2-create-form.png)

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning** 
    ---|---|---
    servernamn | Unikt servernamn | Välj ett unikt namn för din Azure Database for MariaDB-server. Till exempel mydemoserver. Domännamnet *.mariadb.database.azure.com* läggs till i det servernamn du anger. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
    Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | *myresourcegroup* | Ange ett nytt eller ett befintligt resursgruppnamn.    Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
    Välj källa | *Tom* | Välj *Tom* om du vill skapa en ny server från början. (Du kan välja *Säkerhetskopiering* om du vill skapa en server från en geo-säkerhetskopia av en befintlig Azure Database for MariaDB-server).
    inloggning för serveradministratör | myadmin | Ett inloggningskonto som du använder när du ansluter till servern. Inloggningsnamnet för administratören får inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.
    Lösenord | *Ditt val* | Ange ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv).
    Bekräfta lösenord | *Ditt val*| Bekräfta administratörslösenordet.
    Plats | *Regionen som ligger närmast dina användare*| Välj den plats som är närmast dina användare eller dina andra Azure-program.
    Version | *Den senaste versionen*| Välj den senaste versionen (om du inte har särskilda behov som gör att du måste ha en annan version).
    Prisnivå | **Generell användning**, **Gen 5**, **2 virtuella kärnor**, **5 GB**, **7 dagar**, **Geografiskt redundant** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Prisnivå**. Sedan väljer du fliken **Generell användning**. *Gen 5*, *2 virtuella kärnor*, *5 GB*, och *7 dagar* är standardvärdena för **Compute-generering**, **Virtuell kärna** , **Lagring** och **Kvarhållningsperiod för säkerhetskopior**. Du kan lämna dessa skjutreglage som de är. Välj **Geografiskt redundant** bland **redundansalternativen för säkerhetskopiering** om du vill använda geo-redundant lagring för dina serversäkerhetskopior. Spara den valda prisnivån genom att välja **OK**. På nästa skärmbild visas dessa val.
    
   ![Prisnivå](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

4. Klicka på **Skapa**. Efter några minuter körs en ny Azure Database for MariaDB-server i molnet. Du kan klicka på knappen **Aviseringar** i verktygsfältet om du vill övervaka distribueringsprocessen.

## <a name="configure-firewall"></a>Konfigurera brandvägg
Azure Database for MariaDB-databaser skyddas av en brandvägg. Som standard avvisas alla anslutningar till servern och databaserna på servern. Innan du ansluter till Azure Database for MariaDB för första gången ska du konfigurera brandväggen att lägga till klientdatorns offentliga nätverks-IP-adress (eller IP-adressintervall).

1. Klicka på server du skapade och sedan på **Anslutningssäkerhet**.
   
   ![Anslutningssäkerhet](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Välj **Lägg till min IP** eller konfigurera brandväggsregler här. Kom ihåg att klicka på **Spara** när du har skapat reglerna.
Nu kan du ansluta till servern med kommandoradsverktyget mysql eller gränssnittsverktyget MySQL Workbench.

> [!TIP]
> Azure Database for MariaDB-servern kommunicerar över port 3306. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 3306 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure Database for MariaDB-server om inte din IT-avdelning öppnar port 3306.

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta det fullständiga **servernamnet** och **inloggningsnamnet för serveradministratören** för din Azure Database for MariaDB-server från Azure-portalen. Du använder det fullständigt kvalificerade servernamnet när du ska ansluta till servern med kommandoradsverktyget mysql. 

1. I [Azure-portalen](https://portal.azure.com/) klickar du på **Alla resurser** i menyn till vänster, skriver namnet och letar sedan rätt på din Azure Database for MariaDB-server. Välj servernamnet så visas informationen.

2. Skriv ned **Servernamn** och **Inloggningsnamn för serveradministratör** på sidan **Översikt**. Du kan klicka på kopieringsknappen bredvid respektive fält för att kopiera till Urklipp.
   ![4-2 Serveregenskaper](./media/tutorial-design-database-using-portal/2-server-properties.png)

I det här exemplet är servernamnet *mydemoserver.mariadb.database.azure.com* och inloggningen för serveradministratören är *myadmin@mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Anslut till servern med mysql
Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) till att upprätta en anslutning till din Azure Database för MariaDB-server. Du kan köra kommandoradsverktyget mysql från Azure Cloud Shell i webbläsaren, eller från din egen dator med mysql-verktyg som installerats lokalt. Om du vill starta Azure Cloud Shell klickar du på knappen `Try It` i ett kodblock i den här artikeln, eller så öppnar du Azure-portalen och klickar på ikonen `>_` i det övre högra verktygsfältet. 

Skriv anslutningskommandot:
```azurecli-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas
När du är ansluten till servern skapar du en tom databas att arbeta med.
```sql
CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure Database for MariaDB-databasen kan du utföra några grundläggande uppgifter:

Skapa först en tabell och läs in lite data till den. Vi skapar en tabell som innehåller lagerinformation.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läs in data till tabellerna
Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken så at du löser in några datarader.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna
Kör följande frågor för att hämta information från databastabellen.
```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellerna.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras när du hämtar data.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt
Anta att du av misstag har tagit bort en viktig databastabell och inte enkelt kan återställa dessa data. Med Azure Database for MariaDB kan du återställa servern till en annan tidpunkt och skapa en kopia av databaserna på den nya servern. Du kan använda den nya servern till att återställa dina data. Följande steg återställer exempelservern till en tidpunkt innan tabellen lades till.

1. Leta rätt på din Azure Database for MariaDB i Azure-portalen. På sidan **Översikt** klickar du på **Återställ** i verktygsfältet. Sidan Återställ öppnas.

   ![10-1 Återställ en databas](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Fyll i formuläret **Återställ** med den information som behövs.
   
   ![10-2 Återställningsformulär](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Återställningspunkt**: Välj en tidpunkt du vill återställa till, inom den tidsperiod som visas. Var noga med att konvertera din lokala tidszon till UTC.
   - **Återställ till ny server**: Ange ett nytt servernamn som du vill återställa till.
   - **Plats**: Regionen är samma som källservern och kan inte ändras.
   - **Prisnivå**: Prisnivån är samma som källservern och kan inte ändras.
   
3. Klicka på **OK** för att återställa servern till en tidpunkt <!--[restore to a point in time](./howto-restore-server-portal.md)--> innan tabellen togs bort. När du återställer en server skapas en ny kopia av servern vid den tidpunkt du anger. 

<!--## Next steps
In this tutorial, you use the Azure portal to learned how to:

> [!div class="checklist"]
> * Create an Azure Database for MariaDB
> * Configure the server firewall
> * Use mysql command-line tool to create a database
> * Load sample data
> * Query data
> * Update data
> * Restore data

><> [!div class="nextstepaction"]
> [How to connect applications to Azure Database for MariaDB](./howto-connection-string.md)-->
