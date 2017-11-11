---
title: "Säkra din Azure SQL-databas | Microsoft Docs"
description: "Lär dig mer om teknik och nya funktioner för att skydda din Azure SQL-databas."
services: sql-database
documentationcenter: 
author: DRediske
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,security
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 06/28/2017
ms.author: daredis
ms.openlocfilehash: 90c03f1538197e1cd1c90165417a4ec74c9c5961
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="secure-your-azure-sql-database"></a>Skydda din Azure SQL-databas

SQL Database skyddar dina data genom att begränsa åtkomsten till databasen med hjälp av brandväggsregler, autentiseringsmekanismer som kräver att användare bevisar sin identitet och auktorisering till data via rollbaserade medlemskap och behörigheter, såväl som säkerhet på radnivå och dynamisk datamaskering.

Du kan förbättra skyddet av databasen mot obehöriga användare eller obehörig åtkomst med bara några få enkla steg. I den här kursen lär du dig: 

> [!div class="checklist"]
> * Konfigurera brandväggsregler på servernivå för servern i Azure-portalen
> * Konfigurera brandväggsregler på databasnivå för databasen med hjälp av SSMS
> * Ansluta till databasen med en säker anslutningssträng
> * Hantera användarnas åtkomst
> * Skydda dina data med kryptering
> * Aktivera granskning av SQL-databas
> * Aktivera hotidentifiering för SQL-databas

Om du inte har en Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Kontrollera att du har följande för att slutföra den här självstudiekursen:

- Installerat den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). 
- Installerade Microsoft Excel
- Skapat ett Azure SQL server och databas - finns [skapa en Azure SQL database i Azure portal](sql-database-get-started-portal.md), [skapa en enda Azure SQL-databas med hjälp av Azure CLI](sql-database-get-started-cli.md), och [skapa en enskild SQL Azure databasen med hjälp av PowerShell](sql-database-get-started-powershell.md). 

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Skapa en brandväggsregel på servernivå på Azure Portal

SQL-databaser är skyddade av en brandvägg i Azure. Som standard är alla anslutningar till servern och databaser på servern avvisade förutom anslutningar från andra Azure-tjänster. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](sql-database-firewall-configure.md).

Den säkraste konfigurationen är att ange 'tillåter åtkomst till Azure-tjänster, till OFF. Om du behöver ansluta till databasen från en virtuell dator i Azure eller molnet tjänst bör du skapa en [reserverade IP: N](../virtual-network/virtual-networks-reserved-public-ip.md) och ge endast reserverade IP-adress åtkomst via brandväggen. 

Följ dessa steg för att skapa en [SQL-databas brandväggsregel på servernivå](sql-database-firewall-configure.md) för servern för att tillåta anslutningar från en specifik IP-adress. 

> [!NOTE]
> Om du har skapat en exempeldatabas i Azure med hjälp av en av de föregående handledningar eller Snabbstart och utför den här kursen på en dator med samma IP-adress som det hade när du har gått igenom dessa självstudiekurser du kan hoppa över detta steg som du har redan Använ ated en brandväggsregel på servernivå.
>

1. Klicka på **SQL-databaser** från den vänstra menyn och klicka på den databas som du vill konfigurera brandväggen-regel för för den **SQL-databaser** sidan. På översiktssidan för din databas öppnas och visar fullständigt kvalificerade servernamnet (exempelvis **mynewserver 20170313.database.windows.net**) och innehåller alternativ för ytterligare konfiguration.

      ![brandväggsregler för server](./media/sql-database-security-tutorial/server-firewall-rule.png) 

2. Klicka på **Konfigurera serverns brandvägg** i verktygsfältet (se föregående bild). Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

3. Klicka på **lägga till klientens IP-Adressen** i verktygsfältet för att lägga till offentliga IP-adressen för den dator som är ansluten till portalen med eller ange brandväggsregeln manuellt och klickar sedan på **spara**.

      ![ange brandväggsregel för server](./media/sql-database-security-tutorial/server-firewall-rule-set.png) 

4. Klicka på **OK** och sedan på **X** för att stänga sidan **Brandväggsinställningar**.

Nu kan du ansluta till en databas på servern med den angivna IP-adress eller IP-adressintervall.

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-server om din IT-avdelning inte öppnar port 1433.
>

## <a name="create-a-database-level-firewall-rule-using-ssms"></a>Skapa en brandväggsregel på databasnivå med hjälp av SSMS

Brandväggsregler på databasnivå gör det möjligt att skapa olika brandväggsinställningar för olika databaser inom samma logiska server och skapa brandväggsregler som – vilket innebär att de följer databas under en [redundans](sql-database-geo-replication-overview.md) i stället för att lagras på SQLServer. Brandväggsregler på databasnivå kan bara vara konfigurerad med hjälp av Transact-SQL-uttryck och bara när du har konfigurerat den första brandväggsregeln på servernivå. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](sql-database-firewall-configure.md).

Följer de här stegen för att skapa en databasspecifika brandväggsregel.

1. Ansluta till databasen, till exempel med [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

2. Högerklicka på databasen som du vill lägga till en brandväggsregel för och klicka på i Object Explorer **ny fråga**. Ett tomt frågefönster öppnas som är anslutet till databasen.

3. Ändra IP-adressen till din offentliga IP-adress i frågefönstret och kör följande fråga:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

4. I verktygsfältet klickar du på **Execute** att skapa brandväggsregeln.

## <a name="view-how-to-connect-an-application-to-your-database-using-a-secure-connection-string"></a>Visa hur du ansluter ett program till databasen med en säker anslutningssträng

För att säkerställa en säker och krypterad anslutning mellan ett klientprogram och en SQL-databas, måste anslutningssträngen konfigureras för att:

- Begär en krypterad anslutning och
- Ska inte lita på servercertifikat. 

Detta upprättar en anslutning med Transport Layer Security (TLS) och minskar risken för man-in-the-middle-attacker. Du kan hämta korrekt konfigurerade anslutningssträngar för SQL-databas för klient som stöds drivrutiner från Azure portal enligt för ADO.net i den här skärmbilden.

1. Välj **SQL-databaser** i den vänstra menyn och klicka på din databas på den **SQL-databaser** sidan.

2. På den **översikt** för din databas, klickar du på **visa databasanslutningssträngar**.

3. Granska den fullständiga **ADO.NET**-anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/sql-database-security-tutorial/adonet-connection-string.png)

## <a name="creating-database-users"></a>Skapa databasanvändare

Innan du skapar användare, måste du först välja från en av två typer av autentisering som stöds av Azure SQL Database: 

**SQL-autentisering**, som använder användarnamn och lösenord för inloggning och användare som är endast giltig i kontexten för en viss databas i en logisk server. 

**Azure Active Directory-autentisering**, som använder identiteter som hanteras av Azure Active Directory. 

Om du vill använda [Azure Active Directory](./sql-database-aad-authentication.md) för att autentisera mot SQL-databasen, en ifylld Azure Active Directory måste finnas innan du kan fortsätta.

Följ dessa steg om du vill skapa en användare som använder SQL-autentisering:

1. Ansluta till databasen, till exempel med [SQL Server Management Studio](./sql-database-connect-query-ssms.md) med dina administratörsautentiseringsuppgifter för servern.

2. Högerklicka på databasen som du vill lägga till en ny användare på och klicka på i Object Explorer **ny fråga**. Ett tomt frågefönster öppnas som är ansluten till den valda databasen.

3. Skriv följande fråga i frågefönstret:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

4. I verktygsfältet klickar du på **Execute** att skapa användaren.

5. Som standard användaren kan ansluta till databasen, men har inte behörighet att läsa eller skriva data. Om du vill bevilja dessa behörigheter till den nyligen skapade användaren att köra följande två kommandon i ett nytt frågefönster

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

Det är bäst att skapa dessa administratörskonton på databasnivå att ansluta till databasen om du inte behöver köra administratörsåtgärder som skapar nya användare. Granska de [Azure Active Directory-kursen](./sql-database-aad-authentication-configure.md) på hur du autentiserar med hjälp av Azure Active Directory.


## <a name="protect-your-data-with-encryption"></a>Skydda dina data med kryptering

Azure SQL Database transparent datakryptering (TDE) krypteras automatiskt data i vila, utan ändringar till programmet åtkomst till krypterade databasen. För nya databaser är TDE aktiverad som standard. Följ dessa steg att aktivera TDE för din databas eller för att verifiera att TDE finns på:

1. Välj **SQL-databaser** i den vänstra menyn och klicka på din databas på den **SQL-databaser** sidan. 

2. Klicka på **Transparent datakryptering** att öppna konfigurationssidan för TDE.

    ![Transparent datakryptering](./media/sql-database-security-tutorial/transparent-data-encryption-enabled.png)

3. Om det behövs, ange **datakryptering** on och klicka på **spara**.

Krypteringsprocessen startas i bakgrunden. Du kan övervaka förloppet genom att ansluta till SQL-databas med hjälp av [SQL Server Management Studio](./sql-database-connect-query-ssms.md) genom att fråga kolumnen encryption_state för den `sys.dm_database_encryption_keys` vyn.

## <a name="enable-sql-database-auditing-if-necessary"></a>Aktivera SQL Database auditing, om det behövs

Azure SQL Database Auditing spårar databashändelser och skriver dem till en granskningslogg logga in i ditt Azure Storage-konto. Granskning hjälper dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan indikera potentiella säkerhetsöverträdelser. Följ dessa steg om du vill skapa en standard granskningsprincip för SQL-databasen:

1. Välj **SQL-databaser** i den vänstra menyn och klicka på din databas på den **SQL-databaser** sidan. 

2. I bladet inställningar väljer **granskning och Hotidentifiering**. Observera att servernivå granskning är diabled och att det finns en **visa inställningarna för** länk där du kan visa eller ändra servern granskningsinställningar från den här kontexten.

    ![Granskning bladet](./media/sql-database-security-tutorial/auditing-get-started-settings.png)

3. Om du vill aktivera en Audit typ (eller plats?) skiljer sig från den angivna på servernivå, aktivera **ON** granskning, och välj den **Blob** Granskningstypen. Om servern blobbgranskning är aktiverat, kommer att finnas databasgranskningen konfigurerats bredvid server Blob-granskning.

    ![Aktivera granskning](./media/sql-database-security-tutorial/auditing-get-started-turn-on.png)

4. Välj **lagringsinformation** att öppna bladet granska loggarna lagring. Välj Azure storage-konto där loggar sparas och kvarhållningsperioden som de gamla loggarna tas bort, klicka på **OK** längst ned. 

   > [!TIP]
   > Använda samma lagringskonto för alla granskad databaser för att få ut mesta av granskning rapporter mallarna.
   > 

5. Klicka på **Spara**.

> [!IMPORTANT]
> Om du vill anpassa granskade händelser kan du göra detta via PowerShell eller REST API - finns [SQL database auditing](sql-database-auditing.md) för mer information.
>

## <a name="enable-sql-database-threat-detection"></a>Aktivera hotidentifiering för SQL-databas

Hotidentifiering innehåller ett nytt lager av säkerhet som ger kunder möjlighet att identifiera och svara på potentiella hot allteftersom de sker genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter. Användare kan utforska misstänkta händelser med SQL Database Auditing för att avgöra om de härrör från ett försök att komma åt, bryta mot eller utnyttja data i databasen. Hotidentifiering gör det enkelt att adressen potentiella hot mot databasen utan att behöva vara en expert säkerhet eller hantera avancerade säkerhetsövervakning system.
Hotidentifiering identifierar till exempel vissa avvikande databasaktiviteter som indikerar potentiella försök med SQL injection. SQL injection är en av säkerhetsproblem för vanliga Web program på Internet, används till att attackera datadrivna program. Angripare kan utnyttja säkerhetsproblem för programmet att mata in skadlig SQL-instruktioner i programmet post fält för brott mot eller ändra data i databasen.

1. Navigera till bladet för konfiguration av SQL-databas som du vill övervaka. I bladet inställningar väljer **granskning och Hotidentifiering**.

    ![Navigeringsfönstret](./media/sql-database-security-tutorial/auditing-get-started-settings.png)
2. I den **granskning och Hotidentifiering** configuration bladet Stäng **ON** granskning, som visar inställningarna för identifiering av hot.

3. Aktivera **på** hotidentifiering.

4. Konfigurera en lista över e-postmeddelanden som ska ta emot säkerhetsaviseringar vid identifiering av avvikande databasaktiviteter.

5. Klicka på **spara** i den **Auditing & Threat detection** bladet för att spara den nya eller uppdaterade granskning och hot princip.

    ![Navigeringsfönstret](./media/sql-database-security-tutorial/td-turn-on-threat-detection.png)

    Om avvikande databasaktiviteter identifieras, får du ett e-postmeddelande vid identifiering av avvikande databasaktiviteter. E-postmeddelandet ger information om händelsen misstänkta säkerhet inklusive avvikande aktiviteter, databasens namn, servernamn och tidpunkt för händelsen. Dessutom ger information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minska den potentiella risken till databasen. Nästa steg vägleder dig genom så här gör du får sådana ett e-postmeddelande:

    ![Threat detection e-post](./media/sql-database-threat-detection-get-started/4_td_email.png)

6. I e-post, klickar du på den **Azure SQL-granskning logg** länkar, som startar Azure-portalen och visa de granskning register vid ungefär samma tidpunkt misstänkta händelsen.

    ![Granskningsposter](./media/sql-database-threat-detection-get-started/5_td_audit_records.png)

7. Klicka på granskningsposter om du vill visa mer information om misstänkt databasaktiviteter, till exempel SQL-instruktionen, fel orsak och klient-IP.

    ![Registrera information](./media/sql-database-security-tutorial/6_td_audit_record_details.png)

8. I bladet granskning poster klickar du på **öppna i Excel** för att öppna en förkonfigurerad excel-mall som du vill importera och kör djupare analys av granskningsloggen vid ungefär samma tidpunkt misstänkta händelsen.

    > [!NOTE]
    > I Excel 2010 eller senare, Power Query och **snabb kombinera** inställningen är obligatorisk.

    ![Öppna poster i Excel](./media/sql-database-threat-detection-get-started/7_td_audit_records_open_excel.png)

9. Så här konfigurerar du den **snabb kombinera** - inställningen i den **POWER QUERY** menyfliksområdet fliken **alternativ** att visa dialogrutan Alternativ. Välj avsnittet sekretess och väljer det andra alternativet - ”Ignorera sekretessnivåerna och förbättra prestanda”:

    ![Excel snabb kombinera](./media/sql-database-threat-detection-get-started/8_td_excel_fast_combine.png)

10. Kontrollera att parametrarna i inställningar på fliken är korrekt och välj ”uppgifter” menyfliksområdet och klicka på knappen 'Uppdatera alla' för att läsa in SQL-granskningsloggar.

    ![Excel-parametrar](./media/sql-database-threat-detection-get-started/9_td_excel_parameters.png)

11. Resultatet visas i den **SQL granskningsloggar** blad som gör det möjligt att köra djupare analys av avvikande aktiviteter som upptäckts och minimera effekten av säkerhetshändelse i ditt program.


## <a name="next-steps"></a>Nästa steg
I kursen får du lärt dig att bättre skydda databasen mot obehöriga användare eller obehörig åtkomst med bara några få enkla steg.  Du har lärt dig att: 

> [!div class="checklist"]
> * Konfigurera brandväggsregler för servern och eller-databas
> * Ansluta till databasen med en säker anslutningssträng
> * Hantera användarnas åtkomst
> * Skydda dina data med kryptering
> * Aktivera granskning av SQL-databas
> * Aktivera hotidentifiering för SQL-databas

Gå vidare till nästa kurs att lära dig hur du implementerar en fördelade databas.

> [!div class="nextstepaction"]
>[Implementera en geodistribuerad databas](sql-database-implement-geo-distributed-database.md)

