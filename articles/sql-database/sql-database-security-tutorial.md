---
title: Skydda en enkel databas i Azure SQL Database | Microsoft Docs
description: Lär dig olika tekniker och funktioner för att skydda en enkel databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: e0311174303fc91767d3f99e6db05927b25aea05
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051670"
---
# <a name="tutorial-secure-a-single-database"></a>Självstudier: Skydda en enkel databas

Azure SQL Database skyddar data i en enkel SQL-databas genom att låta dig:

- Begränsa åtkomst med hjälp av brandväggsregler
- Använd autentiseringsmekanismer som kräver identitet
- Använda auktorisering med rollbaserade medlemskap och behörigheter
- Aktivera säkerhetsfunktioner

> [!NOTE]
> En Azure SQL-databas på en hanterad instans skyddas med nätverkssäkerhetsregler och privata slutpunkter enligt beskrivningen i [Hanterad Azure SQL Database-instans](sql-database-managed-instance-index.yml) och [anslutningsarkitektur](sql-database-managed-instance-connectivity-architecture.md).

Du kan förbättra din databassäkerhet med bara några få enkla steg. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> - Skapa brandväggsregler på servernivå och databasnivå
> - Konfigurera en administratör för Azure Active Directory (AD Azure)
> - Hantera användaråtkomst med SQL-autentisering, Azure AD-autentisering och säkra anslutningssträngar
> - Aktivera säkerhetsfunktioner som hotskydd, granskning, datamaskering och kryptering

Mer information finns i artiklarna [Säkerhetsöversikt för Azure SQL Database](/azure/sql-database/sql-database-security-index) och [Funktioner](sql-database-security-overview.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra den här självstudien behöver du följande:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- En Azure SQL-server och -databas
    - Skapa dem med [Azure-portalen](sql-database-get-started-portal.md), [CLI](sql-database-cli-samples.md), eller [PowerShell](sql-database-powershell-samples.md)

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Om du vill utföra stegen med den här självstudien loggar du in på [Azure-portalen](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Skapa brandväggsregler

SQL-databaser skyddas av brandväggar i Azure. Som standard avvisas alla anslutningar till servern och databaserna, med undantag för anslutningar från andra Azure-tjänster. Om du vill veta mer läser du [Azure SQL Database-brandväggsregler på servernivå och databasnivå](sql-database-firewall-configure.md).

Ställ in **Tillåt åtkomst till Azure-tjänster** på **AV** för den säkraste konfigurationen. Skapa sedan en [reserverad IP (klassisk distribution)](../virtual-network/virtual-networks-reserved-public-ip.md) för resursen som behöver anslutas, till exempel en virtuell Azure-dator eller molntjänst, och tillåt endast att den IP-adressen får åtkomst via brandväggen. Om du använder distributionsmodellen [resurshanteraren](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) krävs en dedikerad offentlig IP-adress för varje resurs.

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till Azure SQL Database-servern om inte din administratör öppnar port 1433.

### <a name="set-up-server-level-firewall-rules"></a>Konfigurera brandväggsregler på servernivå

Brandväggsregler på servernivå gäller för alla databaser på samma logiska server.

Konfigurera en brandväggsregel på servernivå:

1. I Azure-portalen väljer du **SQL-databaser** på den vänstra menyn och klickar på databasen på sidan **SQL-databaser**.

    ![brandväggsregler för server](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Se till att kopiera det fullständigt kvalificerade servernamnet (till exempel *yourserver.database.windows.net*) för användning senare i självstudien.

1. På **översiktssidan** väljer du **Konfigurera serverns brandvägg**. Sidan **Brandväggsinställningar** för databasservern öppnas.

    1. Välj **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med regeln kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall. Välj **Spara**.

    ![ange brandväggsregel för server](./media/sql-database-security-tutorial/server-firewall-rule2.png)

    1. Välj **OK** och stäng sidan **Brandväggsinställningar**.

Nu kan du ansluta till valfri databas på servern med IP-adressen eller IP-adressintervallet som angetts.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster under **Tillåt åtkomst till Azure-tjänster**. Välj **AV** om du vill inaktivera åtkomsten för alla Azure-tjänster.

### <a name="setup-database-level-firewall-rules"></a>Konfigurera brandväggsregler på databasnivå

Brandväggsregler på databasnivå gäller endast för enskilda databaser. Dessa regler är portabla och följer databasen under en serverredundans. Brandväggsregler på databasnivå kan bara konfigureras med instruktioner för Transact-SQL (T-SQL), och bara efter att du har konfigurerat en brandväggsregel på servernivå.

Så här konfigurerar du en brandväggsregel på databasnivå:

1. Anslut till databasen med exempelvis [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. I **Object Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. Ändra IP-adressen till din offentliga IP-adress i frågefönstret och lägg till instruktionen:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Välj **Kör** i verktygsfältet för att skapa brandväggsregeln.

> [!NOTE]
> Du kan också skapa en brandväggsregel på servernivå i SSMS med kommandot [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current), men du måste vara ansluten till *master*-databasen.

## <a name="create-an-azure-ad-admin"></a>Skapa en Azure AD-administratör

Kontrollera att du använder den lämpliga hanterade domänen med Azure Active Directory (AD). Välj AD-domänen längst upp till höger i Azure-portalen. Den här processen bekräftar att samma prenumeration används för både Azure AD och SQL Server som är värd för din Azure SQL-databas eller datalagret.

   ![choose-ad](./media/sql-database-security-tutorial/8choose-ad.png)

Så här anger du Azure AD-administratör:

1. I Azure-portalen går du till **SQL-serversidan** och väljer **Active Directory-administratör**. Sedan väljer du **Konfigurera administratör**.

    ![Välj active directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Du måste vara antingen ”Företagsadministratör” eller ”Global administratör” för att utföra den här uppgiften.

1. På sidan **Lägg till administratör** söker du efter och väljer AD-användaren eller gruppen och därefter **Välj**. Alla medlemmar och grupper för din Active Directory visas, och nedtonade poster stöds inte som Azure AD-administratörer. Se avsnittet om [Azure AD-funktioner och begränsningar](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![välja administratör](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Rollbaserad åtkomstkontroll (RBAC) gäller enbart för portalen och sprids inte till SQL Server.

1. Längst upp på sidan **Active Directory-administratör** väljer du **Spara**.

    Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan **Active Directory-administratör**.

> [!NOTE]
> När Azure AD-administratören konfigureras får namnet för den nya administratören (användare eller grupp) inte redan finnas som en SQL Server-autentiseringsanvändare i *master*-databasen. Om den gör det misslyckas installationen och ändringarna återställs, vilket indikerar att det redan finns ett administratörsnamn. Eftersom SQL Server-autentiseringsanvändaren inte är en del av Azure AD misslyckas alla försök att ansluta användaren med Azure AD-autentisering.

Information om hur du konfigurerar Azure AD finns i:

- [Integrera dina lokala identiteter med Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Lägga till ditt eget domännamn i Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure har nu stöd för federation med Windows Server AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrera Azure AD-katalogen](../active-directory/fundamentals/active-directory-administer.md)
- [Hantera Azure AD med hjälp av PowerShell](/powershell/azure/overview?view=azureadps-2.0)
- [Portar och protokoll som krävs för hybrididentitet](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Hantera databasåtkomst

Hantera åtkomst till databasen genom att lägga till användare i databasen eller tillåta användaråtkomst med säkra anslutningssträngar. Anslutningssträngar är användbara för externa program. Mer information finns i [Azure SQL-åtkomstkontroll](sql-database-control-access.md) och [AD-autentisering](sql-database-aad-authentication.md).

Välj autentiseringstyp för databasen för att lägga till användare:

- **SQL-autentisering**, som använder användarnamn och lösenord för inloggning, och användarna är endast behöriga i kontexten av en specifik databas inom servern

- **Azure AD-autentisering**, använder identiteter som hanteras av Azure AD

### <a name="sql-authentication"></a>SQL-autentisering

Så här lägger du till en användare med SQL-autentisering:

1. Anslut till databasen med exempelvis [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. I **Object Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. Skriv följande kommando i frågefönstret:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Välj **Kör** i verktygsfältet för att skapa användaren.

1. Som standard kan användaren ansluta till databasen, men har inte behörighet att läsa eller skriva data. Om du vill bevilja de här behörigheterna kör du följande två kommandon i ett nytt frågefönster:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Skapa icke-administratörskonton på databasnivå, såvida de inte behöver köra administratörsåtgärder som att skapa nya användare.

### <a name="azure-ad-authentication"></a>Azure AD-autentisering

Azure Active Directory-autentisering kräver att databasanvändare skapas som användare av oberoende databas. En oberoende databasanvändare mappar till en identitet i Azure AD-katalogen som är kopplad till databasen och har ingen inloggning i *master*-databasen. Azure AD-identiteten kan vara antingen ett enskilt användarkonto eller en grupp. Mer information finns i avsnittet om [oberoende databasanvändare, göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx) och läs [Azure AD-självstudien](./sql-database-aad-authentication-configure.md) om hur du autentiserar med hjälp av Azure AD.

> [!NOTE]
> Databasanvändare (exklusive administratörer) kan inte skapas med hjälp av Azure-portalen. Azure RBAC-roller sprids inte till SQL-servrar, databaser eller datalager. De används endast för att hantera Azure-resurser och gäller inte för databasbehörigheter.
>
> Till exempel beviljar rollen *SQL Server-deltagare* inte åtkomst för att ansluta till en databas eller ett datalager. Den här behörigheten måste beviljas i databasen med hjälp av T-SQL-instruktioner.

> [!IMPORTANT]
> Specialtecken som kolon `:` eller et-tecken `&` stöds inte i användarnamn i T-SQL `CREATE LOGIN` och `CREATE USER` instruktioner.

Så här lägger du till en användare med Azure AD-autentisering:

1. Anslut till Azure SQL-servern med hjälp av ett Azure AD-konto med minst behörigheten *ALTER ANY USER*.

1. I **Object Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. I frågefönstret anger du följande kommando och ändrar `<Azure_AD_principal_name>` till Azure AD-användarens huvudnamn eller visningsnamnet för Azure AD-gruppen:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Azure AD-användare har markerats i databasens metadata med typ `E (EXTERNAL_USER)` och typ `X (EXTERNAL_GROUPS)` för grupper. Mer information finns i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Skydda anslutningssträngar

För att kunna säkerställa en säker och krypterad anslutning mellan ett klientprogram och SQL Database så måste anslutningssträngen konfigureras för att:

- Begära en krypterad anslutning
- Inte lita på servercertifikatet

Anslutningen upprättas med TLS (Transport Layer Security) och risken för MITM-attacker (man-in-the-middle) minskar. Anslutningssträngar är tillgängliga per databas och har redan konfigurerats för att stödja klientdrivrutiner som ADO.NET, JDBC, ODBC och PHP. Information om TLS och anslutningar finns i avsnittet om [TLS-specifika överväganden](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Så här kopierar du en säker anslutningssträng:

1. I Azure-portalen väljer du **SQL-databaser** på den vänstra menyn och klickar på databasen på sidan **SQL-databaser**.

1. På databasens **översiktssida** klickar du på **Visa databasanslutningssträngar**.

1. Välj en drivrutinsflik och kopiera den fullständiga anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

Azure SQL Database innehåller säkerhetsfunktioner som nås med hjälp av Azure portal. Dessa funktioner är tillgängliga för både databasen och servern, förutom datamaskning, som endast är tillgängligt för databasen. Mer information finns i [Avancerad hotidentifiering](sql-advanced-threat-protection.md), [Granskning](sql-database-auditing.md), [Dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md) och [Transparent datakryptering](transparent-data-encryption-azure-sql.md).

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

Funktionen för avancerat skydd identifierar potentiella hot när de inträffar och innehåller säkerhetsaviseringar om avvikande aktiviteter. Användare kan utforska misstänkta händelser med granskningsfunktionen och avgöra om händelsen orsakades av ett försök att komma åt, tränga in i eller utnyttja data i databasen. Användare kan också få en översikt över säkerhet som innehåller en sårbarhetsbedömning och verktyget för dataidentifiering och klassificering.

> [!NOTE]
> Ett exempel på hot är SQL-inmatning, en process där angripare matar in skadliga SQL i indataprogrammet. Ett program kan sedan omedvetet köra skadlig SQL och ge angripare åtkomst till att tränga in i eller ändra data i databasen.

Så här aktiverar du hotskydd:

1. I Azure-portalen väljer du **SQL-databaser** på den vänstra menyn och klickar på databasen på sidan **SQL-databaser**.

1. På **översiktssidan** väljer du **Servernamn**. Databasserversidan öppnas.

1. På sidan **SQL-server** letar du upp avsnittet **Säkerhet** och väljer **Advanced Threat Protection**.

    1. Välj **PÅ** under **Advanced Threat Protection** för att aktivera funktionen. Välj sedan **Spara**.

    ![Navigeringsfönster](./media/sql-database-security-tutorial/threat-settings.png)

    Du kan också konfigurera e-postmeddelanden för att få säkerhetsaviseringar, lagringsinformation och typer för identifiering av hot.

1. Gå tillbaka till sidan **SQL-databaser** i din databas och välj **Advanced Threat Protection** i avsnittet **Säkerhet**. Här hittar du olika säkerhetsindikatorer som är tillgängliga för databasen.

    ![Hotstatus](./media/sql-database-security-tutorial/threat-status.png)

Om avvikande aktiviteter identifieras får du ett e-postmeddelande med information om händelsen. Detta inkluderar typen av aktivitet, databas, server, tidpunkt för händelsen, möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet. Om du får ett sådant e-postmeddelande väljer du länken för **Azure SQL-granskningsloggen** för att starta Azure-portalen och visa relevanta granskningsposter för händelsens tidpunkt.

   ![E-post om hotidentifiering](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Granskning

Granskningsfunktionen spårar databashändelser och skriver händelser till en granskningslogg i antingen Azure Storage, Log Analytics eller till en händelsehubb. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insyn i avvikelser och fel som kan tyda på potentiella säkerhetsöverträdelser.

Så här aktiverar du granskning:

1. I Azure-portalen väljer du **SQL-databaser** på den vänstra menyn och klickar på databasen på sidan **SQL-databaser**.

1. I avsnittet **Säkerhet** väljer du **Granskning**.

1. Under **granskningsinställningarna** anger du följande värden:

    1. Ställ in **Granskning** på **PÅ**.

    1. Välj **Mål för spårningsloggen** som något av följande:

        - **Storage**, ett Azure Storage-konto där händelseloggar sparas och kan laddas ned som *.xel*-filer

           > [!TIP]
           > Använd samma lagringskonto för alla granskade databaser för att få ut mesta möjliga av granskningsrapportmallarna.

        - **Log Analytics**, som automatiskt lagrar händelser för frågor eller ytterligare analys

            > [!NOTE]
            > En **Log Analytics-arbetsyta** krävs för att stödja avancerade funktioner som analyser, anpassade aviseringsregler och export av Excel eller Power BI. Utan en arbetsyta är endast frågeredigeraren tillgänglig.

        - **Event Hub**, som tillåter att händelser dirigeras för användning i andra program

    1. Välj **Spara**.

    ![Granska inställningar](./media/sql-database-security-tutorial/audit-settings.png)

1. Nu kan du välja **Visa granskningsloggar** för att visa databashändelsers data.

    ![Granskningsposter](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Se [Granskning av SQL Database](sql-database-auditing.md) om hur du ytterligare anpassar granskningshändelser med PowerShell eller REST API.

### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

Funktionen för datamaskning kommer automatiskt att dölja känsliga data i databasen.

Så här aktiverar du datamaskning:

1. I Azure-portalen väljer du **SQL-databaser** på den vänstra menyn och klickar på databasen på sidan **SQL-databaser**.

1. I avsnittet **Säkerhet** väljer du **Dynamisk datamaskning**.

1. Under inställningarna för **Dynamisk datamaskning** väljer du **Lägg till mask** för att lägga till en maskningsregel. Azure fyller automatiskt i tillgängliga databasscheman, tabeller och kolumner att välja bland.

    ![Maskinställningar](./media/sql-database-security-tutorial/mask-settings.png)

1. Välj **Spara**. Den valda informationen maskeras nu för sekretess.

    ![Maskexempel](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparent datakryptering

Krypteringsfunktionen krypterar automatiskt dina vilande data och kräver inga ändringar i program som har åtkomst till den krypterade databasen. Kryptering är standard för nya databaser. Du kan också kryptera data med hjälp av SSMS och funktionen [Alltid krypterad](sql-database-always-encrypted.md).

Så här aktiverar eller kontrollerar du kryptering:

1. I Azure-portalen väljer du **SQL-databaser** på den vänstra menyn och klickar på databasen på sidan **SQL-databaser**.

1. I avsnittet **Säkerhet** väljer du **Transparent datakryptering**.

1. Om det behövs väljer du **PÅ** för **Datakryptering**. Välj **Spara**.

    ![Transparent datakryptering](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Om du vill visa krypteringsstatus ansluter du till databasen med [SSMS](./sql-database-connect-query-ssms.md) och frågar efter kolumnen `encryption_state` i vyn [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql?view=sql-server-2017). Tillståndet `3` anger att databasen är krypterad.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att förbättra säkerheten för din databas med bara några få enkla steg. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa brandväggsregler på servernivå och databasnivå
> - Konfigurera en administratör för Azure Active Directory (AD Azure)
> - Hantera användaråtkomst med SQL-autentisering, Azure AD-autentisering och säkra anslutningssträngar
> - Aktivera säkerhetsfunktioner som hotskydd, granskning, datamaskering och kryptering

I nästa självstudie får du lära dig hur du implementerar en geo-distribution.

> [!div class="nextstepaction"]
>[Implementera en geodistribuerad databas](sql-database-implement-geo-distributed-database.md)
