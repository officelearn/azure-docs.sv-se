---
title: "Azure-portalen: Skapa en Azure-databas för PostgreSQL-server | Microsoft Docs"
description: "Snabbstartsguide för att skapa och hantera Azure-databas för PostgreSQL-server med användargränssnittet för Azure-portalen."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/03/2017
ms.openlocfilehash: b78009a4b2683bb7ee881808ddbbc792d66dea6c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Skapa en Azure-databas för PostgreSQL i Azure-portalen

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL-server med Azure Portal på ungefär fem minuter.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå till [portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

En Azure Database för PostgreSQL-server skapas med en definierad uppsättning [compute- och lagringsresurser](./concepts-compute-unit-and-storage.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md).

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Klicka på knappen **Nytt** (+) i det övre vänstra hörnet i portalen.

2. Välj **Databaser** > **Azure-databas för PostgreSQL**.

    ![Alternativet ”Azure-databas för PostgreSQL”](./media/quickstart-create-database-portal/1-create-database.png)

3. Fyll i detaljformuläret för den nya servern med följande information (se föregående bild):

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    servernamn |*mypgserver-20170401*|Ett unikt namn som identifierar Azure Database för PostgreSQL-servern. Domännamnet *postgres.database.azure.com* läggs till i det servernamn du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla minst 3 och upp till 63 tecken.
    Prenumeration|Din prenumeration|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
    inloggning för serveradministratör |*mylogin*| Ett eget inloggningskonto att använda när du ansluter till servern. Inloggningsnamnet för administratören får inte vara **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest,** eller **public.** Det får inte börja med **pg_**.
    Lösenord |Ditt val | Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till och med 9) och icke-alfanumeriska tecken (!, $, #, % osv.).
    Plats|Den region som är närmast dina användare| Den plats som är närmast dina användare.
    PostgreSQL-version|Den senaste versionen| Den senaste versionen såvida du inte har särskilda krav.
    Prisnivå | **Basic**, **50 Compute-enheter**, **50 GB** | Tjänstnivå och prestandanivå för den nya databasen. Välj **Prisnivå**. Välj sedan fliken **Grundläggande**. Välj änden längst till vänster på skjutreglaget **Compute-enheter** och justera värdet till minsta tillgängliga mängd för den här snabbstarten. Spara den valda prisnivån genom att välja **OK**. Mer information finns på följande skärmbild. 
    Fäst vid instrumentpanelen | Markera | Gör att du enkelt kan spåra servern på den främre instrumentpanelen på portalen.

    > [!IMPORTANT]
    > Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

    ![Fönstret ”Prisnivå”](./media/quickstart-create-database-portal/2-service-tier.png)

4. Välj **Skapa** för att etablera servern. Etableringen kan ta upp till 20 minuter.

5. Välj symbolen **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

    ![Fönstret ”Aviseringar”](./media/quickstart-create-database-portal/3-notifications.png)
   
  Som standard skapas en **postgres**-databas under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. 

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database för PostgreSQL skapar en brandvägg på server-nivå. Den förhindrar att externa program och verktyg ansluter till servern eller databaser på servern, om du inte konfigurerar en regel som öppnar brandväggen för specifika IP-adresser. 

1. Leta upp servern när distributionen är klar. Om det behövs kan du söka efter den. Välj exempelvis **Alla resurser** på menyn till vänster. Skriv in servernamnet, till exempel **mypgserver-20170401** för att söka efter den nya servern. Välj servernamnet i sökresultatlistan. **Översikt**-sidan för din server öppnas och innehåller alternativ ytterligare konfiguration.
 
    ![Sökning efter servernamn](./media/quickstart-create-database-portal/4-locate.png)

2. På serversidan väljer du **Anslutningssäkerhet**.

    ![Inställningen ”Anslutningssäkerhet”](./media/quickstart-create-database-portal/5-firewall-2.png)

3. Under **Brandväggsregler** väljer du den tomma textrutan i kolumnen **Regelnamn** och börjar skapa brandväggsregeln. 

    I den här snabbstarten vill vi tillåta alla IP-adresser till servern. Fyll i textrutan i varje kolumn med följande värden:

    Regelnamn | Start-ip | Slut-ip 
    ---|---|---
    AllowAllIps | 0.0.0.0 | 255.255.255.255

4. Välj **Spara** i det övre verktygsfältet på sidan **Anslutningssäkerhet**. Vänta tills meddelandet visas som talar om att uppdateringen av anslutningssäkerhet har slutförts innan du fortsätter.

    > [!NOTE]
    > Anslutningar till din Azure Database för PostgreSQL-server kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 5432.
    >

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När du skapar din Azure Database för PostgreSQL-server skapas även en standarddatabas med namnet **postgres**. Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan ha antecknat de här värdena tidigare i snabbstartsartikeln. I annat fall hittar du enkelt servernamnet och inloggningsuppgifterna på serversidan **Översikt** i portalen.

Öppna serverns **Översikt**-sida. Anteckna **Servernamn** och **Inloggningsnamn för serveradministratören**. Håll markören över varje fält så att kopieringssymbolen visas till höger om texten. Välj kopieringssymbolen för att kopiera värdena.

 ![Serversidan ”Översikt”](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-by-using-psql-in-cloud-shell"></a>Anslut till PostgreSQL-databasen med psql i Cloud Shell

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Först använder vi psql-kommandoradsverktyget för att visa hur man ansluter till servern. Du kan använda en webbläsare och Azure Cloud Shell enligt nedan utan att behöva installera ytterligare programvara. Om psql-verktyget är installerat lokalt på din dator kan du ansluta därifrån också.

1. I det övre navigeringsfönstret väljer du terminalsymbolen för att öppna Cloud Shell.

   ![Terminalsymbol för Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Cloud Shell öppnas i webbläsaren så att du kan skriva Bash shell-kommandon.

   ![Cloud Shell Bash-kommandotolk](./media/quickstart-create-database-portal/8-bash.png)

3. I Cloud Shell-kommandotolken ansluter du till din Azure Database för PostgreSQL-server genom att skriva in psql-kommandoraden.

    Använd följande format för att ansluta till en Azure Database för PostgreSQL-server med [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Till exempel ansluter följande kommando till en exempelserver:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    --host | servernamn | Det värde för servernamn som du använde tidigare när du skapade Azure Database för PostgreSQL-server. Exempelservern som visas är **mypgserver-20170401.postgres.database.azure.com**. Använd det fullständiga domännamnet (**\*.postgres.database.azure.com**) som i det här exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. 
    --port | 5432 | Porten som ska användas när du ansluter till Azure Database för PostgreSQL-servern. 
    --username | Inloggningsnamn för serveradministratör |Ange det användarnamn för serveradministratörsinloggning som du angav tidigare när du skapade Azure Database för PostgreSQL-server. Om du inte kommer ihåg användarnamnet följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. Formatet är *username@servername*.
    --dbname | *postgres* | Det systemgenererade standarddatabasnamnet som skapades för den första anslutningen. Senare kan du skapa en egen databas.

    När du har kört psql-kommandot med egna parametervärden uppmanas du att ange lösenordet för serveradministratören. Det här är samma lösenord som du angav när du skapade servern. 

    psql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    lösenord | Ditt administratörslösenord | Det angivna lösenordet visas inte i bash-kommandotolken. Välj **Retur** när du har skrivit alla tecken för att autentisera och ansluta.

    När du har anslutit visar psql-verktyget en postgres-kommandotolk där du skriver sql-kommandon. Vid den första anslutningen kanske en varning visas eftersom psql i Cloud Shell kan vara en annan version än Azure Databas för PostgreSQL-serverversionen. 
    
    Exempel på psql-utdata:
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Om brandväggen inte är konfigurerad att tillåta IP-adressen för Cloud Shell uppstår följande fel:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL: SSL connection is required. Specify SSL options and retry.
    > 
    > Lös felet genom att se till att serverkonfigurationen matchar stegen i avsnittet ”Konfigurera en brandväggsregel på servernivå” i artikeln.

4. Skapa en tom databas i kommandotolken genom att skriva följande kommando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    Det kan ta några minuter att slutföra kommandot. 

5. I kommandotolken kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
    ```bash
    \c mypgsqldb
    ```

6. Skriv `\q` och välj sedan **Retur** för att avsluta psql. När du är klar kan du stänga Cloud Shell.

Nu har du anslutit till Azure Database för PostgreSQL-servern och skapat en tom användardatabas. Fortsätt till nästa avsnitt för att ansluta med hjälp av ett annat vanligt verktyg, pgAdmin.

## <a name="connect-to-the-postgresql-database-by-using-pgadmin"></a>Ansluta till PostgreSQL-databasen med hjälp av pgAdmin

Ansluta till Azure PostgreSQL-servern med hjälp av GUI-verktyget pgAdmin:
1. Öppna pgAdmin-programmet på klientdatorn. Du kan installera pgAdmin från [pgAdmin-webbplatsen](http://www.pgadmin.org/).

2. På instrumentpanelssidan går du till avsnittet **Snabblänkar** och väljer symbolen **Lägg till ny server**.

3. I dialogrutan **Skapa – server** på fliken **Allmänt** anger du ett unikt eget namn för servern, t.ex. **Azure PostgreSQL-server**.

    ![Fliken ”Allmänt”](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. I dialogrutan **Skapa – server** på fliken **Anslutning** använder du de angivna inställningarna och klickar på **Spara**.

   ![Fliken ”Anslutning”](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    Värdnamn/-adress | servernamn | Det värde för servernamn som du använde tidigare när du skapade Azure Database för PostgreSQL-server. Vår exempelserver är **mypgserver-20170401.postgres.database.azure.com**. Använd det fullständiga domännamnet (**\*.postgres.database.azure.com**) som i det här exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. 
    Port | 5432 | Porten som ska användas när du ansluter till Azure Database för PostgreSQL-servern. 
    Underhållsdatabas | *postgres* | Systemgenererat standardnamn för databasen.
    Användarnamn | Inloggningsnamn för serveradministratör | Ange det användarnamn för serveradministratörsinloggning som du angav tidigare när du skapade Azure Database för PostgreSQL-server. Om du inte kommer ihåg användarnamnet följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. Formatet är *username@servername*.
    Lösenord | Ditt administratörslösenord | Det lösenord du angav när du skapade servern tidigare i den här snabbstarten.
    Roll | Lämna tomt | Du behöver inte ange ett rollnamn nu. Lämna fältet tomt.
    SSL-läge | Krävs | Som standard skapas alla Azure PostgreSQL-servrar med tvingande SSL aktiverat. Om du vill inaktivera tvingande SSL kan du se [Framtvinga SSL-anslutning](./concepts-ssl-connection-security.md).
    
5. Välj **Spara**.

6. I fönstret **Webbläsare** till vänster expanderar du noden **Servrar**. Markera din server, till exempel **Azure PostgreSQL-server**. Klicka för att ansluta till den.

7. Expandera servernoden och expandera sedan **Databaser** under den. Listan ska innehålla din befintliga *postgres*-databas och alla eventuella nyligen skapade användardatabaser, som **mypgsqldb**, som skapades i föregående avsnitt. Observera att du kan skapa flera databaser per server med Azure Database för PostgreSQL.

8. Högerklicka på **Databaser**, välj menyn **Skapa** och välj sedan **Databas**.

9. Skriv valfritt databasnamn i fältet **Databas**, till exempel **mypgsqldb** som i exemplet.

10. Välj **Ägare** för databasen från den nedrullningsbara listan. Välj ditt inloggningsnamn som administratör för servern, som vårt exempel **mylogin**.

11. Välj **Spara** för att skapa en tom databas.

12. I fönstret **Webbläsare** ser du den databas du skapade i listan över databaser under servernamnet.

    ![Fönstret ”Webbläsare”](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Rensa resurser
Du kan rensa de resurser som du skapade i snabbstarten på något av två sätt. Du kan ta bort den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som innehåller alla resurser i resursgruppen. Om du vill bevara alla andra resurser tar du bara bort den enda serverresursen.

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Rensa inte upp resurserna som du skapade i den här snabbstarten om du tänker fortsätta att arbeta med efterföljande snabbstarter. Om du inte planerar att fortsätta följer du dessa steg för att ta bort alla resurser som har skapats i den här snabbstarten i portalen.

Ta bort hela resursgruppen, inklusive den nya servern:
1. Leta reda på resursgruppen i portalen. Välj **Resursgrupper** på den vänstra menyn. Välj sedan namnet på resursgruppen, som i vårt exempel **myresourcegroup**.

2. Välj **Ta bort** på din resursgruppssida. Skriv namnet på resursgruppen, som vårt exempel **myresourcegroup**, i textrutan för att bekräfta borttagning. Välj **Ta bort**.

Ta bort bara den nyligen skapade servern:
1. Leta upp servern i portalen om du inte har den öppen. Välj **Alla resurser** på menyn till vänster. Sök sedan efter den server som du skapade.

2. Välj **Ta bort** på sidan **Översikt**.

    ![Knappen ”Ta bort”](./media/quickstart-create-database-portal/12-delete.png)

3. Bekräfta servernamnet som du vill ta bort och visa de databaser under den som påverkas. Skriv namnet på servern i textrutan, som vårt exempel **mypgserver 20170401**. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
