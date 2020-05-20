---
title: Auktorisera Server-och databas åtkomst med hjälp av inloggningar och användar konton
description: Lär dig mer om hur Azure SQL Database och Azure Synapse Analytics autentiserar användare för åtkomst med hjälp av inloggningar och användar konton. Lär dig också hur du skapar databas roller och explicita behörigheter för att auktorisera inloggningar och användare för att utföra åtgärder och fråga data.
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
ms.openlocfilehash: 751c85559330272e84e628d22756d47c24b08711
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701656"
---
# <a name="authorizing-database-access-to-authenticated-users-to-sql-database-and-azure-synapse-analytics-using-logins-and-user-accounts"></a>Auktorisera databas åtkomst till autentiserade användare till SQL Database och Azure Synapse Analytics med hjälp av inloggningar och användar konton

I den här artikeln får du lära dig om:

- Alternativ för att konfigurera Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) så att användarna kan utföra administrativa uppgifter och komma åt de data som lagras i dessa databaser.
- Åtkomst-och auktoriserings konfigurationen efter att en ny Azure SQL Database har skapats
- Hur du lägger till inloggningar och användar konton i huvud databasen och sedan tilldelar de här kontona administratörs behörighet
- Hur du lägger till användar konton i användar databaser, antingen kopplade till inloggningar eller som inneslutna användar konton
- Konfigurera användar konton med behörigheter i användar databaser genom att använda databas roller och explicita behörigheter

> [!IMPORTANT]
> Databaser i Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) kallas kollektivt i resten av den här artikeln som antingen databaser eller som Azure SQL (för enkelhetens skull).

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

[**Autentisering**](sql-database-security-overview.md#authentication) är en process för att bevisa att användaren är den som han eller hon ansöker. En användare ansluter till en databas med ett användar konto.
När en användare försöker ansluta till en databas, anger de ett användar konto och autentiseringsinformation. Användaren autentiseras med hjälp av någon av följande två autentiseringsmetoder:

- [SQL-autentisering](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Med den här autentiseringsmetoden skickar användaren ett användar konto namn och kopplat lösen ord för att upprätta en anslutning. Det här lösen ordet lagras i huvud databasen för användar konton som är länkade till en inloggning eller lagras i databasen som innehåller de användar konton som *inte är* länkade till någon inloggning.
- [Azure Active Directory autentisering](sql-database-aad-authentication.md)

  Med den här autentiseringsmetoden skickar användaren ett användar konto namn och begär att tjänsten använder den autentiseringsinformation som lagrats i Azure Active Directory.

**Inloggningar och användare**: i Azure SQL kan ett användar konto i en databas associeras med en inloggning som lagras i huvud databasen eller som kan vara ett användar namn som lagras i en enskild databas.

- En **inloggning** är ett enskilt konto i huvud databasen, till vilket ett användar konto i en eller flera databaser kan länkas. Med en inloggning lagras autentiseringsuppgifterna för användar kontot med inloggningen.
- Ett **användar konto** är ett enskilt konto i en databas som kan vara men inte behöver länkas till en inloggning. Med ett användar konto som inte är länkat till en inloggning lagras autentiseringsinformation med användar kontot.

[**Behörighet att komma**](sql-database-security-overview.md#authorization) åt data och utföra olika åtgärder hanteras med databas roller och explicita behörigheter. Auktorisering syftar på de behörigheter som tilldelats en användare och avgör vad användaren får göra. Auktoriseringen styrs av ditt användar kontos databas [roll medlemskap](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) och [behörigheter på objekt nivå](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine). Ett bra tips är att du ska ge användare så få behörigheter som möjligt.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Befintliga inloggningar och användar konton när du har skapat en ny databas

När du skapar din första Azure SQL-distribution anger du en Administratörs inloggning och ett kopplat lösen ord för inloggningen. Det här administratörs kontot kallas **Server administratör**. Följande konfiguration av inloggningar och användare i huvud databasen och användar databaser sker under distributionen:

- En SQL-inloggning med administratörs behörighet skapas med det inloggnings namn som du har angett. En [inloggning](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) är ett enskilt användar konto för inloggning till SQL Database.
- Den här inloggningen beviljas fullständig administratörs behörighet för alla databaser som en [huvud server nivå](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). Den här inloggningen har alla tillgängliga behörigheter inom SQL Database och kan inte begränsas. I en hanterad instans läggs den här inloggningen till i den [fasta Server rollen sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (den här rollen finns inte med en eller flera databaser i en pool).
- Ett [användar konto](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) som heter `dbo` skapas för den här inloggningen i varje användar databas. [Dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) -användaren har alla databas behörigheter i databasen och är mappad till den `db_owner` fasta databas rollen. Ytterligare fasta databas roller beskrivs längre fram i den här artikeln.

Om du vill identifiera administratörs konton för en databas öppnar du Azure Portal och navigerar till fliken **Egenskaper** för servern eller den hanterade instansen.

![SQL-serveradministratörer](media/sql-database-manage-logins/sql-admins.png)

![SQL-serveradministratörer](media/sql-database-manage-logins/sql-admins2.png)

> [!IMPORTANT]
> Inloggnings namnet för administratören kan inte ändras efter att det har skapats. Om du vill återställa lösen ordet för den logiska Server administratören går du till [Azure Portal](https://portal.azure.com), klickar på **SQL-servrar**, väljer servern i listan och klickar sedan på **Återställ lösen ord**. Om du vill återställa lösen ordet för en hanterad instans Server går du till Azure Portal, klickar på instansen och sedan på **Återställ lösen ord**. Du kan också använda PowerShell eller Azure CLI.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Skapa ytterligare inloggningar och användare med administratörs behörighet

Nu är din Azure SQL-instans bara konfigurerad för åtkomst med hjälp av en enda SQL-inloggning och ett användar konto. Om du vill skapa ytterligare inloggningar med fullständig eller delvis administratörs behörighet har du följande alternativ (beroende på ditt distributions läge):

- **Skapa ett Azure Active Directory administratörs konto med fullständig administratörs behörighet**

  Aktivera Azure Active Directory autentisering och skapa en Azure AD-Administratörs inloggning. Ett Azure Active Directory konto kan konfigureras som administratör av SQL Database-distributionen med fullständig administratörs behörighet. Det här kontot kan vara ett individuellt konto eller ett konto för säkerhets grupp. En Azure AD-administratör **måste** konfigureras om du vill använda Azure AD-konton för att ansluta till SQL Database. Detaljerad information om hur du aktiverar Azure AD-autentisering för alla SQL Database distributions typer finns i följande artiklar:

  - [Använd Azure Active Directory autentisering för autentisering med SQL](sql-database-aad-authentication.md)
  - [Konfigurera och hantera Azure Active Directory-autentisering med SQL](sql-database-aad-authentication-configure.md)

- **I en hanterad instans distribution skapar du SQL-inloggningar med fullständig administratörs behörighet**

  - Skapa ytterligare en SQL Server inloggning i den hanterade instansen
  - Lägg till inloggningen till den [fasta Server rollen sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) med hjälp av instruktionen [Alter Server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) . Den här inloggningen kommer att ha fullständig administratörs behörighet.
  - Du kan också skapa en [Azure AD-inloggning](sql-database-aad-authentication-configure.md?tabs=azure-powershell#new-azure-ad-admin-functionality-for-mi) med hjälp av syntaxen för att <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Skapa inloggning</a> .

- **Skapa SQL-inloggningar med begränsade administrativa behörigheter i en enskild eller pool distribution**

  - Skapa ytterligare en SQL-inloggning i huvud databasen för en databas distribution med en enskild databas eller en hanterad instans distribution
  - Skapa ett användar konto i huvud databasen som är associerad med den nya inloggningen
  - Lägg till användar kontot i `dbmanager` , `loginmanager` rollen eller både och i `master` databasen med hjälp av instruktionen [Alter Server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) (för Azure Synapse Analytics använder du [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) -instruktionen).

  > [!NOTE]
  > `dbmanager`och- `loginmanager` roller gäller **inte** för distributioner av hanterade instanser.

  Medlemmar i dessa [särskilda huvud databas roller](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) för enskilda databaser eller databaser i pooler gör det möjligt för användarna att skapa och hantera databaser eller skapa och hantera inloggningar. I databaser som skapats av en användare som är medlem i `dbmanager` rollen mappas medlemmen till den `db_owner` fasta databas rollen och kan logga in på och hantera databasen med hjälp av `dbo` användar kontot. De här rollerna har inga uttryckliga behörigheter utanför Master-databasen.

  > [!IMPORTANT]
  > Du kan inte skapa ytterligare en SQL-inloggning med fullständig administratörs behörighet i en databas med en eller flera databaser.

## <a name="create-accounts-for-non-administrator-users"></a>Skapa konton för användare som inte är administratörer

Du kan skapa konton för icke-administratörer på något av två sätt:

- **Skapa en inloggning**

  Skapa en SQL-inloggning i huvud databasen. Skapa sedan ett användar konto i varje databas som användaren behöver åtkomst till och koppla användar kontot till den inloggningen. Den här metoden rekommenderas när användaren måste ha åtkomst till flera databaser och du vill synkronisera lösen orden. Den här metoden har dock komplicerade metoder när de används med geo-replikering eftersom inloggningen måste skapas på både den primära servern och de sekundära servrarna. Mer information finns i [Konfigurera och hantera Azure SQL Database säkerhet för geo-återställning eller redundans](sql-database-geo-replication-security-config.md).
- **Skapa ett användarkonto**

  Skapa ett användar konto i databasen som en användare behöver åtkomst till (kallas även för en [innesluten användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Med en databas med en eller flera databaser kan du alltid skapa den här typen av användar konto.
  - Med en hanterad instans databas som inte stöder [Azure AD server-huvudobjekt](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)kan du bara skapa den här typen av användar konto i en [innesluten databas](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). Med hanterad instans som stöder [Azure AD server-huvudobjekt](sql-database-aad-authentication-configure.md?tabs=azure-powershell#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)kan du skapa användar konton för att autentisera till den hanterade instansen utan att databas användare måste skapas som en innesluten databas användare.

  Med den här metoden lagras informationen om användarautentisering i varje databas och replikeras till geo-replikerade databaser automatiskt. Men om samma konto finns i flera databaser och du använder SQL-autentisering måste du synkronisera lösen orden manuellt. Om en användare har ett konto i olika databaser med olika lösen ord, kan det dessutom bli problem med att komma ihåg lösen orden.

> [!IMPORTANT]
> Om du vill skapa inneslutna användare som är mappade till Azure AD-identiteter måste du vara inloggad med ett Azure AD-konto som är en administratör i SQL Database. I en hanterad instans kan en SQL-inloggning med `sysadmin` behörigheter också skapa en Azure AD-inloggning eller användare.

Exempel som visar hur du skapar inloggningar och användare finns i:

- [Skapa inloggning för en eller flera databaser i en pool](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Skapa inloggning för hanterad instans databas](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Skapa inloggning för Azure Synapse Analytics-databasen](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Skapa användare](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Skapa Azure AD-inkluderade användare](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)

> [!TIP]
> En säkerhets kurs som innehåller information om hur du skapar SQL Server en innesluten användare i en databas med en eller flera databaser finns i [Självstudier: skydda en databas med en eller flera databaser](sql-database-security-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Använda fasta och anpassade databas roller

När du har skapat ett användar konto i en databas, antingen baserat på en inloggning eller som en innesluten användare, kan du ge användaren behörighet att utföra olika åtgärder och komma åt data i en viss databas. Du kan använda följande metoder för att auktorisera åtkomst:

- **Fasta databas roller**

  Lägg till användar kontot till en [fast databas roll](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Det finns 9 fasta databas roller, var och en med en definierad uppsättning behörigheter. De vanligaste fasta databas rollerna är: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**och **db_denydatareader**. **db_owner** används ofta för att endast ge fullständig behörighet till några användare. De andra fasta databasrollerna är användbara för att snabbt få en enkel databas i utveckling, men de rekommenderas inte för de flesta produktionsdatabaserna. **Db_datareader** den fasta databas rollen ger till exempel Läs behörighet till alla tabeller i databasen, vilket är mer än vad som är absolut nödvändigt.

  - Så här lägger du till en användare till en fast databas roll:

    - Använd instruktionen [Alter Role](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) i Azure SQL Database. Exempel finns i [ändra roll exempel](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Azure Synapse Analytics använder du [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) -instruktionen. Exempel finns i [sp_addrolemember exempel](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Anpassad databas roll**

  Skapa en anpassad databas roll med instruktionen [skapa roll](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) . Med en anpassad roll kan du skapa egna användardefinierade databas roller och samtidigt ge varje roll minst de behörigheter som krävs för affärs behovet. Du kan sedan lägga till användare i den anpassade rollen. När en användare är medlem i flera roller sammanställs behörigheterna för alla.
- **Bevilja behörigheter direkt**

  Bevilja användar kontots [behörigheter](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) direkt. Det finns över 100 behörigheter som individuellt kan beviljas eller nekas i SQL Database. Många av de här behörigheterna är kapslade. Till exempel inkluderar `UPDATE`-behörighet på ett schema `UPDATE`-behörighet för alla tabeller i schemat. Som i de flesta andra behörighetssystem åsidosätter ett nekande av en behörighet en beviljad. På grund av den kapslade karaktären och antalet behörigheter kan det krävas noggranna studier för att designa ett behörighetssystem som korrekt skyddar databasen. Börja med listan över behörigheter på [Behörigheter (Databasmotor)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) och granska den [stora bilden](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) med behörigheter.

## <a name="using-groups"></a>Använda grupper

Effektiv åtkomst hantering använder behörigheter som tilldelats Active Directory säkerhets grupper och fasta eller anpassade roller i stället för enskilda användare.

- När du använder Azure Active Directory autentisering ska du ange Azure Active Directory användare i en Azure Active Directory säkerhets grupp. Skapa en oberoende databasanvändare för gruppen. Placera en eller flera databas användare i en anpassad databas roll med vissa behörigheter som är lämpliga för den gruppen av användare.

- När du använder SQL-autentisering skapar du inneslutna databas användare i databasen. Placera en eller flera databas användare i en anpassad databas roll med vissa behörigheter som är lämpliga för den gruppen av användare.

  > [!NOTE]
  > Du kan också använda grupper för icke-inneslutna databas användare.

Du bör bekanta dig med följande funktioner som kan användas för att begränsa eller utöka behörigheter:

- [Personifiering](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) och [modulsignering](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) kan användas för att säkert höja behörigheter tillfälligt.
- [Säkerhet på radnivå](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kan användas som en begränsning av vilka rader en användare kan komma åt.
- [Datamaskning](sql-database-dynamic-data-masking-get-started.md) kan användas för att begränsa exponering av känsliga data.
- [Lagrade procedurer](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) kan användas för att begränsa de åtgärder som kan utföras i databasen.

## <a name="next-steps"></a>Nästa steg

En översikt över alla säkerhetsfunktioner i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md).
