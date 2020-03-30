---
title: Auktorisera åtkomst till server och databas med hjälp av inloggningar och användarkonton
description: Lär dig mer om hur Azure SQL Database och Azure Synapse Analytics autentiserar användare för åtkomst med hjälp av inloggningar och användarkonton. Lär dig också hur du databasroller och explicita behörigheter för att auktorisera inloggningar och användare att utföra åtgärder och frågedata.
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
ms.date: 03/23/2020
ms.openlocfilehash: 98c15fe11b64e8c177e60a2ea1eb7c50eaf69353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124824"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Auktorisera databasåtkomst till autentiserade användare till SQL Database och Azure Synapse Analytics med hjälp av inloggningar och användarkonton

I den här artikeln får du lära dig mer om:

- Alternativ för att konfigurera Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) för att göra det möjligt för användare att utföra administrativa uppgifter och komma åt data som lagras i dessa databaser.
- Åtkomst- och auktoriseringskonfigurationen efter att först ha skapat en ny Azure SQL-databas
- Så här lägger du till inloggningar och användarkonton i huvuddatabasen och användarkonton och ger sedan dessa konton administratörsbehörighet
- Så här lägger du till användarkonton i användardatabaser, antingen kopplade till inloggningar eller som inneslutna användarkonton
- Konfigurera användarkonton med behörigheter i användardatabaser med hjälp av databasroller och explicita behörigheter

> [!IMPORTANT]
> Databaser i Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) kallas gemensamt i resten av den här artikeln som antingen databaser eller som Azure SQL (för enkelhetens skull).

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

[**Autentisering**](sql-database-security-overview.md#authentication) är processen att bevisa att användaren är den de påstår sig vara. En användare ansluter till en databas med ett användarkonto.
När en användare försöker ansluta till en databas tillhandahåller de ett användarkonto och en autentiseringsinformation. Användaren autentiseras med någon av följande två autentiseringsmetoder:

- [SQL-autentisering](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Med den här autentiseringsmetoden skickar användaren ett användarkontonamn och associerat lösenord för att upprätta en anslutning. Det här lösenordet lagras i huvuddatabasen för användarkonton som är länkade till en inloggning eller lagras i databasen som innehåller användarkontot för användarkonton som inte är länkade till en inloggning.
- [Azure Active Directory-autentisering](sql-database-aad-authentication.md)

  Med den här autentiseringsmetoden skickar användaren ett användarkontonamn och begär att tjänsten använder den autentiseringsuppgifter som lagras i Azure Active Directory.

**Inloggningar och användare**: I Azure SQL kan ett användarkonto i en databas associeras med en inloggning som lagras i huvuddatabasen eller vara ett användarnamn som lagras i en enskild databas.

- En **inloggning** är ett enskilt konto i huvuddatabasen, till vilket ett användarkonto i en eller flera databaser kan länkas till. Med en inloggning lagras autentiseringsuppgifterna för användarkontot med inloggningen.
- Ett **användarkonto** är ett enskilt konto i en databas som kan vara men inte behöver länkas till en inloggning. Med ett användarkonto som inte är länkat till en inloggning lagras autentiseringsuppgifterna med användarkontot.

[**Auktorisering**](sql-database-security-overview.md#authorization) för åtkomst till data och utföra olika åtgärder hanteras med hjälp av databasroller och explicita behörigheter. Auktorisering refererar till de behörigheter som tilldelats en användare och avgör vad användaren får göra. Auktorisering kontrolleras av [användarkontots databasrollmedlemskap](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) och [behörigheter på objektnivå](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Ett bra tips är att du ska ge användare så få behörigheter som möjligt.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Befintliga inloggningar och användarkonton när du har skapat en ny databas

När du skapar din första Azure SQL-distribution anger du en administratörsinloggning och ett associerat lösenord för den inloggningen. Det här administrativa kontot kallas **Serveradministratör**. Följande konfiguration av inloggningar och användare i huvud- och användardatabaserna sker under distributionen:

- En SQL-inloggning med administratörsbehörighet skapas med det inloggningsnamn du angav. En [inloggning](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) är ett enskilt användarkonton för att logga in på SQL Database.
- Den här inloggningen beviljas fullständig administratörsbehörighet för alla databaser som [huvudnamn på servernivå](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Den här inloggningen har alla tillgängliga behörigheter i SQL Database och kan inte begränsas. I en hanterad instans läggs den här inloggningen till i [rollen fast server i sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (den här rollen finns inte med enstaka eller poolade databaser).
- Ett [användarkonto](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) `dbo` som anropas skapas för den här inloggningen i varje användardatabas. [Dbo-användaren](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) har alla databasbehörigheter i databasen och `db_owner` mappas till den fasta databasrollen. Ytterligare fasta databasroller beskrivs senare i den här artikeln.

Om du vill identifiera administratörskontona för en databas öppnar du Azure-portalen och navigerar till fliken **Egenskaper** för servern eller den hanterade instansen.

![SQL-serveradministratörer](media/sql-database-manage-logins/sql-admins.png)

![SQL-serveradministratörer](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> Det går inte att ändra administratörsinloggningsnamnet när det har skapats. Om du vill återställa lösenordet för den logiska serveradministratören går du till [Azure-portalen,](https://portal.azure.com)klickar på **SQL-servrar,** väljer servern i listan och klickar sedan på **Återställ lösenord**. Om du vill återställa lösenordet för en server med hanterade instanser går du till Azure-portalen, klickar på instansen och klickar på **Återställ lösenord**. Du kan också använda PowerShell eller Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Skapa ytterligare inloggningar och användare med administratörsbehörighet

Nu är din Azure SQL-instans endast konfigurerad för åtkomst med hjälp av en enda SQL-inloggning och användarkonto. Om du vill skapa ytterligare inloggningar med fullständig eller partiell administratörsbehörighet har du följande alternativ (beroende på distributionsläget):

- **Skapa ett Azure Active Directory-administratörskonto med fullständig administratörsbehörighet**

  Aktivera Azure Active Directory-autentisering och skapa en Azure AD-administratörsinloggning. Ett Azure Active Directory-konto kan konfigureras som administratör för SQL Database-distributionen med fullständiga administratörsbehörigheter. Det här kontot kan vara antingen ett enskilt konto eller ett säkerhetsgruppkonto. En Azure AD-administratör **måste** konfigureras om du vill använda Azure AD-konton för att ansluta till SQL Database. Detaljerad information om hur du aktiverar Azure AD-autentisering för alla distributionstyper i SQL Database finns i följande artiklar:

  - [Använda Azure Active Directory-autentisering för autentisering med SQL](sql-database-aad-authentication.md)
  - [Konfigurera och hantera Azure Active Directory-autentisering med SQL](sql-database-aad-authentication-configure.md)

- **Skapa SQL-inloggningar med fullständig administratörsbehörighet i en distribution av hanterade instanser**

  - Skapa ytterligare en SQL Server-inloggning i den hanterade instansen
  - Lägg till inloggningen till [sysadmin-rollen med](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) hjälp av [UTTRYCKET ALTER SERVER ROLE.](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) Den här inloggningen har fullständiga administratörsbehörighet.
  - Du kan också skapa en [Azure AD-inloggning](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) med hjälp av syntaxen <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">SKAPA INLOGGNING.</a>

- **Skapa SQL-inloggningar med begränsad administratörsbehörighet i en enda eller poolad distribution**

  - Skapa ytterligare en SQL-inloggning i huvuddatabasen för en enda eller poolad databasdistribution eller en distribution av hanterade instanser
  - Skapa ett användarkonto i huvuddatabasen som är associerad med den här nya inloggningen
  - Lägg till användarkontot `dbmanager`i `loginmanager` rollen , eller `master` båda i databasen med hjälp av [ALTER SERVER ROLE-satsen](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (för Azure Synapse Analytics använder du sp_addrolemember-satsen). [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

  > [!NOTE]
  > `dbmanager`och `loginmanager` roller gäller **inte** hanterade instansdistributioner.

  Medlemmar i dessa [särskilda huvuddatabasroller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) för enskilda eller poolade databaser gör det möjligt för användarna att ha behörighet att skapa och hantera databaser eller att skapa och hantera inloggningar. I databaser som skapats av en `dbmanager` användare som är medlem i `db_owner` rollen mappas medlemmen till den `dbo` fasta databasrollen och kan logga in på och hantera databasen med hjälp av användarkontot. Dessa roller har inga explicita behörigheter utanför huvuddatabasen.

  > [!IMPORTANT]
  > Du kan inte skapa ytterligare en SQL-inloggning med fullständiga administratörsbehörigheter i en enda eller poolad databas.

## <a name="create-accounts-for-non-administrator-users"></a>Skapa konton för användare som inte är administratörsanvändare

Du kan skapa konton för icke-administrativa användare med någon av två metoder:

- **Skapa en inloggning**

  Skapa en SQL-inloggning i huvuddatabasen. Skapa sedan ett användarkonto i varje databas som användaren behöver åtkomst till och koppla användarkontot till den inloggningen. Den här metoden är att föredra när användaren måste komma åt flera databaser och du vill behålla lösenorden synkroniserade. Den här metoden har dock komplexitet när den används med geo-replikering eftersom inloggningen måste skapas på både den primära servern och de sekundära servrarna. Mer information finns i [Konfigurera och hantera Azure SQL Database-säkerhet för geo-återställning eller redundans](sql-database-geo-replication-security-config.md).
- **Skapa ett användarkonto**

  Skapa ett användarkonto i databasen som en användare behöver åtkomst till (kallas även en [innesluten användare).](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)

  - Med en enda eller poolad databas kan du alltid skapa den här typen av användarkonto.
  - Med en hanterad instansdatabas som inte stöder [Azure AD-serverhuvudnamn](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)kan du bara skapa den här typen av användarkonto i en [innesluten databas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Med hanterad instans som stöder [Azure AD-serverhuvudnamn](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)kan du skapa användarkonton för att autentisera till den hanterade instansen utan att databasanvändare behöver skapas som en innesluten databasanvändare.

  Med den här metoden lagras användarautentiseringsinformationen i varje databas och replikeras automatiskt till geo-replikerade databaser. Om samma konto finns i flera databaser och du använder SQL-autentisering måste du behålla lösenorden synkroniserade manuellt. Om en användare har ett konto i olika databaser med olika lösenord kan det dessutom bli ett problem att komma ihåg dessa lösenord.

> [!IMPORTANT]
> Om du vill skapa inneslutna användare som mappas till Azure AD-identiteter måste du vara inloggad med ett Azure AD-konto som är administratör i SQL-databasen. I hanterad instans kan `sysadmin` en SQL-inloggning med behörigheter också skapa en Azure AD-inloggning eller användare.

Exempel på hur du skapar inloggningar och användare finns i:

- [Skapa inloggning för enstaka eller poolade databaser](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Skapa inloggning för databas med hanterade instanser](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Skapa inloggning för Azure Synapse Analytics-databas](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Skapa användare](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Skapa Azure AD-inneslutna användare](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> En säkerhetsstudiekurs som innehåller hur SQL Server skapas som användare i en enda eller poolad databas finns i [Självstudiekurs: Skydda en enda eller poolad databas](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Använda fasta och anpassade databasroller

När du har skapat ett användarkonto i en databas, antingen baserat på en inloggning eller som en innesluten användare, kan du auktorisera användaren att utföra olika åtgärder och komma åt data i en viss databas. Du kan använda följande metoder för att auktorisera åtkomst:

- **Fasta databasroller**

  Lägg till användarkontot i en [fast databasroll](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Det finns 9 fasta databasroller, var och en med en definierad uppsättning behörigheter. De vanligaste fasta databasrollerna är: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**och **db_denydatareader**. **db_owner** används ofta för att endast ge fullständig behörighet till några användare. De andra fasta databasrollerna är användbara för att snabbt få en enkel databas i utveckling, men de rekommenderas inte för de flesta produktionsdatabaserna. Den **db_datareader** fasta databasrollen ger till exempel läsåtkomst till varje tabell i databasen, vilket är mer än vad som är absolut nödvändigt.

  - Så här lägger du till en användare i en fast databasroll:

    - Använd [uttrycket ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) i Azure SQL Database. Exempel på exempel finns i [EXEMPEL PÅ ÄNDRA ROLL](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics använder du sp_addrolemember-satsen. [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) Exempel på finns [i sp_addrolemember exempel](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Anpassad databasroll**

  Skapa en anpassad databasroll med hjälp av [CREATE ROLE-satsen.](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) Med en anpassad roll kan du skapa egna användardefinierade databasroller och noggrant ge varje roll de lägsta behörigheter som krävs för affärsbehovet. Du kan sedan lägga till användare i den anpassade rollen. När en användare är medlem i flera roller sammanställs behörigheterna för alla.
- **Bevilja behörigheter direkt**

  Bevilja [användarkontobehörigheterna](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) direkt. Det finns över 100 behörigheter som individuellt kan beviljas eller nekas i SQL Database. Många av de här behörigheterna är kapslade. Till exempel inkluderar `UPDATE`-behörighet på ett schema `UPDATE`-behörighet för alla tabeller i schemat. Som i de flesta andra behörighetssystem åsidosätter ett nekande av en behörighet en beviljad. På grund av den kapslade karaktären och antalet behörigheter kan det krävas noggranna studier för att designa ett behörighetssystem som korrekt skyddar databasen. Börja med listan över behörigheter på [Behörigheter (Databasmotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) och granska den [stora bilden](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) med behörigheter.

## <a name="using-groups"></a>Använda grupper

Effektiv åtkomsthantering använder behörigheter som tilldelats Active Directory-säkerhetsgrupper och fasta eller anpassade roller i stället för för enskilda användare.

- När du använder Azure Active Directory-autentisering placerar du Azure Active Directory-användare i en Azure Active Directory-säkerhetsgrupp. Skapa en oberoende databasanvändare för gruppen. Placera en eller flera databasanvändare i en anpassad databasroll med specifika behörigheter som är lämpliga för den gruppen av användare.

- När du använder SQL-autentisering, skapa innehöll databasanvändare i databasen. Placera en eller flera databasanvändare i en anpassad databasroll med specifika behörigheter som är lämpliga för den gruppen av användare.

  > [!NOTE]
  > Du kan också använda grupper för icke-inneslutna databasanvändare.

Du bör bekanta dig med följande funktioner som kan användas för att begränsa eller utöka behörigheter:

- [Personifiering](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) och [modulsignering](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) kan användas för att säkert höja behörigheter tillfälligt.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan användas som en begränsning av vilka rader en användare kan komma åt.
- [Datamaskning](sql-database-dynamic-data-masking-get-started.md) kan användas för att begränsa exponering av känsliga data.
- [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) kan användas för att begränsa de åtgärder som kan utföras i databasen.

## <a name="next-steps"></a>Nästa steg

En översikt över alla säkerhetsfunktioner i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md).
