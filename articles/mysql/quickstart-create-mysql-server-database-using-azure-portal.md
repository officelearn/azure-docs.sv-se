---
title: "Snabbstart: Skapa Azure-databas för MySQL-server – Azure Portal | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder Azure Portal för att snabbt skapa ett exempel på en Azure-databas för MySQL-server på fem minuter."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: sv-se
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Skapa en Azure Database för MySQL med Azure Portal
Azure Database för MySQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Den här snabbstarten visar hur du skapar en Azure Database för MySQL-server med Azure Portal på ungefär fem minuter. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure
Öppna webbläsaren och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-azure-database-for-mysql-server"></a>Skapa en Azure-databas för MySQL-servern
En Azure Database för MySQL-server skapas med en definierad uppsättning [compute- och lagringsresurser](./concepts-compute-unit-and-storage.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md).

Följ de här stegen för att skapa en Azure Database för MySQL-server:

1. Klicka på knappen **New** (Nytt) längst upp till vänster i Azure Portal.

2. Välj **Databaser** från sidan **Nytt** och välj **Azure Database för MySQL** från sidan **Databaser**. Du kan också hitta tjänsten genom att skriva **MySQL** i sökrutan för nya sidor.
![Azure Portal – ny – databas – MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Fyll i detaljformuläret för den nya server med följande information, som det visas i föregående bil:

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning** 
    ---|---|---
    servernamn | myserver4demo | Välj ett unikt namn för Azure Database för MySQL-server. Domännamnet *mysql.database.azure.com* läggs till i det servernamn du anger som program ska ansluta till. Ditt servernamn får bara innehålla gemener, siffror och bindestreck och måste innehålla mellan 3 och 63 tecken.
    Prenumeration | Din prenumeration | Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer, väljer du lämplig prenumeration som resursen ska debiteras till.
    Resursgrupp | myresourcegroup | Du kan skapa ett nytt resursgruppnamn eller använda ett befintligt namn i prenumerationen.
    inloggning för serveradministratör | myadmin | Skapa ett eget inloggningskonto att använda när du ansluter till servern. Inloggningsnamnet för administratören får inte vara 'azure_superuser', 'admin', 'administrator', 'root', 'guest' eller 'public'.
    Lösenord | *Ditt val* | Skapa ett nytt lösenord för serverns administratörskonto. Måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv).
    Bekräfta lösenord | *Ditt val*| Bekräfta administratörslösenordet.
    Plats | *Regionen som ligger närmast dina användare*| Välj den plats som är närmast dina användare eller andra Azure-program.
    Version | *Välj den senaste versionen*| Välj den senaste versionen såvida du inte har särskilda krav.
    Prisnivå | **Basic**, **50 Compute-enheter** **50 GB** | Klicka på **Prisnivå** för att ange tjänstenivå och prestandanivå för den nya databasen. Välj **Basnivå** på fliken högst upp. Klicka längst ut till vänster på skjutreglaget för **Compute-enheter** och justera värdet till minsta tillgängliga mängd för den här snabbstarten. Spara den valda prisnivån genom att klicka på **OK**. Se följande skärmbild.
    Fäst vid instrumentpanelen | Markera | Markera alternativet **Fäst vid instrumentpanel** för att tillåta enkel spårning av servern på den främre instrumentpanelen på Azure Portal.

    > [!IMPORTANT]
    > Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.
    > 

    ![Azure Portal – skapa MySQL genom att tillhandahålla de formuläruppgifter som krävs](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Klicka på **Skapa** för att etablera servern. Det tar några minuter, högst 20, att slutföra etableringen.
   
5.  Klicka på **Aviseringar** (klockikonen) i verktygsfältet för att övervaka distributionsprocessen.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database för MySQL-tjänsten skapar en brandvägg på servernivå. Brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern, om inte en brandväggsregel konfigureras som öppnar brandväggen för specifika IP-adresser. 

1.  Leta upp servern när distributionen är klar. Om det behövs kan du söka efter den. Klicka exempelvis på **Alla resurser** på vänster meny och skriv in servernamnet (till exempel *myserver4demo*) för att söka efter den nya servern. Klicka på servernamnet i sökresultatlistan. **Översikt**-sidan för din server öppnas och innehåller alternativ ytterligare konfiguration.

2. På serversidan väljer du **Anslutningssäkerhet**.

3.  Under **Brandväggsregler** klickar du i den tomma textrutan i kolumnen **Regelnamn** och börjar skapa brandväggsregeln. 

    I den här snabbstartsguiden ska vi tillåta alla ip-adresser till servern genom att fylla i textrutan i varje kolumn med följande värden:

    Regelnamn | Start-ip | Slut-ip 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. I det övre verktygsfältet på sidan **Anslutningssäkerhet** klickar du på **Spara**. Vänta en stund tills du ser meddelandet om att uppdateringen av anslutningssäkerhet har slutförts innan du fortsätter.

    > [!NOTE]
    > Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 3306 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din server om inte din IT-avdelning öppnar port 3306.
    > 

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen
Du måste komma ihåg det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan ha antecknat de här värdena tidigare i snabbstartsartikeln. I annat fall hittar du enkelt servernamnet och inloggningsuppgifterna på sidan **Översikt** eller **Egenskaper** för servern på Azure Portal.

1. Öppna serverns **Översikt**-sida. Anteckna **servernamn** och **inloggningsnamnet för serveradministratören**. 
    Håll markören över varje fält så att kopieringssymbolen visas till höger om texten. Klicka på kopieringssymbolen för att kopiera värdena.

    I det här exemplet är servernamnet *myserver4demo.mysql.database.azure.com* och inloggningen för serveradministratören är *myadmin@myserver4demo*.

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>Ansluta till MySQL med kommandoradsverktyget mysql
Det finns ett antal program du kan använda för att ansluta till Azure Database för MySQL-servern. Först använder vi [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)-kommandoradsverktyget för att visa hur man ansluter till servern.  Du kan använda en webbläsare och Azure Cloud Shell enligt nedan utan att behöva installera ytterligare programvara. Om mysql-verktyget är installerat lokalt på din dator kan du ansluta därifrån också.

1. Starta Azure Cloud Shell via terminalikonen ( >_ ) överst till höger på webbsidan Azure Portal.

2. Azure Cloud Shell öppnas i webbläsaren så att du kan skriva bash shell-kommandon.

    ![Kommandotolk – exempel på mysql-kommandorad](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. I Cloud Shell-prompten, ansluter du till din Azure Database för MySQL-server genom att skriva in mysql-kommandoraden i den gröna prompten.

    Följande format används för att ansluta till en Azure Database för MySQL-server med mysql-verktyget:
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Till exempel ansluter följande kommando till vår exempelserver:
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql-parameter |Föreslaget värde|Beskrivning
    ---|---|---
    --host | *servernamn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för MySQL. Exempelservern som visas är myserver4demo.mysql.database.azure.com. Använd det fullständiga domännamnet (\*.mysql.database.azure.com) som i det här exemplet. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg namnet på servern. 
    --användare | *inloggning för serveradministratör* |Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för MySQL. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg användarnamnet.  Formatet är *username@servername*.
    --lösenord | *vänta på uppmaning* | Du uppmanas att ange lösenord när du har angett kommandot. När du uppmanas ange lösenord ska du ange samma lösenord som när du skapade servern.  Tänk på att det angivna lösenordet inte visas i bash-kommandotolken när du skriver. Tryck på Retur när du har skrivit alla tecken för att autentisera och ansluta.

   När du är ansluten visar mysql-verktyget visar en `mysql>`-kommandotolk där du kan skriva kommandon. 

    Exempel på mysql-utdata:
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Om brandväggen inte är konfigurerad att tillåta IP-adressen för Azure Cloud Shell uppstår följande fel:
    >
    > ERROR 2003 (28000): Client with IP address 123.456.789.0 is not allowed to access the server. (Klienten med IP-adress 123.456.789.0 har inte tillstånd att ansluta till servern.)
    >
    > Lös felet genom att se till att serverkonfigurationen matchar stegen i avsnittet *Konfigurera en brandväggsregel på servernivå* i artikeln.

4. Visa serverstatus så att du ser att anslutningen fungerar. Skriv `status` vid prompten mysql> när anslutningen är upprättad.
    ```sql
    status
    ```

   > [!TIP]
   > Fler kommandon finns i [referenshandboken för MySQL 5.7 – kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Skapa en tom databas i kommandotolken mysql> genom att skriva följande kommando:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    Kommandot kan ta en stund att slutföra. 

    Du kan skapa en eller flera databaser på en Azure Database för MySQL-server. Du kan välja att skapa en databas per server om du vill använda dig av samtliga resurser, eller skapa flera databaser som får dela på resurserna. Det finns ingen gräns för hur många databaser som kan skapas, men flera databaser delar samma serverresurser. 

6. Visa en lista med databaserna i kommandotolken mysql> genom att skriva följande kommando:

    ```sql
    SHOW DATABASES;
    ```

7.  Skriv `\q` och tryck sedan på RETUR för att avsluta mysql-verktyget. När du är klar kan du stänga Azure Cloud Shell.

Nu har du anslutit till Azure Database för MySQL och skapat en tom användardatabas. Fortsätta till nästa avsnitt ska upprepas en liknande Övning för att ansluta till samma server med ett annat gemensamma verktyg, MySQL-arbetsstationen.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Anslut till servern med verktyget MySQL Workbench GUI
Om du vill ansluta till Azure MySQL-servern med GUI-verktyget MySQL Workbench:

1.  Starta programmet MySQL Workbench på klientdatorn. Du kan ladda ned och installera MySQL Workbench [här](https://dev.mysql.com/downloads/workbench/).

2.  I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

    ![konfigurera ny anslutning](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **Inställning** | **Föreslaget värde** | **Fältbeskrivning** |
    |---|---|---|
    |   Anslutningsnamn | Demoanslutning | Ange ett namn på anslutningen. |
    | Anslutningsmetod | Standard (TCP/IP) | Standard (TCP/IP) är tillräckligt. |
    | Värdnamn | *servernamn* | Ange det värde för servernamn som användes när du tidigare skapade Azure Database för MySQL. Exempelservern som visas är myserver4demo.mysql.database.azure.com. Använd det fullständiga domännamnet (\*.mysql.database.azure.com) som i det här exemplet. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg namnet på servern.  |
    | Port | 3306 | Använd alltid port 3306 när du ansluter till Azure Database för MySQL. |
    | Användarnamn |  *inloggning för serveradministratör* | Ange det användarnamn för serveradministratörsinloggning som användes när du tidigare skapade Azure Database för MySQL. Vår användarnamn i exemplet är myadmin@myserver4demo. Följ anvisningarna i föregående avsnitt för att hitta anslutningsinformation om du inte kommer ihåg användarnamnet. Formatet är *username@servername*.
    | Lösenord | ditt lösenord | Klicka på knappen Spara i valvet... för att spara lösenordet. |

    Klicka på **Testanslutning** för att testa om alla parametrar är rätt konfigurerade. Spara anslutningen genom att klicka på OK. 

    > [!NOTE]
    > SSL tillämpas som standard på din server vilket kräver extra konfiguration av anslutningar. Se [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Konfigurera SSL-anslutning i ditt program för att säkert ansluta till Azure Database för MySQL).  Om du vill inaktivera SSL för den här snabbstartsguiden öppnar du Azure Portal, klickar på sidan Anslutningssäkerhet och inaktiverar alternativet Framtvinga SSL-anslutning.

## <a name="clean-up-resources"></a>Rensa resurser
Rensa bland de resurser du skapade i snabbstarten, antingen genom att ta bort [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md), som innehåller alla resurser i resursgruppen, eller genom att ta bort serverresursen om du vill behålla de andra resurserna intakta.

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter, ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.
>

Så här tar du bort hela resursgruppen, inklusive den nya servern:
1.  Leta reda på resursgruppen på Azure Portal. Klicka på **Resursgrupper** på den vänstra menyn i Azure Portal och sedan på namnet på resursgruppen, som vårt exempel **myresourcegroup**.
2.  Klicka på **Ta bort** på din resursgruppssida. Skriv sedan namnet på resursgruppen, som vårt exempel **myresourcegroup**, i textrutan för att bekräfta borttagning och klicka sedan på **Ta bort**.

Eller gör så här om du vill ta bort den nyligen skapade servern:
1.  Leta upp din server i Azure Portal om du inte har den öppen. Klicka på **Alla resurser** på den vänstra menyn i Azure Portal och leta upp den server som du nyss skapade.
2.  Klicka på knappen **Ta bort** i den övre panelen på sidan **Översikt**.
![Azure Database för MySQL – Ta bort server](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Bekräfta servernamnet som du vill ta bort och visa de databaser under den som påverkas. Skriv namnet på servern i textrutan, som vårt exempel **myserver4demo**, och klicka sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa din första Azure-databas för MySQL](./tutorial-design-database-using-portal.md)


