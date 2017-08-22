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
ms.date: 08/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 8bba5a97fdc4bbc15fe996ee68daf5b796d1bfac
ms.contentlocale: sv-se
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Skapa en Azure Database för PostgreSQL i Azure-portalen

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database för PostgreSQL-server med Azure Portal på ungefär fem minuter.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Öppna webbläsaren och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

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
    inloggning för serveradministratör |*mylogin*| Skapa ett eget inloggningskonto att använda när du ansluter till servern. Inloggningsnamnet för administratören får inte vara azure_superuser, azure_pg_admin, admin, administrator, root, guest, eller public och får inte börja med pg_.
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

1.  Leta upp servern när distributionen är klar. Om det behövs kan du söka efter den. Klicka exempelvis på **Alla resurser** på vänster meny och skriv in servernamnet (till exempel *mypgserver-20170401*) för att söka efter den nya servern. Klicka på servernamnet i sökresultatlistan. **Översikt**-sidan för din server öppnas och innehåller alternativ ytterligare konfiguration.
 
    ![Azure Database för PostgreSQL – sök efter servernamn](./media/quickstart-create-database-portal/4-locate.png)

2.  På serversidan väljer du **Anslutningssäkerhet**. 
    ![Azure Database för PostgreSQL – Skapa brandväggsregel](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  Under **Brandväggsregler** klickar du i den tomma textrutan i kolumnen **Regelnamn** och börjar skapa brandväggsregeln. 

    I den här snabbstartsguiden ska vi tillåta alla ip-adresser till servern genom att fylla i textrutan i varje kolumn med följande värden:

    Regelnamn | Start-ip | Slut-ip 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. I det övre verktygsfältet på sidan Anslutningssäkerhet klickar du på **Spara**. Vänta en stund tills du ser meddelandet om att uppdateringen av anslutningssäkerhet har slutförts innan du fortsätter.

    > [!NOTE]
    > Anslutningar till din Azure Database för PostgreSQL-server kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din server om inte din IT-avdelning öppnar port 5432.
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
    --host | *servernamn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för PostgreSQL. Exempelservern som visas är mypgserver-20170401.postgres.database.azure.com. Använd det fullständiga domännamnet (\*.postgres.database.azure.com) som i det här exemplet. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg namnet på servern. 
    --port | **5432** | Använd alltid port 5432 när du ansluter till Azure Database för PostgreSQL. 
    --username | *inloggning för serveradministratör* |Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för PostgreSQL. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg användarnamnet.  Formatet är *username@servername*.
    --dbname | **postgres** | Använd det systemgenererade standarddatabasnamnet *postgres* för den första anslutningen. Senare kan du skapa en egen databas.

    När du har kört psql-kommandot med egna parametervärden uppmanas du att ange lösenordet för serveradministratören. Det här är det lösenord som du angav när du skapade servern. 

    psql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    lösenord | *ditt administratörslösenord* | Tänk på att det angivna lösenordet inte visas i bash-kommandotolken. Tryck på Retur när du har skrivit alla tecken för att autentisera och ansluta.

    När du är ansluten visas i psql-verktyget en postgres-kommandotolk där du skriver sql-kommandon. Vid den första anslutningen kanske en varning visas eftersom psql i Azure Cloud Shell kan vara en annan version än Azure Databas för PostgreSQL-serverversionen. 
    
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
    > Om brandväggen inte är konfigurerad att tillåta IP-adressen för Azure Cloud Shell uppstår följande fel:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL:  SSL connection is required. Please specify SSL options and retry.
    > 
    > Lös felet genom att se till att serverkonfigurationen matchar stegen i avsnittet *Konfigurera en brandväggsregel på servernivå* i artikeln.

4.  Skapa en tom databas i kommandotolken genom att skriva följande kommando:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    Kommandot kan ta en stund att slutföra. 

5.  I prompten kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**.
    ```bash
    \c mypgsqldb
    ```

6.  Skriv \q och tryck sedan på RETUR för att avsluta psql. När du är klar kan du stänga Azure Cloud Shell.

Nu har du anslutit till Azure Database för PostgreSQL och skapat en tom användardatabas. Fortsätt till nästa avsnitt för att ansluta med hjälp av ett annat vanligt verktyg pgAdmin.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Anslut till PostgreSQL-databasen med pgAdmin

Om du vill ansluta till Azure PostgreSQL-servern med GUI-verktyget _pgAdmin_
1.  Starta _pgAdmin_-programmet på din klientdator. Du kan installera _pgAdmin_ från http://www.pgadmin.org/.
2.  Klicka på ikonen **Lägg till ny server** från avsnittet **Snabblänkar** i mitten av instrumentpanelssidan.
3.  I dialogrutan **skapa – server** i fliken **allmänt**, anger du ett unikt eget namn för servern, som **Azure PostgreSQL-server**.
![pgAdmin-verktyget – skapa – server](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  I dialogrutan **skapa – server** i fliken **anslutning**, använder du de angivna inställningarna och klickar på **spara**.
   ![pgAdmin – skapa – server](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    Värdnamn/-adress | *servernamn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för PostgreSQL. Exempelservern som visas är mypgserver-20170401.postgres.database.azure.com. Använd det fullständiga domännamnet (\*.postgres.database.azure.com) som i det här exemplet. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg namnet på servern. 
    Port | **5432** | Använd alltid port 5432 när du ansluter till Azure Database för PostgreSQL.  
    Underhållsdatabas | **postgres** | Använd det systemgenererade standardnamnet för databasen, *postgres*.
    Användarnamn | *inloggning för serveradministratör* | Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för PostgreSQL. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg användarnamnet. Formatet är *username@servername*.
    Lösenord | *ditt administratörslösenord* |  Det lösenord du angav när du skapade servern tidigare i den här snabbstarten.
    Roll | *lämna tomt* | Du behöver inte ange ett rollnamn nu. Lämna fältet tomt.
    SSL-läge | Kräv | Som standard skapas alla Azure PostgreSQL-servrar med SSL tvingande aktiverat. Om du vill inaktivera SSL tvingande, se informationen i [tvingande SSL](./concepts-ssl-connection-security.md).
    
5.  Klicka på **Spara**.
6.  I webbläsarens vänstra fönster expanderar du noden **Servrar**. Välj din server, till exempel **Azure PostgreSQL Server**, och klicka för att ansluta till den.
7. Expandera servernoden och expandera sedan **Databaser** under den. Listan ska innehålla din befintliga *postgres*-databas och alla eventuella nyligen skapade användardatabaser, som *mypgsqldb*, som vi skapade i föregående avsnitt. Observera att du kan skapa flera databaser per server med Azure Database för PostgreSQL.
8. Högerklicka på **Databaser**, välj menyn **Skapa** och klicka på **Databas**.
9.  Skriv valfritt databasnamn i fältet **Databas**, till exempel *mypgsqldb* som i exemplet. 
10. Välj **Ägare** för databasen från den nedrullningsbara listan. Välj ditt inloggningsnamn som administratör för servern, som vårt exempel *mylogin*.
10. Klicka på **Spara** för att skapa en tom databas.
11. I rutan **Webbläsare** ser du den databas du skapade i listan Databaser under servernamnet.
 ![pgAdmin – skapa – databas](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Rensa resurser
Rensa bland de resurser du skapade i snabbstarten, antingen genom att ta bort [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md), som innehåller alla resurser i resursgruppen, eller genom att ta bort serverresursen om du vill behålla de andra resurserna intakta.

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter, ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure Portal.

Så här tar du bort hela resursgruppen, inklusive den nya servern:
1.  Leta reda på resursgruppen på Azure Portal. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på resursgruppen, som vårt exempel **myresourcegroup**.
2.  Klicka på **Ta bort** på din resursgruppssida. Skriv sedan namnet på resursgruppen, som vårt exempel **myresourcegroup**, i textrutan för att bekräfta borttagning och klicka sedan på **Ta bort**.

Eller gör så här om du vill ta bort den nyligen skapade servern:
1.  Leta upp din server i Azure Portal om du inte har den öppen. Klicka på **Alla resurser** på den vänstra menyn i Azure Portal och leta upp den server som du nyss skapade.
2.  Klicka på knappen **Ta bort** i den övre panelen på sidan **Översikt**.
![Azure Database för PostgreSQL – Ta bort server](./media/quickstart-create-database-portal/12-delete.png)
3.  Bekräfta servernamnet som du vill ta bort och visa de databaser under den som påverkas. Skriv namnet på servern i textrutan, som vårt exempel **mypgserver 20170401** och klicka sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)

