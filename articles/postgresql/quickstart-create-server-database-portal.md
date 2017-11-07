---
title: "Azure-portalen: skapa en Azure-databas för PostgreSQL-server | Microsoft Docs"
description: "Snabbstart hjälper till att skapa och hantera en Azure-databas för PostgreSQL-servern med hjälp av Azure portal användargränssnittet."
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
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Skapa en Azure-databas för PostgreSQL-server i Azure-portalen

Azure PostgreSQL-databas är en hanterad tjänst som används för att köra, hantera och skala högtillgänglig PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure-databas för PostgreSQL-servern om fem minuter med hjälp av Azure portal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå till den [portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

En Azure Database för PostgreSQL-server skapas med en definierad uppsättning [compute- och lagringsresurser](./concepts-compute-unit-and-storage.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md).

Om du vill skapa en Azure-databas för PostgreSQL-server gör du följande:
1. Klicka på knappen **Nytt** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser** > **Azure-databas för PostgreSQL**.

    ![Alternativet ”Azure-databas för PostgreSQL”](./media/quickstart-create-database-portal/1-create-database.png)

3. Fyll i detaljformuläret för den nya servern med följande information (se föregående bild):

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    servernamn |*mypgserver-20170401*|Ett unikt namn som identifierar din Azure-databas för PostgreSQL-servern. Domännamnet *postgres.database.azure.com* läggs till i namnet på den server som du anger. Servern kan innehålla endast små bokstäver, siffror och bindestreck (-). Det måste innehålla minst 3 och 63 tecken.
    Prenumeration|Din prenumeration|Azure-prenumerationen som du vill använda för servern. Om du har flera prenumerationer väljer du den prenumeration som du använder debiteras för resursen.
    Resursgrupp|*myresourcegroup*| Namn på en ny resursgrupp eller en befintlig från prenumerationen.
    inloggning för serveradministratör |*mylogin*| Egna inloggningskonto som ska användas när du ansluter till servern. Inloggningsnamnet för administratören får inte vara **azure_superuser,** **azure_pg_admin,** **admin,** **administratör,** **rot,** **Gäst,** eller **offentliga.** Det går inte att starta med **pg_**.
    Lösenord |Ditt val | Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv.).
    Plats|Regionen som är närmast dina användare| Den plats som ligger närmast dina användare.
    PostgreSQL-version|Den senaste versionen| Den senaste versionen om du inte har särskilda krav.
    Prisnivå | **Basic**, **50 Compute-enheter**, **50 GB** | Tjänstnivå och prestandanivå för den nya databasen. Välj **prisnivå**. Välj sedan den **grundläggande** fliken. Välj sedan längst till vänster i den **Compute enheter** skjutreglaget för att justera värdet till den minsta uppsättningen som är tillgängliga för denna Snabbstart. Välj för att spara den prisnivå val av **OK**. Mer information finns på följande skärmbild. 
    Fäst vid instrumentpanelen | Markera | Möjliggör enkel spårning av din server på instrumentpanelssidan främre i portalen.

    > [!IMPORTANT]
    > Inloggning för serveradministratör och lösenord som du anger här krävs för att logga in på servern och dess databaser senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

    ![Fönstret ”prisnivå”](./media/quickstart-create-database-portal/2-service-tier.png)

4. Välj **Skapa** för att etablera servern. Etableringen kan ta upp till 20 minuter.

5. I verktygsfältet, väljer du den **meddelanden** symbol att övervaka processen för distribution.

    ![Fönstret ”meddelanden”](./media/quickstart-create-database-portal/3-notifications.png)
   
  Som standard en **postgres** databas skapas under din server. Den [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) databasen är en standarddatabas som är avsedd för användning av användare, verktyg och program från tredje part. 

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure-databas för PostgreSQL skapar en brandvägg på servernivå. Det förhindrar att externa program och verktyg från att ansluta till servern och alla databaser på servern, om du inte skapar en regel för att öppna brandväggen för specifika IP-adresser. 

1. Leta upp servern när distributionen är klar. Om det behövs kan du söka efter den. Välj exempelvis på menyn till vänster **alla resurser**. Skriv namnet på servern, till exempel exemplet **mypgserver 20170401**, för att söka efter nya servern. Välj namnet på servern från listan över resultat. **Översikt**-sidan för din server öppnas och innehåller alternativ ytterligare konfiguration.
 
    ![Servern namnsökning](./media/quickstart-create-database-portal/4-locate.png)

2. På serversidan väljer du **Anslutningssäkerhet**.

    ![Inställningen ”anslutningssäkerhet”](./media/quickstart-create-database-portal/5-firewall-2.png)

3. Under den **regler i brandväggen** rubriken i den **Regelnamn** kolumn, väljer sedan tomma textrutan för att börja skapa brandväggsregeln. 

    Denna Snabbstart vi tillåter du att alla IP-adresser till servern. Fyll i textrutan i varje kolumn med följande värden:

    Regelnamn | Start-ip | Slut-ip 
    ---|---|---
    AllowAllIps | 0.0.0.0 | 255.255.255.255

4. Välj **Spara** i det övre verktygsfältet på sidan **Anslutningssäkerhet**. Vänta tills meddelandet visas som talar om att anslutningen säkerhetsuppdateringen har slutförts innan du fortsätter.

    > [!NOTE]
    > Anslutningar till din Azure Database för PostgreSQL-server kommunicerar via port 5432. Om du försöker ansluta från ett företagsnätverk kanske utgående trafik via port 5432 inte av ditt nätverks brandvägg. I så fall, kan du ansluta till servern om din IT-avdelning öppnar port 5432.
    >

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När du skapar din Azure-databas för PostgreSQL-server, en standarddatabas med namnet **postgres** skapas. För att ansluta till databasservern måste hela servern namn och admin inloggningsuppgifter. Du kan ha antecknat de här värdena tidigare i snabbstartsartikeln. Om du inte hittar du servern information om namn och logga in på servern **översikt** sida i portalen.

Öppna serverns **Översikt**-sida. Anteckna den **servernamn** och **serverinloggningsnamnet för admin**. Håller markören över varje fält och kopiera symbolen visas till höger om texten. Välj symbolen kopia som behövs för att kopiera värdena.

 ![Sidan ”Overview”](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-by-using-psql-in-cloud-shell"></a>Ansluta till PostgreSQL-databas med hjälp av psql i molnet Shell

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Först använder vi psql-kommandoradsverktyget för att visa hur man ansluter till servern. Du kan använda en webbläsare och Azure Cloud Shell som beskrivs här utan att behöva installera ytterligare programvara. Om psql-verktyget är installerat lokalt på din dator kan du ansluta därifrån också.

1. I det övre navigeringsfönstret, Välj symbolen terminal för att öppna molnet Shell.

   ![Azure Cloud Shell terminal symbol](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Molnet Shell öppnas i webbläsaren, där du kan ange Bash shell-kommandon.

   ![Molnet Shell Bash Kommandotolken](./media/quickstart-create-database-portal/8-bash.png)

3. Ansluta till en databas i din Azure-databas för PostgreSQL-servern genom att skriva psql kommandoraden i molnet Shell-Kommandotolken.

    Att ansluta till en Azure-databas för PostgreSQL-server med den [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) -verktyget, Använd följande format:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Till exempel ansluter följande kommando till en exempelserver:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    --host | servernamn | Värdet för servernamnet som du använde när du skapade Azure-databas för tidigare PostgreSQL-server. Exempel-servern som visas är **mypgserver 20170401.postgres.database.azure.com.** Använd det fullständigt kvalificerade domännamnet (**\*. postgres.database.azure.com**) som visas i exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. 
    --port | 5432 | Porten som ska användas när du ansluter till Azure-databasen för PostgreSQL-servern. 
    --username | Serverinloggningsnamnet för admin |Server-inloggning administratörsanvändarnamnet som du angav när du skapade Azure-databas för tidigare PostgreSQL-server. Om du inte kommer ihåg ditt användarnamn, följer du stegen i föregående avsnitt för att hämta anslutningsinformation om. Formatet är *username@servername*.
    --dbname | *postgres* | Standard systemgenererade databasnamnet som har skapats för den första anslutningen. Senare kan skapa du en egen databas.

    När du har kört kommandot psql med egna parametervärden, uppmanas du att ange lösenord för serveradministratören. Lösenordet är detsamma som du angav när du skapade servern. 

    psql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    lösenord | Ditt administratörslösenord | Skrivna lösenord tecknen visas inte på bash-fråga. När du har angett alla tecken, Välj den **RETUR** för att autentisera och ansluta.

    När du har anslutit frågar verktyget psql postgres skriver du sql-kommandon. I den första anslutningen utdatan, kan en varning visas eftersom psql i molnet Shell kanske en annan version än Azure-databasen för PostgreSQL-serverversionen. 
    
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
    > Om brandväggen inte är konfigurerad för att tillåta IP-adressen för molnet Shell uppstår följande fel:
    > 
    > ”psql: allvarligt fel: ingen pg_hba.conf post för värd” 138.91.195.82 ”användare” mylogin ”databas” postgres ”, SSL på allvarligt fel: SSL-anslutning krävs. Ange alternativ för SSL och försök igen.
    > 
    > Åtgärda felet genom att kontrollera att serverkonfigurationen matchar stegen i avsnittet ”Konfigurera en brandväggsregel på servernivå” i den här artikeln.

4. Skapa en tom databas i kommandotolken genom att skriva följande kommando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    Kommandot kan ta några minuter att slutföra. 

5. I Kommandotolken kör du följande kommando för att växla anslutningar till databasen som har skapats **mypgsqldb**:
    ```bash
    \c mypgsqldb
    ```

6. Typen `\q`, och välj sedan den **RETUR** för att avsluta psql. När du är klar kan du stänga molnet Shell.

Du är nu ansluten till Azure-databasen för PostgreSQL-server och du har skapat en tom databas. Fortsätt till nästa avsnitt för att ansluta med ett annat gemensamma verktyg, pgAdmin.

## <a name="connect-to-the-postgresql-database-by-using-pgadmin"></a>Ansluta till PostgreSQL-databas med hjälp av pgAdmin

Att ansluta till Azure PostgreSQL-server med GUI-verktyget pgAdmin:
1. Öppna pgAdmin programmet på klientdatorn. Du kan installera pgAdmin från den [pgAdmin webbplats](http://www.pgadmin.org/).

2. På instrumentpanelssidan under den **snabblänkar** väljer den **Lägg till ny Server** symbolen.

3. I den **skapa - Server** dialogrutan den **allmänna** ange ett unikt namn för servern som **Azure PostgreSQL Server**.

    ![Fliken ”Allmänt”](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. I den **skapa - Server** dialogrutan den **anslutning** , använda inställningarna som anges, och välj sedan **spara**.

   ![Fliken ”anslutning”](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    Värdnamn/-adress | servernamn | Värdet för servernamnet som du använde när du skapade Azure-databas för tidigare PostgreSQL-server. Vårt exempel server är **mypgserver 20170401.postgres.database.azure.com.** Använd det fullständigt kvalificerade domännamnet (**\*. postgres.database.azure.com**) som visas i exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. 
    Port | 5432 | Porten som ska användas när du ansluter till Azure-databasen för PostgreSQL-servern. 
    Databasen för underhåll | *postgres* | Standard systemgenererade databasnamnet.
    Användarnamn | Serverinloggningsnamnet för admin | Server-inloggning administratörsanvändarnamnet som du angav när du skapade Azure-databas för tidigare PostgreSQL-server. Om du inte kommer ihåg användarnamnet följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. Formatet är *username@servername*.
    Lösenord | Ditt administratörslösenord | Lösenordet du angav när du skapade servern tidigare i den här snabbstarten.
    Roll | Lämna tomt | Det finns inget behov av att ange ett rollnamn nu. Lämna fältet tomt.
    SSL-läge | Krävs | Som standard skapas alla Azure PostgreSQL-servrar med SSL framtvinga aktiverat. Om du vill inaktivera SSL framtvinga finns [framtvinga SSL](./concepts-ssl-connection-security.md).
    
5. Välj **Spara**.

6. I den **webbläsare** fönstret till vänster, expandera den **servrar** nod. Markera din server, till exempel **Azure PostgreSQL Server**. Klicka för att ansluta till den.

7. Expandera servernoden och expandera sedan **Databaser** under den. I listan ska innehålla din befintliga *postgres* databasen och alla nyligen skapade användaren databasen som **mypgsqldb**, som skapades i föregående avsnitt. Observera att du kan skapa flera databaser per server med Azure-databas för PostgreSQL.

8. Högerklicka på **databaser**, Välj den **skapa** -menyn och välj sedan **databasen**.

9. Ange ett databasnamn som du väljer i den **databasen** fält som **mypgsqldb**som visas i exemplet.

10. Välj den **ägare** för databasen i listrutan. Välj din server admin inloggningsnamn, till exempel det här exemplet **mylogin**.

11. Välj **spara** att skapa en ny tom databas.

12. I den **webbläsare** rutan finns i databasen som du skapade i listan över databaser under namnet på servern.

    ![Fönstret ”webbläsaren”](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Rensa resurser
Du kan rensa de resurser som du skapade i Snabbstart i ett av två sätt. Du kan ta bort den [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) som innehåller alla resurser i resursgruppen. Ta bort den enda serverresursen om du vill behålla de andra resurserna intakt.

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Rensa inte upp resurserna som du skapade i den här snabbstarten om du tänker fortsätta att arbeta med efterföljande snabbstarter. Om du inte planerar att fortsätta, Följ dessa steg för att ta bort de resurser som har skapats av denna Snabbstart i portalen.

Ta bort hela resursgruppen, inklusive den nya servern:
1. Leta upp din resursgrupp i portalen. Välj på menyn till vänster **resursgrupper**. Välj sedan namnet på resursgruppen, som i exempel **myresourcegroup**.

2. Välj **Ta bort** på din resursgruppssida. Skriv namnet på resursgruppen, som i exempel **myresourcegroup**, i textrutan för att bekräfta borttagningen. Välj **Ta bort**.

Ta bort nyskapade servern:
1. Leta upp din server i portalen om du inte har öppnat. Välj på menyn till vänster **alla resurser**. Sök sedan efter den server som du skapade.

2. Välj **Ta bort** på sidan **Översikt**.

    ![Knappen ”Ta bort”](./media/quickstart-create-database-portal/12-delete.png)

3. Kontrollera servernamnet som du vill ta bort och visa databaserna under den som påverkas. Skriv namnet på servern i textrutan, till exempel exemplet **mypgserver 20170401**. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
