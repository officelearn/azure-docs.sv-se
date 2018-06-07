---
title: 'Azure SQL: inloggningar och användare | Microsoft Docs'
description: Lär dig mer om SQL Database säkerhetshantering, särskilt hur du hanterar säkerheten för databasåtkomst och inloggning via huvudnamnkonto på servernivå.
keywords: sql database-säkerhet, hantering av databassäkerhet, inloggningssäkerhet, databassäkerhet, databasåtkomst
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: carlrab
ms.openlocfilehash: 8529256313d8e3cb3b7155bb1b79764c17274397
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649813"
---
# <a name="controlling-and-granting-database-access"></a>Kontrollera och att bevilja åtkomst till databasen

När brandväggsregler har konfigurerats, kan personer ansluta till en SQL-databas som ett administratörskontot, databasens ägare eller en databasanvändare i databasen.  

>  [!NOTE]  
>  Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. 
>

> [!TIP]
> En självstudiekurs finns [skydda din Azure SQL Database](sql-database-security-tutorial.md).
>


## <a name="unrestricted-administrative-accounts"></a>Obegränsade administrativa konton
Det finns två administrativa konton (**Serveradministratör** och **Active Directory-administratör**) som fungerar som administratörer. För att identifiera dessa administratörskonton för SQL-servern öppnar du Azure-portalen och går till egenskaperna för SQL-servern.

![SQL-serveradministratörer](./media/sql-database-manage-logins/sql-admins.png)

- **Serveradministratör**   
När du skapar en Azure SQL-server måste du ange en **Inloggning för serveradministratör**. SQL Server skapar kontot som en inloggning i huvuddatabasen. Det här kontot ansluter med hjälp av SQL Server-autentisering (användarnamn och lösenord). Endast ett av dessa konton kan finnas.   
- **Azure Active Directory-administratör**   
Ett Azure Active Directory-konto, antingen ett enskilt eller säkerhetsgruppkonto, kan också konfigureras som en administratör. Det är valfritt att konfigurera en Azure AD-administratör, men en Azure AD-administratör måste konfigureras om du vill använda Azure AD-konton för att ansluta till SQL Database. Mer information om hur du konfigurerar åtkomst till Azure Active Directory finns i [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering](sql-database-aad-authentication.md) och [SSMS-stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).
 

Kontona **Serveradministratör** och **Azure AD-administratör** har följande egenskaper:
- De är de enda kontona som kan ansluta automatiskt till valfri SQL-databas på servern. (För att kunna ansluta till en användardatabas måste andra konton antingen vara ägare till databasen eller ha ett användarkonto i databasen.)
- Dessa konton går in i användardatabaser som användaren `dbo` och de har alla behörigheter i användardatabaserna. (Ägaren till en användardatabas går också in i databasen som användaren `dbo`.) 
- Dessa konton går inte in i `master`-databasen som användaren `dbo` och de har begränsad behörighet i huvuddatabasen. 
- Dessa konton är inte medlemmar i den fasta serverrollen `sysadmin` i standard-SQL Server, som inte är tillgänglig i SQL Database.  
- Dessa konton kan skapa, ändra och ta bort databaser, inloggningar, användare i huvuddatabasen och brandväggsregler på servernivå.
- Dessa konton kan lägga till och ta bort medlemmar i rollerna `dbmanager` och `loginmanager`.
- Dessa konton kan visa systemtabellen `sys.sql_logins`.

### <a name="configuring-the-firewall"></a>Konfigurering av brandväggen
När brandväggen på servernivå är konfigurerad för en enskild IP-adress eller ett intervall kan **SQL-serveradministratören** och **Azure Active Directory-administratören** ansluta till huvuddatabasen och alla användardatabaser. Den första brandväggen på servernivå kan konfigureras via [Azure-portalen](sql-database-get-started-portal.md)med hjälp av [PowerShell](sql-database-get-started-powershell.md) eller med [REST API:t](https://msdn.microsoft.com/library/azure/dn505712.aspx). När en anslutning upprättats, kan även ytterligare brandväggsregler på servernivå konfigureras med hjälp av [Transact-SQL](sql-database-configure-firewall-settings.md).

### <a name="administrator-access-path"></a>Åtkomstväg för administratör
När brandväggen på servernivå är korrekt konfigurerad kan **SQL-serveradministratören** och **Azure Active Directory-administratören** ansluta med hjälp av klientverktyg som SQL Server Management Studio eller SQL Server Data Tools. Endast de senaste verktygen innehåller alla funktioner och möjligheter. I följande diagram visas en typisk konfiguration för två administratörskonton.

![Åtkomstväg för administratör](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

Vid användning av en öppen port i brandväggen på servernivå kan administratörer ansluta till en SQL Database.

### <a name="connecting-to-a-database-by-using-sql-server-management-studio"></a>Ansluta till en databas med hjälp av SQL Server Management Studio
Om du behöver en genomgång av hur man skapar en databas, brandväggsregler på servernivå och hur man använder SQL Server Management Studio för att fråga en databas, finns det i [Kom igång med Azure SQL Database-servrar, databaser och brandväggsregler med hjälp av Azure-portalen och SQL Server Management Studio](sql-database-get-started-portal.md).

> [!IMPORTANT]
> Det rekommenderas att du alltid använder den senaste versionen av Management Studio för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="additional-server-level-administrative-roles"></a>Ytterligare administrativa roller på servernivå
Förutom de administrativa roller på servernivå som diskuterats, erbjuder SQL Database två begränsade administrativa roller i huvuddatabasen där användarkonton kan läggas till som beviljar behörigheter att antingen skapa databaser eller hantera inloggningar.

### <a name="database-creators"></a>Databasskapare
En av dessa administrativa roller är **dbmanager**-rollen. Medlemmar i den här rollen kan skapa nya databaser. För att använda den här rollen skapar du en användare i `master`-databasen och lägger sedan till användaren i **dbmanager**-databasrollen. För att skapa en databas måste användaren vara en användare som är baserad på SQL Server-inloggning i huvuddatabasen eller en oberoende databasanvändare som är baserad på en Azure Active Directory-användare.

1. Anslut till huvuddatabasen med ett administratörskonto.
2. Alternativt steg: Skapa en SQL Server-autentiseringsinloggning med hjälp av instruktionen [SKAPA INLOGGNING](https://msdn.microsoft.com/library/ms189751.aspx). Exempel på instruktion:
   
   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```
   
   > [!NOTE]
   > Använd ett starkt lösenord när du skapar en inloggning eller en oberoende databasanvändare. Mer information finns i [Starka lösenord](https://msdn.microsoft.com/library/ms161962.aspx).
    
   För att förbättra prestandan cachelagras inloggningar (huvudnamn på servernivå) tillfälligt på databasnivån. Information om hur du uppdaterar autentiseringscache finns i [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

3. Skapa en användare i huvuddatabasen med hjälp av instruktionen [SKAPA ANVÄNDARE](https://msdn.microsoft.com/library/ms173463.aspx). Användaren kan vara en Azure Active Directory-autentiserad oberoende databasanvändare (om du har konfigurerat din miljö för Azure AD-autentisering), eller en SQL Server-autentiserad oberoende databasanvändare, eller en SQL Server-autentiserad användare baserad på en SQL Server-autentiserad inloggning (skapad i föregående steg.) Exempel på instruktioner:
   
   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Tran WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Lägg till den nya användaren till **dbmanager**-databasrollen med hjälp av instruktionen [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx). Exempel på instruktioner:
   
   ```sql
   ALTER ROLE dbmanager ADD MEMBER Mary; 
   ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
   ```
   
   > [!NOTE]
   > Dbmanager är en databasroll i en huvuddatabas, så du kan bara lägga till en databasanvändare i dbmanager-rollen. Du kan inte lägga till en inloggning på servernivå till databasnivårollen.
    
5. Vid behov konfigurerar du en brandväggsregel så att den nya användaren kan ansluta. (Den nya användaren kan omfattas av en befintlig brandväggsregel.)

Nu kan användaren ansluta till huvuddatabasen och kan skapa nya databaser. Det konto som skapar databasen blir ägare till databasen.

### <a name="login-managers"></a>Inloggningshanterare
Den andra administrativa rollen är inloggningshanterare-rollen. Medlemmar i den här rollen kan skapa nya inloggningar i huvuddatabasen. Om du vill kan du slutföra samma steg (skapa en inloggning och användare och lägga till en användare i rollen **loginmanager**) så att en användare kan skapa nya inloggningar i huvuddatabasen. Inloggningar är vanligtvis inte nödvändiga, eftersom Microsoft rekommenderar att du använder oberoende databasanvändare, som autentiseras på databasnivå istället för att använda användare baserat på inloggningar. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="non-administrator-users"></a>Användare som är icke-administratörer
Icke-administratörskonton behöver i allmänhet inte åtkomst till huvuddatabasen. Skapa oberoende databasanvändare på databasnivå med hjälp av instruktionen [SKAPA ANVÄNDARE (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). Användaren kan vara en Azure Active Directory-autentiserad oberoende databasanvändare (om du har konfigurerat din miljö för Azure AD-autentisering), eller en SQL Server-autentiserad oberoende databasanvändare, eller en SQL Server-autentiserad användare baserad på en SQL Server-autentiserad inloggning (skapad i föregående steg.) Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx). 

För att skapa användare, anslut till databasen och köra instruktioner som liknar följande exempel:

```sql
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Initialt kan endast en av administratörerna eller ägaren av databasen skapa användare. För att auktorisera ytterligare användare som ska kunna skapa nya användare: Ge den valda användaren `ALTER ANY USER`-behörighet genom att använda en instruktion som:

```sql
GRANT ALTER ANY USER TO Mary;
```

Om du vill ge ytterligare användare fullständig behörighet till databasen, gör dem till medlemmar i den fasta databasrollen **db_owner** med hjälp av `ALTER ROLE`-instruktionen.

```sql
ALTER ROLE db_owner ADD MEMBER Mary; 
```

> [!NOTE]
> Det är en vanlig orsak till att skapa en databasanvändare baserat på en logisk server-inloggning för användare som behöver åtkomst till flera databaser. Eftersom finns databasanvändare enskilda enheter, varje databas behåller sin egen användar- och eget lösenord. Detta kan orsaka kostnader som användaren sedan komma ihåg varje lösenord för varje databas, och den kan bli ohållbara när behöva ändra flera lösenord för många databaser. Men när du använder SQL Server-inloggningar och hög tillgänglighet (aktiv geo-replikering och redundans grupper), måste SQL Server-inloggningar anges manuellt på varje server. Annars kommer databasanvändaren inte längre att mappas till server-inloggning efter en växling vid fel inträffar och kommer inte att komma åt databasen efter växling vid fel. Mer information om hur du konfigurerar inloggningar för geo-replikering finns [konfigurera och hantera Azure SQL Database-säkerhet för geo-återställning eller failover](sql-database-geo-replication-security-config.md).

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

* Du måste vara ansluten till **huvud**databasen när du kör uttrycket `CREATE/ALTER/DROP DATABASE`.   
* Databasanvändaren som motsvarar inloggningen som **serveradministratör** kan inte ändras eller tas bort. 
* Amerikansk engelska är standardspråket för inloggningen **Serveradministratör**.
* Endast administratörer (inloggningen som **serveradministratör** eller Azure AD-administratör) och medlemmar i databasrollen **dbmanager** i **huvuddatabasen** har behörighet att köra `CREATE DATABASE`- och `DROP DATABASE`-uttrycken.
* Du måste vara ansluten till huvuddatabasen när du kör uttrycket `CREATE/ALTER/DROP LOGIN`. Att använda inloggningar rekommenderas inte. Använd i stället oberoende databasanvändare.
* Du måste ange namnet på databasen i anslutningssträngen för att ansluta till en användardatabas.
* Endast huvudsaklig inloggning på servernivå och medlemmarna i databasrollen **loginmanager** i **huvud**databasen har behörighet att köra uttryck `CREATE LOGIN`, `ALTER LOGIN` och `DROP LOGIN`.
* När du kör uttryck `CREATE/ALTER/DROP LOGIN` och `CREATE/ALTER/DROP DATABASE` i ett ADO.NET-program, är det inte tillåtet att använda parametriserade kommandon. Mer information finns i [Kommandon och parametrar](https://msdn.microsoft.com/library/ms254953.aspx).
* När du kör uttrycket `CREATE/ALTER/DROP DATABASE` och `CREATE/ALTER/DROP LOGIN`, måste vart och ett av dessa uttryck vara den enda instruktionen i en Transact-SQL-batch. Annars uppstår ett fel. Till exempel kontrollerar följande Transact-SQL huruvida databasen finns. Om den finns anropas ett `DROP DATABASE`-uttryck för att ta bort databasen. Eftersom `DROP DATABASE`-uttrycket inte är det enda uttrycket i batchen, ger körning av följande Transact-SQL-uttryck ett fel.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```

* När du kör uttryck `CREATE USER` med alternativ `FOR/FROM LOGIN`, måste det vara det enda uttrycket i en Transact-SQL-batch.
* När du kör uttryck `ALTER USER` med alternativ `WITH LOGIN`, måste det vara det enda uttrycket i en Transact-SQL-batch.
* För `CREATE/ALTER/DROP` behöver en användare behörighet `ALTER ANY USER` på databasen.
* När ägaren av en databasroll försöker lägga till eller ta bort en annan databasanvändare till eller från databasrollen uppstår följande fel: **Användarens eller rollens ”Namn” finns inte i den här databasen.** Det här felet beror på att användaren inte är synlig för ägaren. Ge rollägare behörighet `VIEW DEFINITION` på användaren för att lösa problemet. 


## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om brandväggsregler, se [Azure SQL Database-brandväggen](sql-database-firewall-configure.md).
- En översikt över alla säkerhetsfunktioner i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md).
- En självstudiekurs finns [skydda din Azure SQL Database](sql-database-security-tutorial.md).
- Information om vyer och lagrade procedurer finns i [Skapa vyer och lagrade procedurer](https://msdn.microsoft.com/library/ms365311.aspx)
- Information om hur du beviljar åtkomst till ett databasobjekt finns i [Bevilja åtkomst till ett databasobjekt](https://msdn.microsoft.com/library/ms365327.aspx)
