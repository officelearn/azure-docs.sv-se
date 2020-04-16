---
title: SQL-autentisering
description: Lär dig mer om SQL-autentisering i Azure Synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 2b80efa30ac7e04b9eb21dd6f8a39ab4ee90adf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424855"
---
# <a name="sql-authentication"></a>SQL-autentisering

Azure Synapse Analytics har två SQL-formfaktorer som gör att du kan styra din resursförbrukning. I den här artikeln beskrivs hur de två formfaktorerna styr användarautentiseringen.

Om du vill auktorisera till Synapse SQL kan du använda två auktoriseringstyper:

- AAD-auktorisering
- SQL-auktorisering

AAD-auktorisering är beroende av Azure Active Directory och gör att du kan ha en enda plats för användarhantering. SQL-auktorisering gör det möjligt för äldre program att använda Synapse SQL på ett väl förtrogent sätt.

## <a name="administrative-accounts"></a>Administrativa konton

Det finns två administrativa konton (**Serveradministratör** och **Active Directory-administratör**) som fungerar som administratörer. Om du vill identifiera dessa administratörskonton för SQL-servern öppnar du Azure-portalen och navigerar till fliken Egenskaper i Synapse SQL.

![SQL-serveradministratörer](./media/sql-authentication/sql-admins.png)

- **Serveradministratör**

  När du skapar en Azure Synapse Analytics måste du ange en **serveradministratörsinloggning**. SQL Server skapar kontot som en inloggning i huvuddatabasen. Det här kontot ansluter med hjälp av SQL Server-autentisering (användarnamn och lösenord). Endast ett av dessa konton kan finnas.

- **Azure Active Directory-administratör**

  Ett Azure Active Directory-konto, antingen ett enskilt eller säkerhetsgruppkonto, kan också konfigureras som en administratör. Det är valfritt att konfigurera en Azure AD-administratör, men en Azure AD-administratör **måste** konfigureras om du vill använda Azure AD-konton för att ansluta till Synapse SQL.

**Serveradministratörs-** och **Azure AD-administratörskonton** har följande egenskaper:

- Är de enda konton som automatiskt kan ansluta till en SQL-databas på servern. (För att kunna ansluta till en användardatabas måste andra konton antingen vara ägare till databasen eller ha ett användarkonto i databasen.)
- Dessa konton går in i användardatabaser som användaren `dbo` och de har alla behörigheter i användardatabaserna. (Ägaren till en användardatabas går också in i databasen som användaren `dbo`.)
- Ange inte `master` databasen som `dbo` användare och har begränsad behörighet i huvud.
- Är **inte** medlemmar i `sysadmin` den fasta standardserverrollen för SQL Server, som inte är tillgänglig i SQL-databasen.  
- Kan skapa, ändra och släppa databaser, inloggningar, användare i huvud- och IP-brandväggsregler på servernivå.
- Kan lägga till och `dbmanager` `loginmanager` ta bort medlemmar i rollerna och.
- Kan visa `sys.sql_logins` systemtabellen.

## <a name="sql-on-demand-preview"></a>SQL on-demand (förhandsgranskning)

Om du vill hantera de användare som har åtkomst till SQL på begäran kan du använda instruktionerna nedan.

Om du vill skapa en inloggning till SQL på begäran använder du följande syntax:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
När inloggningen finns kan du skapa användare i de enskilda databaserna i SQL on-demand-slutpunkten och bevilja nödvändiga behörigheter till dessa användare. Om du vill skapa en användning kan du använda följande syntax:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

När inloggning och användare har skapats kan du använda den vanliga SQL Server-syntaxen för att bevilja rättigheter.

## <a name="sql-pool"></a>SQL-pool

### <a name="administrator-access-path"></a>Åtkomstväg för administratör

När brandväggen på servernivå är korrekt konfigurerad kan **SQL-serveradministratören** och **Azure Active Directory-administratören** ansluta med hjälp av klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. 

I följande diagram visas en typisk konfiguration för de två administratörskontona:
 
![konfigurationen av de två administrationskontona](./media/sql-authentication/1sql-db-administrator-access.png)

Vid användning av en öppen port i brandväggen på servernivå kan administratörer ansluta till en SQL Database.

### <a name="database-creators"></a>Databasskapare

En av dessa administrativa roller är **dbmanager-rollen.** Medlemmar i den här rollen kan skapa nya databaser. För att använda den här rollen skapar du en användare i `master`-databasen och lägger sedan till användaren i **dbmanager**-databasrollen. 

För att skapa en databas måste användaren vara en användare `master` som baseras på en SQL Server-inloggning i databasen eller innehöll databasanvändare baserat på en Azure Active Directory-användare.

1. Anslut till `master` databasen med ett administratörskonto.
2. Skapa en SQL Server-autentiseringsinloggning med hjälp av [CREATE LOGIN-uttrycket.](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Exempel på instruktion:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Använd ett starkt lösenord när du skapar en inloggning eller en oberoende databasanvändare. Mer information finns i [Starka lösenord](/sql/relational-databases/security/strong-passwords?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

   För att förbättra prestandan cachelagras inloggningar (huvudnamn på servernivå) tillfälligt på databasnivån. Information om hur du uppdaterar autentiseringscache finns i [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

3. Skapa `master` en användare i databasen med hjälp av UTTRYCKET [SKAPA ANVÄNDARE.](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Användaren kan vara en Azure Active Directory-autentisering som innehöll databasanvändare (om du har konfigurerat din miljö för Azure AD-autentisering) eller en SQL Server-autentisering innehöll databasanvändare eller en SQL Server-autentiseringsanvändare baserat på en SQL Server-autentiseringsinloggning (skapad i föregående steg.) Exempel på utdrag:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Lägg till den nya användaren i **dbmanager-databasrollen** när `master` du använder [UTTRYCKET ALTER ROLE.](/sql/t-sql/statements/alter-role-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Exempel på instruktioner:

   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary;
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```

   > [!NOTE]
   > Dbmanager är en databasroll i en huvuddatabas, så du kan bara lägga till en databasanvändare i dbmanager-rollen. Du kan inte lägga till en inloggning på servernivå till databasnivårollen.

5. Vid behov konfigurerar du en brandväggsregel så att den nya användaren kan ansluta. (Den nya användaren kan omfattas av en befintlig brandväggsregel.)

Nu kan användaren ansluta `master` till databasen och skapa nya databaser. Det konto som skapar databasen blir ägare till databasen.

### <a name="login-managers"></a>Inloggningshanterare

Den andra administrativa rollen är inloggningshanterare-rollen. Medlemmar i den här rollen kan skapa nya inloggningar i huvuddatabasen. Om du vill kan du slutföra samma steg (skapa en inloggning och användare och lägga till en användare i rollen **loginmanager**) så att en användare kan skapa nya inloggningar i huvuddatabasen. Inloggningar är vanligtvis inte nödvändiga, eftersom Microsoft rekommenderar att du använder oberoende databasanvändare, som autentiseras på databasnivå istället för att använda användare baserat på inloggningar. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="non-administrator-users"></a>Användare som är icke-administratörer

Icke-administratörskonton behöver i allmänhet inte åtkomst till huvuddatabasen. Skapa oberoende databasanvändare på databasnivå med hjälp av instruktionen [SKAPA ANVÄNDARE (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

Användaren kan vara en Azure Active Directory-autentiserad oberoende databasanvändare (om du har konfigurerat din miljö för Azure AD-autentisering), eller en SQL Server-autentiserad oberoende databasanvändare, eller en SQL Server-autentiserad användare baserad på en SQL Server-autentiserad inloggning (skapad i föregående steg.)  

För att skapa användare, anslut till databasen och köra instruktioner som liknar följande exempel:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Initialt kan endast en av administratörerna eller ägaren av databasen skapa användare. För att auktorisera ytterligare användare som ska kunna skapa nya användare: Ge den valda användaren `ALTER ANY USER`-behörighet genom att använda en instruktion som:

```sql
GRANT ALTER ANY USER TO Mary;
```

Om du vill ge ytterligare användare fullständig kontroll över databasen gör du dem till medlem i **den db_owner** fasta databasrollen.

Använd uttrycket i `ALTER ROLE` Azure SQL Database.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

I SQL-poolen använder [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> En vanlig orsak till att skapa en databasanvändare baserat på en SQL Database-serverinloggning är för användare som behöver åtkomst till flera databaser. Eftersom databasanvändare är enskilda entiteter har varje databas sin egen användare och sitt eget lösenord. Detta kan orsaka omkostnader eftersom användaren sedan måste komma ihåg varje lösenord för varje databas, och det kan bli ohållbart när du måste ändra flera lösenord för många databaser. När DU använder SQL Server-inloggningar och hög tillgänglighet (aktiva geo-replikerings- och redundansgrupper) måste SQL Server-inloggningarna ställas in manuellt på varje server. Annars kommer databasanvändaren inte längre att mappas till serverinloggningen efter att en redundans inträffar och kommer inte att kunna komma åt databasinlägget. 

Mer information om hur du konfigurerar inloggningar för geo-replikering finns i [Konfigurera och hantera Azure SQL Database-säkerhet för geo-återställning eller redundans](../../sql-database/sql-database-geo-replication-security-config.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="configuring-the-database-level-firewall"></a>Konfigurera brandvägg på databasnivå

Bästa praxis är att icke-administratörer bara har åtkomst genom brandväggen till de databaser som de använder. Istället för att auktorisera deras IP-adresser genom brandväggen på servernivå och ge dem åtkomst till alla databaser, använd instruktionen [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) för att konfigurera brandväggen på databasnivå. Brandväggen på databasnivå kan inte konfigureras via portalen.

### <a name="non-administrator-access-path"></a>Åtkomstväg för icke-administratör

När brandväggen på databasnivå är korrekt konfigurerad kan databasanvändare ansluta med klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. I följande diagram visas en typisk åtkomstväg för en icke-administratör.

![Åtkomstväg för icke-administratör](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Grupper och roller

Effektiv åtkomsthantering använder behörigheter tilldelade grupper och roller i stället för enskilda användare.

- När du använder Azure Active Directory-autentisering, lägger du Azure Active Directory-användare i en Azure Active Directory-grupp. Skapa en oberoende databasanvändare för gruppen. Placera en eller flera användare i en [databasrollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och tilldela sedan [behörigheter](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) till databasrollen.

- När du använder SQL Server-autentisering kan du skapa inneslutna databasanvändare i databasen. Placera en eller flera användare i en [databasrollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och tilldela sedan [behörigheter](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) till databasrollen.

Databasrollerna kan vara de inbyggda rollerna, som **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** och **db_denydatareader**. **db_owner** används ofta för att endast ge fullständig behörighet till några användare. De andra fasta databasrollerna är användbara för att snabbt få en enkel databas i utveckling, men de rekommenderas inte för de flesta produktionsdatabaserna. 

Till exempel ger den fasta databasrollen **db_datareader** läsbehörighet till alla tabeller i databasen, vilket vanligtvis är mer än är absolut nödvändigt. 

Det är mycket bättre att använda instruktionen [SKAPA ROLL](https://msdn.microsoft.com/library/ms187936.aspx) för att skapa dina egna anpassade databasroller och noggrant bevilja varje roll minsta möjliga behörighet som krävs för företagets behov. När en användare är medlem i flera roller sammanställs behörigheterna för alla.

## <a name="permissions"></a>Behörigheter

Det finns över 100 behörigheter som individuellt kan beviljas eller nekas i SQL Database. Många av de här behörigheterna är kapslade. Till exempel inkluderar `UPDATE`-behörighet på ett schema `UPDATE`-behörighet för alla tabeller i schemat. Som i de flesta andra behörighetssystem åsidosätter ett nekande av en behörighet en beviljad. 

På grund av den kapslade karaktären och antalet behörigheter kan det krävas noggranna studier för att designa ett behörighetssystem som korrekt skyddar databasen. 

Börja med listan över behörigheter på [Behörigheter (Databasmotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) och granska den [stora bilden](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) med behörigheter.

### <a name="considerations-and-restrictions"></a>Överväganden och begränsningar

När du hanterar inloggningar och användare i SQL Database bör du tänka på följande:

- Du måste vara ansluten till **huvuddatabasen** när du kör utdragen. `CREATE/ALTER/DROP DATABASE`
- Databasanvändaren som motsvarar inloggningen som **serveradministratör** kan inte ändras eller tas bort.
- Amerikansk engelska är standardspråket för inloggningen **Serveradministratör**.
- Endast administratörer (inloggningen som **serveradministratör** eller Azure AD-administratör) och medlemmar i databasrollen **dbmanager** i **huvuddatabasen** har behörighet att köra `CREATE DATABASE`- och `DROP DATABASE`-uttrycken.
- Du måste vara ansluten till huvuddatabasen när du kör uttrycket `CREATE/ALTER/DROP LOGIN`. Att använda inloggningar rekommenderas inte. Använd i stället oberoende databasanvändare.
- Du måste ange namnet på databasen i anslutningssträngen för att ansluta till en användardatabas.
- Endast huvudsaklig inloggning på servernivå och medlemmarna i databasrollen **loginmanager** i **huvud**databasen har behörighet att köra uttryck `CREATE LOGIN`, `ALTER LOGIN` och `DROP LOGIN`.
- När du kör uttryck `CREATE/ALTER/DROP LOGIN` och `CREATE/ALTER/DROP DATABASE` i ett ADO.NET-program, är det inte tillåtet att använda parametriserade kommandon. Mer information finns i [Kommandon och parametrar](/dotnet/framework/data/adonet/commands-and-parameters?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
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

Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx).
 
