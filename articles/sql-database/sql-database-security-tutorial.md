---
title: Skydda din Azure SQL-databas | Microsoft Docs
description: Lär dig olika tekniker och funktioner för att skydda din Azure SQL-databas.
services: sql-database
author: DRediske
manager: craigg
ms.service: sql-database
ms.custom: mvc,security
ms.topic: tutorial
ms.date: 09/07/2018
ms.author: daredis
ms.reviewer: vanto
ms.openlocfilehash: 220cda999c21e6b56546fe6d5dbe350675ad9691
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542405"
---
# <a name="secure-your-azure-sql-database"></a>Skydda din Azure SQL-databas

SQL Database skyddar dina data genom: 
- Begränsad åtkomst till databasen med hjälp av brandväggsregler 
- Autentiseringsmekanismer där identiteten kontrolleras
- Databehörigheter via rollbaserade medlemskap och behörigheter 
- Säkerhet på radnivå
- Dynamisk datamaskning

SQL Database har även avancerade funktioner för övervakning, granskning och hotidentifiering. 

Med några få enkla steg kan du förbättra databasens skydd mot obehöriga användare och obehörig åtkomst. I den här självstudien får du lära du dig att: 

> [!div class="checklist"]
> * Skapa brandväggsregler på servernivå på Azure-portalen.
> * Skapa brandväggsregler på databasnivå för din databas med SQL Server Management Studio.
> * Ansluta till databasen med en säker anslutningssträng.
> * Hantera användarkonton.
> * Skydda dina data med kryptering
> * Aktivera SQL Database-granskning.
> * Aktivera hotidentifiering i SQL Database

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har gjort följande för att kunna genomföra den här självstudien:

- Installerat den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Installerat Microsoft Excel
- Skapat en Azure SQL-server och -databas – Se [Skapa en Azure SQL-databas på Azure Portal](sql-database-get-started-portal.md), [Skapa en enskild Azure SQL-databas med Azure CLI](sql-database-cli-samples.md) och [Skapa en enskild Azure SQL-databas med PowerShell](sql-database-powershell-samples.md). 

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

SQL-databaser skyddas av en brandvägg i Azure. Som standard avvisas alla anslutningar till servern och databaserna på servern, med undantag för anslutningar från andra Azure-tjänster. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](sql-database-firewall-configure.md).

Det är säkrast att ställa in ”Tillåt åtkomst till Azure-tjänster” till AV. Om du behöver ansluta till databasen från en virtuell Azure-dator eller molntjänst skapar du en [reserverad IP](../virtual-network/virtual-networks-reserved-public-ip.md) och tillåter att endast den reserverade IP-adressen får åtkomst via brandväggen. 

Följ de här stegen och skapa en [brandväggsregel på servernivå för SQL Database](sql-database-firewall-configure.md) så att servern tillåter anslutningar från en specifik IP-adress. 

> [!NOTE]
> Om du har skapat en exempeldatabas i Azure i en av de föregående självstudierna eller snabbstarterna, och genomför den här självstudien på en dator med samma IP-adress som när du gick igenom de självstudierna, kan du hoppa över det här steget eftersom du redan har skapat en brandväggsregel på servernivå.
>

1. Klicka på **SQL-databaser** på menyn till vänster och klicka på databasen som du vill konfigurera brandväggsregeln för på sidan för **SQL-databaser**. Översiktssidan för databasen öppnas, där du kan se det fullständigt kvalificerade servernamnet (som **mynewserver-20170313.database.windows.net**) och alternativ för ytterligare konfiguration.

      ![brandväggsregler för server](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Klicka på **Konfigurera serverns brandvägg** i verktygsfältet (se föregående bild). Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

3. Klicka på **Lägg till klient-IP** i verktygsfältet för att lägga till den offentliga IP-adressen till datorn som den är ansluten till portalen med, eller ange brandväggsregeln manuellt och klicka på **Spara**.

      ![ange brandväggsregel för server](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Klicka på **OK** och sedan på **X** för att stänga sidan **Brandväggsinställningar**.

Nu kan du ansluta till valfri databas på servern med IP-adressen eller IP-adressintervallet som angetts.

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-server om din IT-avdelning inte öppnar port 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Skapa en brandväggsregel på databasnivå med SQL Server Management Studio

Med brandväggsregler på databasnivå kan du skapa olika brandväggsinställningar för olika databaser inom samma logiska server och brandväggsregler som är portabla, vilket innebär att de följer databasen under [redundans](sql-database-geo-replication-overview.md) i stället för att lagras på SQL-servern. Brandväggsregler på databasnivå kan bara konfigureras med instruktioner för Transact-SQL, och bara efter att du har konfigurerat den första brandväggsregeln på servernivå. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](sql-database-firewall-configure.md).

Följ de här stegen och skapa en databasspecifik brandväggsregel.

1. Anslut till din databas med exempelvis [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. I Object Explorer högerklickar du på databasen du vill lägga till en brandväggsregel för och klickar på **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.

3. Ändra IP-adressen till din offentliga IP-adress i frågefönstret och kör följande fråga:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. Klicka på **Kör** i verktygsfältet för att skapa brandväggsregeln.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Så här ansluter du ett program till databasen med en säker anslutningssträng

För att kunna säkerställa en säker och krypterad anslutning mellan ett klientprogram och SQL Database så måste anslutningssträngen konfigureras för att:

- begära en krypterad anslutning, och
- inte lita på servercertifikatet. 

Då upprättas en anslutning med TLS (Transport Layer Security) och risken för MITM-attacker (man-in-the-middle) minskar. Du kan hämta korrekt konfigurerade anslutningssträngar för SQL Database för klientdrivrutiner som stöds från Azure-portalen enligt den här skärmbilden för ADO.net. Information om TLS och anslutningar finns i avsnittet om [TLS-specifika överväganden](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

1. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**.

2. På databasens **översiktssida** klickar du på **Visa databasanslutningssträngar**.

3. Granska den fullständiga **ADO.NET**-anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Skapa databasanvändare

Innan du skapar användare måste du först välja en av två autentiseringstyper som stöds av Azure SQL Database: 

**SQL-autentisering**, som använder användarnamn och lösenord för inloggning, och användarna är endast behöriga i kontexten av en specifik databas inom en logisk server. 

**Azure Active Directory-autentisering**, som använder identiteter som hanteras av Azure Active Directory. 

Om du vill använda [Azure Active Directory](./sql-database-aad-authentication.md) för att autentisera mot SQL Database, måste det finnas en ifylld Azure Active Directory innan du fortsätter.

Följ de här stegen om du vill skapa en användare med SQL-autentisering:

1. Anslut till databasen med exempelvis [SQL Server Management Studio](./sql-database-connect-query-ssms.md) och använd dina autentiseringsuppgifter som serveradministratör.

2. I Object Explorer högerklickar du på databasen där du vill lägga till en ny användare och klickar på **Ny fråga**. Ett tomt frågefönster öppnas som är anslutet till den valda databasen.

3. Skriv följande fråga i frågefönstret:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. Klicka på **Kör** i verktygsfältet för att skapa användaren.

5. Som standard kan användaren ansluta till databasen, men har inte behörighet att läsa eller skriva data. Om du vill bevilja de här behörigheterna till den nyligen skapade användaren kör du följande två kommandon i ett nytt frågefönster.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Det är bäst att skapa dessa icke-administratörskonton på databasnivå för att ansluta till databasen, såvida du inte behöver köra administratörsåtgärder som att skapa nya användare. Gå igenom [självstudien för Azure Active Directory](./sql-database-aad-authentication-configure.md) och lär dig om autentisering med Azure Active Directory.


## <a name="protect-your-data-with-encryption"></a>Skydda dina data med kryptering

Med transparent datakryptering för Azure SQL Database krypteras vilande data automatiskt, utan att det krävs ändringar i programmet som har åtkomst till den krypterade databasen. Transparent datakryptering (TDE) är som standard aktiverat för nya databaser. Följ de här stegen för att aktivera transparent datakryptering för databasen eller för att verifiera att det är aktiverat:

1. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 

2. Klicka på **Transparent datakryptering** så öppnas konfigurationssidan för transparent datakryptering.

    ![Transparent datakryptering](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Om det behövs väljer du PÅ för **Datakryptering** och klickar på **Spara**.

Krypteringsprocessen startas i bakgrunden. Du kan övervaka förloppet genom att ansluta till SQL Database med [SQL Server Management Studio](./sql-database-connect-query-ssms.md) och köra en fråga mot kolumnen encryption_state i vyn [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017). Tillståndet 3 anger att databasen är krypterad. 

## <a name="enable-sql-database-auditing-if-necessary"></a>Aktivera SQL Database-granskning om det behövs

Azure SQL Database-granskning spårar databashändelser och skriver händelserna i en granskningslogg i ditt Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på potentiella säkerhetsöverträdelser. Följ de här stegen om du vill skapa en standardprincip för granskning för SQL-databasen:

1. Välj **SQL-databaser** på den vänstra menyn och klicka på databasen på sidan **SQL-databaser**. 

2. Välj **Granskning och hotidentifiering** på bladet Inställningar. Observera att granskning på servernivå är inaktiverat och det finns en länk för att **visa serverinställningar** där du kan visa och ändra serverns granskningsinställningar i den här kontexten.

    ![Bladet Granskning](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Om du vill aktivera en annan granskningstyp (eller -plats) än den som specificerats på servernivå ställer du in Granskning till **PÅ** och väljer typen **Blobgranskning**. Om blobgranskning är aktiverat på servern används databasens konfigurerade granskning tillsammans med serverns blobgranskning.

    ![Aktivera granskning](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Välj **Lagringsinformation** så öppnas lagringsbladet i granskningsloggen. Välj Azure-lagringskontot där loggarna sparas, samt kvarhållningsperioden, efter vilken de gamla loggarna tas bort, och klicka sedan på **OK** längst ned. 

   > [!TIP]
   > Använd samma lagringskonto för alla granskade databaser för att få ut mesta möjliga av granskningsrapportmallarna.
   > 

5. Klicka på **Spara**.

> [!IMPORTANT]
> Du har möjlighet att anpassa de granskade händelserna genom PowerShell eller REST API. Mer information finns på sidan om [SQL-databasgranskning](sql-database-auditing.md).
>

## <a name="enable-sql-database-threat-detection"></a>Aktivera hotidentifiering i SQL Database

Hotidentifiering ger ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar om avvikande aktiviteter. Användare kan utforska misstänkta händelser med hjälp av SQL Database-granskning för att avgöra om händelsen orsakades av ett försök att komma åt, tränga in i eller utnyttja data i databasen. Hotidentifiering gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhetsexpert eller hantera avancerade säkerhetsövervakningssystem.
Hotidentifiering identifierar till exempel vissa avvikande databasaktiviteter som kan tyda på försök till SQL-inmatning. SQL-inmatning är ett av de vanligt förekommande säkerhetsproblemen för webbprogram på Internet, vars syfte är att attackera datadrivna program. Angripare kan utnyttja sårbarheter i program för att mata in skadliga SQL-instruktioner i programmets inmatningsfält i syfte att tränga in eller ändra data i databasen.

1. Gå till konfigurationsbladet i SQL-databasen du vill övervaka. Välj **Granskning och hotidentifiering** på bladet Inställningar.

    ![Navigeringsfönster](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. På konfigurationsbladet **Granskning och hotidentifiering** väljer du **PÅ** för granskningen så att inställningarna för hotidentifiering visas.

3. Välj **PÅ** för hotidentifiering.

4. Konfigurera listan över e-postadresser som ska få säkerhetsaviseringar när avvikande databasaktiviteter identifieras.

5. Klicka på **Spara** på bladet **Granskning och hotidentifiering** för att spara den nya eller uppdaterade principen för granskning och hotidentifiering.

    ![Navigeringsfönster](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Om avvikande databasaktiviteter identifieras får du ett aviseringsmeddelande via e-post när dessa inträffar. I e-postmeddelandet får du information om den misstänkta händelsen, inklusive typen av avvikande aktivitet, databasens namn, servernamn och tidpunkt för händelsen. Dessutom ingår information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen. I följande steg går vi igenom vad du bör göra om du får ett sådant e-postmeddelande:

    ![E-post om hotidentifiering](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. Klicka på länken för **Azure SQL-granskningsloggen** i e-postmeddelandet. Då öppnas Azure-portalen och visar de relevanta granskningsposterna vid tidpunkten för den misstänkta händelsen.

    ![Granskningsposter](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Klicka på granskningsposter om du vill visa mer information om misstänkta databasaktiviteter, som en SQL-instruktion, en felorsak eller ett klient-IP.

    ![Information om granskningspost](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. På bladet med granskningsposter klickar du på **Öppna i Excel** om du vill öppna en förkonfigurerad Excel-mall för att importera och köra djupare analyser av granskningsloggen vid tiden för den misstänkta händelsen.

    > [!NOTE]
    > I Excel 2010 eller senare krävs Power Query och inställningen **Kombinera snabbt**.

    ![Öppna poster i Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Konfigurera inställningen **Kombinera snabbt** i menyfliksområdet **POWER QUERY** genom att välja **Alternativ** så öppnas alternativdialogrutan. Välj avsnittet Sekretess och välj det andra alternativet ”Ignorera sekretessnivåerna och förbättra eventuellt prestandan”:

    ![Kombinera snabbt i Excel](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. När du vill läsa in SQL-granskningsloggar kontrollerar du att parametrarna på inställningsfliken är korrekta. Sedan väljer du menyfliken Data och klickar på knappen Uppdatera alla.

    ![Parametrar i Excel](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Resultatet visas på sidan för **SQL-granskningsloggar** och du kan köra analyser av identifierade avvikande händelser och minimera säkerhetshändelsens inverkan på ditt program.


## <a name="next-steps"></a>Nästa steg
Du har nu lärt dig hur du kan förbättra databasens skydd mot obehöriga användare och obehörig åtkomst med bara några få enkla steg.  Du har lärt dig att: 

> [!div class="checklist"]
> * Konfigurera brandväggsregler för din server och databas.
> * Ansluta till databasen med en säker anslutningssträng.
> * Hantera användarkonton.
> * Skydda dina data med kryptering
> * Aktivera SQL Database-granskning.
> * Aktivera hotidentifiering i SQL Database

I nästa självstudie får du lära dig hur du implementerar en geodistribuerad databas.

> [!div class="nextstepaction"]
>[Implementera en geodistribuerad databas](sql-database-implement-geo-distributed-database.md)

