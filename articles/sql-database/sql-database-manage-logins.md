---
title: Inloggningar och användare
description: Lär dig mer om SQL Database och SQL Data Warehouse säkerhets hantering, särskilt hur du hanterar databas åtkomst och inloggnings säkerhet via huvud kontot på server nivå.
keywords: sql database-säkerhet, hantering av databassäkerhet, inloggningssäkerhet, databassäkerhet, databasåtkomst
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/26/2019
ms.openlocfilehash: e9934f868fb62f9b1a19ef408dab69ab8a2c0e29
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159143"
---
# <a name="controlling-and-granting-database-access-to-sql-database-and-sql-data-warehouse"></a>Styra och bevilja databas åtkomst till SQL Database och SQL Data Warehouse

Efter konfigurationen av brand Väggs regler kan du ansluta till Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) som ett administratörs konto, som databas ägare eller som en databas användare i databasen.  

> [!NOTE]  
> Det här avsnittet gäller för Azure SQL Server och för att SQL Database och SQL Data Warehouse databaser som skapats på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. 
> [!TIP]
> En själv studie kurs finns i [skydda din Azure SQL Database](sql-database-security-tutorial.md). Den här självstudien gäller inte för **Azure SQL Database hanterade instanser**.

## <a name="unrestricted-administrative-accounts"></a>Obegränsade administrativa konton

Det finns två administrativa konton (**Serveradministratör** och **Active Directory-administratör**) som fungerar som administratörer. Om du vill identifiera administratörs kontona för din SQL-Server öppnar du Azure Portal och navigerar till fliken Egenskaper i SQL Server eller SQL Database.

![SQL-serveradministratörer](media/sql-database-manage-logins/sql-admins.png)

- **Server administratör**

  När du skapar en Azure SQL-server måste du ange en **Inloggning för serveradministratör**. SQL Server skapar kontot som en inloggning i huvuddatabasen. Det här kontot ansluter med hjälp av SQL Server-autentisering (användarnamn och lösenord). Endast ett av dessa konton kan finnas.

  > [!NOTE]
  > Om du vill återställa lösen ordet för Server administratören går du till [Azure Portal](https://portal.azure.com), klickar på **SQL-servrar**, väljer servern i listan och klickar sedan på **Återställ lösen ord**.

- **Azure Active Directory administratör**

  Ett Azure Active Directory-konto, antingen ett enskilt eller säkerhetsgruppkonto, kan också konfigureras som en administratör. Det är valfritt att konfigurera en Azure AD-administratör, men en Azure AD-administratör **måste** konfigureras om du vill använda Azure AD-konton för att ansluta till SQL Database. Mer information om hur du konfigurerar åtkomst till Azure Active Directory finns i [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering](sql-database-aad-authentication.md) och [SSMS-stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).

Administratörs kontona för **Server administratören** och **Azure AD** har följande egenskaper:

- Är de enda konton som kan ansluta automatiskt till alla SQL Database på servern. (För att kunna ansluta till en användardatabas måste andra konton antingen vara ägare till databasen eller ha ett användarkonto i databasen.)
- Dessa konton går in i användardatabaser som användaren `dbo` och de har alla behörigheter i användardatabaserna. (Ägaren till en användardatabas går också in i databasen som användaren `dbo`.) 
- Ange inte `master` databasen som `dbo` användare och har begränsad behörighet i Master. 
- Är **inte** medlemmar i standard SQL Server `sysadmin` fasta Server rollen, som inte är tillgänglig i SQL Database.  
- Kan skapa, ändra och släppa databaser, inloggningar, användare i huvud servrar och IP-brandvägg på server nivå.
- Kan lägga till och ta bort medlemmar i `dbmanager` och `loginmanager` roller.
- Kan visa `sys.sql_logins` system tabell.
- Det går inte att byta namn på den.
- Om du vill ändra administratörs kontot för Azure AD använder du portalen eller Azure CLI.
- Server administratörs kontot kan inte ändras efteråt.

### <a name="configuring-the-firewall"></a>Konfigurering av brandväggen

När brandväggen på servernivå är konfigurerad för en enskild IP-adress eller ett intervall kan **SQL-serveradministratören** och **Azure Active Directory-administratören** ansluta till huvuddatabasen och alla användardatabaser. Den första brandväggen på servernivå kan konfigureras via [Azure-portalen](sql-database-single-database-get-started.md)med hjälp av [PowerShell](sql-database-powershell-samples.md) eller med [REST API:t](https://msdn.microsoft.com/library/azure/dn505712.aspx). När en anslutning har upprättats kan ytterligare IP-brandvägg på server nivå också konfigureras med hjälp av [Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Åtkomstväg för administratör

När brandväggen på servernivå är korrekt konfigurerad kan **SQL-serveradministratören** och **Azure Active Directory-administratören** ansluta med hjälp av klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. I följande diagram visas en typisk konfiguration för två administratörskonton.

![konfiguration av de två administrations kontona](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Vid användning av en öppen port i brandväggen på servernivå kan administratörer ansluta till en SQL Database.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Ansluta till en databas med hjälp av SQL Server Management Studio

En genom gång av hur du skapar en server, en databas, en IP-brandvägg på server nivå och hur du använder SQL Server Management Studio för att fråga en databas finns i [komma igång med Azure SQL Database servrar, databaser och brand Väggs regler med hjälp av Azure Portal och SQL Server Management Studio](sql-database-single-database-get-started.md).

> [!IMPORTANT]
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="additional-server-level-administrative-roles"></a>Ytterligare administrativa roller på servernivå

>[!IMPORTANT]
>Det här avsnittet gäller inte för **Azure SQL Database hanterade instansen** eftersom rollerna är speciella för **Azure SQL Database**.

Förutom de administrativa roller på servernivå som diskuterats, erbjuder SQL Database två begränsade administrativa roller i huvuddatabasen där användarkonton kan läggas till som beviljar behörigheter att antingen skapa databaser eller hantera inloggningar.

### <a name="database-creators"></a>Databasskapare

En av dessa administrativa roller är **dbmanager**-rollen. Medlemmar i den här rollen kan skapa nya databaser. För att använda den här rollen skapar du en användare i `master`-databasen och lägger sedan till användaren i **dbmanager**-databasrollen. Om du vill skapa en databas måste användaren vara en användare baserad på en SQL Server inloggning i `master` databasen eller en databas användare som är baserad på en Azure Active Directory användare.

1. Anslut till `master`-databasen med ett administratörs konto.
2. Skapa en inloggning för SQL Server autentisering med hjälp av instruktionen [create login](https://msdn.microsoft.com/library/ms189751.aspx) . Exempel på instruktion:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Använd ett starkt lösenord när du skapar en inloggning eller en oberoende databasanvändare. Mer information finns i [Starka lösenord](https://msdn.microsoft.com/library/ms161962.aspx).

   För att förbättra prestandan cachelagras inloggningar (huvudnamn på servernivå) tillfälligt på databasnivån. Information om hur du uppdaterar autentiseringscache finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. Skapa en användare i `master`-databasen med hjälp av instruktionen [create User](https://msdn.microsoft.com/library/ms173463.aspx) . Användaren kan vara en Azure Active Directory autentisering som innehåller databas användare (om du har konfigurerat din miljö för Azure AD-autentisering) eller en SQL Server autentisering som innehåller en databas användare eller en SQL Server autentisering som är baserad på en SQL Server inloggning för autentisering (skapades i föregående steg.) Exempel på uttryck:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Lägg till den nya användaren i **DBManager** -databas rollen i `master` med hjälp av [Alter Role](https://msdn.microsoft.com/library/ms189775.aspx) -instruktionen. Exempel på instruktioner:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > Dbmanager är en databasroll i en huvuddatabas, så du kan bara lägga till en databasanvändare i dbmanager-rollen. Du kan inte lägga till en inloggning på servernivå till databasnivårollen.

5. Vid behov konfigurerar du en brandväggsregel så att den nya användaren kan ansluta. (Den nya användaren kan omfattas av en befintlig brandväggsregel.)

Användaren kan nu ansluta till den `master` databasen och kan skapa nya databaser. Det konto som skapar databasen blir ägare till databasen.

### <a name="login-managers"></a>Inloggningshanterare

Den andra administrativa rollen är inloggningshanterare-rollen. Medlemmar i den här rollen kan skapa nya inloggningar i huvuddatabasen. Om du vill kan du slutföra samma steg (skapa en inloggning och användare och lägga till en användare i rollen **loginmanager**) så att en användare kan skapa nya inloggningar i huvuddatabasen. Inloggningar är vanligtvis inte nödvändiga, eftersom Microsoft rekommenderar att du använder oberoende databasanvändare, som autentiseras på databasnivå istället för att använda användare baserat på inloggningar. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Användare som är icke-administratörer

Icke-administratörskonton behöver i allmänhet inte åtkomst till huvuddatabasen. Skapa oberoende databasanvändare på databasnivå med hjälp av instruktionen [SKAPA ANVÄNDARE (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). Användaren kan vara en Azure Active Directory autentisering som innehåller databas användare (om du har konfigurerat din miljö för Azure AD-autentisering) eller en SQL Server autentisering som innehåller en databas användare eller en SQL Server autentisering som är baserad på en SQL Server inloggning för autentisering (skapades i föregående steg.) Mer information finns i [inneslutna databas användare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx). 

För att skapa användare, anslut till databasen och köra instruktioner som liknar följande exempel:

```sql
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Initialt kan endast en av administratörerna eller ägaren av databasen skapa användare. För att auktorisera ytterligare användare som ska kunna skapa nya användare: Ge den valda användaren `ALTER ANY USER`-behörighet genom att använda en instruktion som:

```sql
GRANT ALTER ANY USER TO Mary;
```

Om du vill ge fler användare fullständig kontroll över databasen gör du dem till medlem i den **db_owner** fasta databas rollen.

I Azure SQL Database använder du `ALTER ROLE`-instruktionen.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

I Azure SQL Data Warehouse använder du [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql).
```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```


> [!NOTE]
> En vanlig orsak till att skapa en databas användare baserat på en SQL Database Server inloggning är för användare som behöver åtkomst till flera databaser. Eftersom inneslutna databas användare är enskilda entiteter, behåller varje databas sin egen användare och det egna lösen ordet. Detta kan medföra att användaren måste komma ihåg varje lösen ord för varje databas, och det kan bli untenable när du behöver ändra flera lösen ord för många databaser. Men när du använder SQL Server inloggningar och hög tillgänglighet (aktiva geo-replikering och failover-grupper) måste SQL Server inloggningar anges manuellt på varje server. Annars kommer databas användaren inte längre att mappas till Server inloggningen efter en redundansväxling och kommer inte att kunna komma åt databasen efter redundansväxlingen. Mer information om hur du konfigurerar inloggningar för geo-replikering finns i [Konfigurera och hantera Azure SQL Database säkerhet för geo-återställning eller redundans](sql-database-geo-replication-security-config.md).

### <a name="configuring-the-database-level-firewall"></a>Konfigurera brandvägg på databasnivå

Bästa praxis är att icke-administratörer bara har åtkomst genom brandväggen till de databaser som de använder. Istället för att auktorisera deras IP-adresser genom brandväggen på servernivå och ge dem åtkomst till alla databaser, använd instruktionen [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) för att konfigurera brandväggen på databasnivå. Brandväggen på databasnivå kan inte konfigureras via portalen.

### <a name="non-administrator-access-path"></a>Åtkomstväg för icke-administratör

När brandväggen på databasnivå är korrekt konfigurerad kan databasanvändare ansluta med klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. I följande diagram visas en typisk åtkomstväg för en icke-administratör.

![Åtkomstväg för icke-administratör](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupper och roller

Effektiv åtkomsthantering använder behörigheter tilldelade grupper och roller i stället för enskilda användare. 

- När du använder Azure Active Directory-autentisering, lägger du Azure Active Directory-användare i en Azure Active Directory-grupp. Skapa en oberoende databasanvändare för gruppen. Placera en eller flera användare i en [databasrollen](https://msdn.microsoft.com/library/ms189121) och tilldela sedan [behörigheter](https://msdn.microsoft.com/library/ms191291.aspx) till databasrollen.

- När du använder SQL Server-autentisering kan du skapa inneslutna databasanvändare i databasen. Placera en eller flera användare i en [databasrollen](https://msdn.microsoft.com/library/ms189121) och tilldela sedan [behörigheter](https://msdn.microsoft.com/library/ms191291.aspx) till databasrollen.

Databasrollerna kan vara de inbyggda rollerna, som **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** och **db_denydatareader**. **db_owner** används ofta för att endast ge fullständig behörighet till några användare. De andra fasta databasrollerna är användbara för att snabbt få en enkel databas i utveckling, men de rekommenderas inte för de flesta produktionsdatabaserna. Till exempel ger den fasta databasrollen **db_datareader** läsbehörighet till alla tabeller i databasen, vilket vanligtvis är mer än är absolut nödvändigt. Det är mycket bättre att använda instruktionen [SKAPA ROLL](https://msdn.microsoft.com/library/ms187936.aspx) för att skapa dina egna anpassade databasroller och noggrant bevilja varje roll minsta möjliga behörighet som krävs för företagets behov. När en användare är medlem i flera roller sammanställs behörigheterna för alla.

## <a name="permissions"></a>Behörigheter

Det finns över 100 behörigheter som individuellt kan beviljas eller nekas i SQL Database. Många av de här behörigheterna är kapslade. Till exempel inkluderar `UPDATE`-behörighet på ett schema `UPDATE`-behörighet för alla tabeller i schemat. Som i de flesta andra behörighetssystem åsidosätter ett nekande av en behörighet en beviljad. På grund av den kapslade karaktären och antalet behörigheter kan det krävas noggranna studier för att designa ett behörighetssystem som korrekt skyddar databasen. Börja med listan över behörigheter på [Behörigheter (Databasmotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) och granska den [stora bilden](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) med behörigheter.


### <a name="considerations-and-restrictions"></a>Överväganden och begränsningar

När du hanterar inloggningar och användare i SQL Database, bör du överväga följande:

- Du måste vara ansluten till **huvud**databasen när du kör uttrycket `CREATE/ALTER/DROP DATABASE`.   
- Databasanvändaren som motsvarar inloggningen som **serveradministratör** kan inte ändras eller tas bort. 
- Amerikansk engelska är standardspråket för inloggningen **Serveradministratör**.
- Endast administratörer (inloggningen som **serveradministratör** eller Azure AD-administratör) och medlemmar i databasrollen **dbmanager** i **huvuddatabasen** har behörighet att köra `CREATE DATABASE`- och `DROP DATABASE`-uttrycken.
- Du måste vara ansluten till huvuddatabasen när du kör uttrycket `CREATE/ALTER/DROP LOGIN`. Att använda inloggningar rekommenderas inte. Använd i stället oberoende databasanvändare.
- Du måste ange namnet på databasen i anslutningssträngen för att ansluta till en användardatabas.
- Endast huvudsaklig inloggning på servernivå och medlemmarna i databasrollen **loginmanager** i **huvud**databasen har behörighet att köra uttryck `CREATE LOGIN`, `ALTER LOGIN` och `DROP LOGIN`.
- När du kör uttryck `CREATE/ALTER/DROP LOGIN` och `CREATE/ALTER/DROP DATABASE` i ett ADO.NET-program, är det inte tillåtet att använda parametriserade kommandon. Mer information finns i [Kommandon och parametrar](https://msdn.microsoft.com/library/ms254953.aspx).
- När du kör uttrycket `CREATE/ALTER/DROP DATABASE` och `CREATE/ALTER/DROP LOGIN`, måste vart och ett av dessa uttryck vara den enda instruktionen i en Transact-SQL-batch. Annars uppstår ett fel. Till exempel kontrollerar följande Transact-SQL huruvida databasen finns. Om den finns anropas ett `DROP DATABASE`-uttryck för att ta bort databasen. Eftersom `DROP DATABASE`-uttrycket inte är det enda uttrycket i batchen, ger körning av följande Transact-SQL-uttryck ett fel.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Använd i stället följande Transact-SQL-uttryck:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- När du kör uttryck `CREATE USER` med alternativ `FOR/FROM LOGIN`, måste det vara det enda uttrycket i en Transact-SQL-batch.
- När du kör uttryck `ALTER USER` med alternativ `WITH LOGIN`, måste det vara det enda uttrycket i en Transact-SQL-batch.
- För `CREATE/ALTER/DROP` behöver en användare behörighet `ALTER ANY USER` på databasen.
- När ägaren av en databasroll försöker lägga till eller ta bort en annan databasanvändare till eller från databasrollen uppstår följande fel: **Användarens eller rollens ”Namn” finns inte i den här databasen.** Det här felet beror på att användaren inte är synlig för ägaren. Ge rollägare behörighet `VIEW DEFINITION` på användaren för att lösa problemet. 


## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om brandväggsregler, se [Azure SQL Database-brandväggen](sql-database-firewall-configure.md).
- En översikt över alla säkerhetsfunktioner i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md).
- En själv studie kurs finns i [skydda din Azure SQL Database](sql-database-security-tutorial.md).
- Information om vyer och lagrade procedurer finns i [Skapa vyer och lagrade procedurer](https://msdn.microsoft.com/library/ms365311.aspx)
- Information om hur du beviljar åtkomst till ett databasobjekt finns i [Bevilja åtkomst till ett databasobjekt](https://msdn.microsoft.com/library/ms365327.aspx)
