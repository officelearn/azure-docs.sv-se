---
title: "Azure-portalen: skapa en Azure Database för PostgreSQL | Microsoft Docs"
description: "Snabbstartsguide för att skapa och hantera Azure Database för PostgreSQL-server med användargränssnittet för Azure-portalen."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/04/2017
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: e89058a500aeb542ae4c7dc6bb4a9b9ae54db7f2
ms.contentlocale: sv-se
ms.lasthandoff: 08/09/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Skapa en Azure Database för PostgreSQL i Azure-portalen

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL-server med Azure-portalen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Skapa en Azure Database för PostgreSQL

En Azure Database för PostgreSQL-server skapas med en definierad uppsättning [compute- och lagringsresurser](./concepts-compute-unit-and-storage.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md).

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1.  Klicka på knappen **New** (Nytt) längst upp till vänster i Azure Portal.
2.  Välj **databaser** från sidan **Nytt** och välj **Azure Database för PostgreSQL** från sidan **databaser**.
 ![Azure Database för PostgreSQL – Skapa databasen](./media/quickstart-create-database-portal/1-create-database.png)

3.  Fyll i detaljformuläret för den nya server med följande information, som det visas i föregående bil:

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    servernamn |*mypgserver-20170401*|Välj ett unikt namn för Azure Database för PostgreSQL-server. Domännamnet *postgres.database.azure.com* läggs till i det servernamn du anger som program ska ansluta till. Ditt servernamn får bara innehålla gemener, siffror och bindestreck och måste innehålla mellan 3 och 63 tecken.
    Prenumeration|*Din prenumeration*|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer, väljer du lämplig prenumeration som resursen ska debiteras till.
    Resursgrupp|*myresourcegroup*| Du kan skapa ett nytt resursgruppnamn eller använda ett befintligt namn i prenumerationen.
    inloggning för serveradministratör |*mylogin*| Skapa ett eget inloggningskonto som ska användas vid anslutning till servern. Inloggningsnamnet för administratören får inte vara azure_superuser, azure_pg_admin, admin, administrator, root, guest, eller public och får inte börja med pg_.
    Lösenord |*Ditt val* | Skapa ett nytt lösenord för serverns administratörskonto. Måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv).
    Plats|*Regionen som ligger närmast dina användare*| Välj den plats som är närmast dina användare.
    PostgreSQL-version|*Välj den senaste versionen*| Välj den senaste versionen såvida du inte har särskilda krav.
    Prisnivå | **Basic**, **50 Compute-enheter** **50 GB** | Klicka på **Prisnivå** för att ange tjänstenivå och prestandanivå för den nya databasen. Välj Basic-nivån på fliken längst upp. Klicka längst ut till vänster på skjutreglaget för Compute-enheter och justera värdet till minsta tillgängliga mängd för den här snabbstarten. Spara den valda prisnivån genom att klicka på **OK**. Se följande skärmbild.
    | Fäst vid instrumentpanelen | Markera | Markera alternativet **Fäst vid instrumentpanel** för att tillåta enkel spårning av servern på den främre instrumentpanelen på Azure Portal.

  > [!IMPORTANT]
  > Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

    ![Azure Database för PostgreSQL – välj prisnivå](./media/quickstart-create-database-portal/2-service-tier.png)

4.  Klicka på **Skapa** för att etablera servern. Det tar några minuter, högst 20, att slutföra etableringen.

5.  Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.
 ![Azure Database för PostgreSQL – se meddelanden](./media/quickstart-create-database-portal/3-notifications.png)
   
  Som standard skapas **postgres**-databasen under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. 

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database för PostgreSQL-tjänsten skapar en brandvägg på server-nivå. Brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern, om inte en brandväggsregel konfigureras som öppnar brandväggen för specifika IP-adresser. 

1.  Leta upp servern när distributionen är klar. Om det behövs kan du söka efter den. Klicka exempelvis på **Alla resurser** på vänster meny och skriv in servernamnet (till exempel mypgserver-20170401) för att söka efter den nya servern. Klicka på servernamnet i sökresultatlistan. Sidan Översikt för servern öppnas, och där ser du alternativ för ytterligare konfiguration.
 
    ![Azure Database för PostgreSQL – sök efter server ](./media/quickstart-create-database-portal/4-locate.png)

2.  På serversidan väljer du **Anslutningssäkerhet**. 
    ![Azure Database för PostgreSQL – Skapa brandväggsregel](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  Under **Brandväggsregler** klickar du i den tomma textrutan i kolumnen **Regelnamn** och börjar skapa brandväggsregeln. 

    I den här snabbstartsguiden ska vi tillåta alla ip-adresser till servern genom att fylla i textrutan i varje kolumn med följande värden:

    Regelnamn | Start-ip | Slut-ip 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. I det övre verktygsfältet på sidan Anslutningssäkerhet klickar du på **Spara**. Klicka sedan på **X**för att stänga sidan Anslutningssäkerhet.

    > [!NOTE]
    > Azure PostgreSQL-servern kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 5432.
  >

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När vi skapade vår Azure Database för PostgreSQL-server, skapades även en standarddatabas med namnet **postgres**. Du måste komma ihåg det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan ha antecknat de här värdena tidigare i snabbstartsartikeln. I annat fall hittar du enkelt servernamnet och inloggningsuppgifterna på sidan Översikt på Azure Portal.

1. Öppna serverns **Översikt**-sida. Anteckna **servernamn** och **inloggningsnamnet för serveradministratören**.
    Håll markören över varje fält så att kopieringssymbolen visas till höger om texten. Klicka på kopieringssymbolen för att kopiera värdena.

 ![Azure Database för PostgreSQL – inloggning för serveradministratör](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Anslut till PostgreSQL-databasen med psql i Cloud Shell

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Först använder vi psql-kommandoradsverktyget för att visa hur man ansluter till servern.  Du kan använda en webbläsare och Azure Cloud Shell enligt nedan utan att behöva installera ytterligare programvara. Om psql-verktyget är installerat lokalt på din dator kan du ansluta därifrån också.

1. Starta Azure Cloud Shell via terminalikonen överst i navigeringsfönstret.

   ![Azure Database för PostgreSQL – Azure Cloud Shell-terminalikonen](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Azure Cloud Shell öppnas i webbläsaren så att du kan skriva bash shell-kommandon.

   ![Azure Database för PostgreSQL – Azure Shell Bash-prompten](./media/quickstart-create-database-portal/8-bash.png)

3. I Cloud Shell-kommandotolken ansluter du till din Azure Database för PostgreSQL-server genom att skriva in psql-kommandoraden i den gröna kommandotolken.

    Följande format används för att ansluta till en Azure Database för PostgreSQL-server med [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget:
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Till exempel ansluter följande kommando till en exempelserver:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    --host | *servernamn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för PostgreSQL. Exempelservern som visas är mypgserver-20170401.postgres.database.azure.com. Använd det fullständiga domännamnet (\*.postgres.database.azure.com) som i det här exemplet. Se föregående avsnitt med anslutningsinformation om du inte kommer ihåg namnet på servern. 
    --port | **5432** | Använd alltid port 5432 när du ansluter till Azure Database för PostgreSQL. 
    --username | *inloggning för serveradministratör* |Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för PostgreSQL. Se föregående avsnitt med anslutningsinformation om du inte kommer ihåg användarnamnet.  Formatet är *username@servername*.
    --dbname | **postgres** | Använd det systemgenererade standarddatabasnamnet *postgres* för den första anslutningen. Senare kan du skapa en egen databas.

    När du har kört psql-kommandot med egna parametervärden uppmanas du att ange lösenordet för serveradministratören. Det här är det lösenord som du angav när du skapade servern. 

    psql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    lösenord | *ditt administratörslösenord* | Tänk på att det angivna lösenordet inte visas i bash-kommandotolken. Tryck på Retur när du har skrivit alla tecken för att autentisera och ansluta.

4.  När du är ansluten till servern skapar du en tom databas i kommandotolken genom att skriva följande kommando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

5.  I prompten kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**.
    ```bash
    \c mypgsqldb
    ```

6.  Skriv \q och tryck sedan på RETUR för att avsluta psql. Nu kan du stänga Azure Cloud Shell.

Nu har du anslutit till Azure Database för PostgreSQL och skapat en tom användardatabas. Fortsätt till nästa avsnitt för att ansluta med hjälp av ett annat vanligt verktyg pgAdmin.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Anslut till PostgreSQL-databasen med pgAdmin

Om du vill ansluta till Azure PostgreSQL-servern med GUI-verktyget _pgAdmin_
1.  Starta _pgAdmin_-programmet på din klientdator. Du kan installera _pgAdmin_ från http://www.pgadmin.org/.
2.  Välj **Lägg till ny Server** från menyn **snabblänkar**.
3.  I dialogrutan **skapa – server** i fliken **allmänt**, anger du ett unikt eget namn för servern, som **Azure PostgreSQL-server**.
![pgAdmin-verktyget – skapa – server](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  I dialogrutan **skapa – server** i fliken **anslutning**, använder du de angivna inställningarna och klickar på **spara**.
   ![pgAdmin – skapa – server](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Värddatornamn/adress**: mypgserver-20170401.postgres.database.azure.com 
        - Fullständigt kvalificerat servernamn.
    - **Port:**  5432
        - Portnummer som används av den här databasservern är 5432.
    - **Underhållsdatabas**: postgres 
        - Systemgenererat standardnamn för databasen.
    - **Användarnamn:** mylogin@mypgserver-20170401 
        - Inloggning för serveradministratören (user@mypgserver) står tidigare i den här snabbstarten.
    - **Lösenord**: det lösenord du angav när du skapade servern tidigare i den här snabbstarten.
    - **SSL-läge**: Kräv
        - Som standard skapas alla Azure PostgreSQL-servrar med SSL tvingande aktiverat. Om du vill inaktivera SSL tvingande, se informationen i [tvingande SSL](./concepts-ssl-connection-security.md).
5.  Klicka på **Spara**.
6.  I webbläsarens vänstra fönster, expanderar du **servergrupper**. Välj din server **Azure PostgreSQL-server**.
7.  Välj den **server** du anslöt till och välj sedan **databaser** under den. 
8.  Högerklicka på **databaser** för att skapa en databas.
9.  Välj ett databasnamn **mypgsqldb** och ägaren för den som inloggning för serveradministratören **mylogin**.
10. Klicka på **spara** för att skapa en tom databas.
11. I **webbläsaren**, expanderar du **servrar**. Expandera servern som du skapade och se databasen **mypgsqldb** under den.
 ![pgAdmin – skapa – databas](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Rensa resurser
Rensa upp alla resurser du skapade i snabbstarten genom att ta bort [Azure-resursgruppen](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter, ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.

1.  I den vänstra menyn i Azure-portalen, klickar du på **Resursgrupper** och sedan på **myresourcegroup**.
2.  På sidan med dina resursgrupper, klickar du på **ta bort**, skriver in **myresourcegroup** i textrutan och klickar sedan på ta bort.

Om du bara vill ta bort den nyligen skapade servern:
1.  I den vänstra menyn i Azure-portalen, klickar du på PostgreSQL-servrar och söker efter den server som du nyss skapade
2.  I översiktssidan, klickar du på ta bort-knappen i övre panelen ![Azure Database för PostgreSQL – ta bort servern](./media/quickstart-create-database-portal/12-delete.png)
3.  Kontrollera servernamnet som du vill ta bort och visa de databaser under den som påverkas. Skriv in **mypgserver-20170401** i textrutan och klicka sedan på ta bort.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)

