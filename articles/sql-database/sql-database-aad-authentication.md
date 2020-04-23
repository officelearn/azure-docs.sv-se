---
title: Azure Active Directory
description: Lär dig mer om hur du använder Azure Active Directory för autentisering med SQL Database, hanterad instans och Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/27/2020
ms.openlocfilehash: 58f40bc7406048df2b052bea799bcbf6466fbbae
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421113"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Använd Azure Active Directory autentisering för autentisering med SQL

Azure Active Directory autentisering är en mekanism för att ansluta till Azure [SQL Database](sql-database-technical-overview.md), [hanterad instans](sql-database-managed-instance.md)och [Azure Synapse Analytics (tidigare Azure SQL Data Warehouse)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (Azure AD). 

> [!NOTE]
> Den här artikeln gäller Azure SQL Server och både SQL Database och Azure-Synapse. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure-Synapse.

Med Azure AD-autentisering kan du hantera identiteter för databasanvändare och andra Microsoft-tjänster på en enda central plats. Central ID-hantering ger en enda plats för hantering av databasanvändare och förenklar behörighetshanteringen. Här är några av fördelarna:

- Det är ett alternativ till SQL Server-autentisering
- Hjälper till att stoppa spridningen av användar identiteter mellan databas servrar.
- Lösenord kan roteras på ett och samma ställe
- Kunderna kan hantera sin databasåtkomst via externa grupper (Azure AD)
- Du slipper lagra lösenord om du aktiverar integrerad Windows-autentisering eller andra former av autentisering som stöds av Azure Active Directory
- Med Azure AD-autentisering används oberoende databasanvändare till att autentisera identiteter på databasnivå
- Azure AD har stöd för tokenbaserad autentisering för appar som ansluter till SQL Database
- Azure AD-autentisering stöder:
  - Endast Azure AD-moln identiteter
  - Azure AD Hybrid identiteter som stöder:
    - Molnbaserad autentisering med två alternativ tillsammans med sömlös enkel inloggning (SSO): **vidarekoppling** av autentisering och **lösen ord för hash** -autentisering
    - Federerad autentisering
  - Mer information om autentiseringsmetoder för Azure AD och vilken av dem som ska väljas finns i följande artikel:
    - [Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](../active-directory/hybrid/choose-ad-authn.md)
- Azure AD har stöd för anslutning från SQL Server Management Studio som använder Active Directory Universal Authentication, inklusive Multi-Factor Authentication (MFA).  I MFA används stark autentisering via en rad enkla verifieringsalternativ – telefonsamtal, SMS, smarta kort med PIN-kod eller avisering i mobilappen. Mer information finns i [SSMS-stöd för Azure AD MFA med SQL Database och Azure Synapse](sql-database-ssms-mfa-authentication.md)
- Azure AD har stöd för liknande anslutningar från SSDT (SQL Server Data Tools) som använder Active Directory Interactive Authentication. Mer information finns i [Azure Active Directory support i SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory)

> [!NOTE]  
> Det går inte att ansluta till SQL Server som körs på en virtuell Azure-dator med ett Azure Active Directory konto. Använd ett domän Active Directory konto i stället.  

Konfigurations stegen innehåller följande procedurer för att konfigurera och använda Azure Active Directory autentisering.

1. Skapa och fyll i Azure AD.
2. Valfritt: associera eller ändra Active Directory som för närvarande är associerat med din Azure-prenumeration.
3. Skapa en Azure Active Directory administratör för Azure SQL Database-servern, den hanterade instansen eller [Azure-Synapse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurera klient datorerna.
5. Skapa inneslutna databas användare i databasen som har mappats till Azure AD-identiteter.
6. Anslut till databasen med hjälp av Azure AD-identiteter.

> [!NOTE]
> Information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure SQL Database, hanterad instans och Azure-Synapse finns i [Konfigurera Azure AD med Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Förtroende arkitektur

- För att ge stöd åt Azure AD Native User Password anses endast moln delen och Azure AD/Azure SQL Database.
- Använd Azure Active Directory autentiseringsuppgifter från en federerad eller hanterad domän som har kon figurer ATS för sömlös enkel inloggning för direkt inloggning och hash-autentisering för lösen ord, om du vill använda autentiseringsuppgifter för enkel inloggning i Windows (eller användare/lösen ord för Windows-autentiseringsuppgifter). Mer information finns i [Azure Active Directory sömlös enkel inloggning](../active-directory/hybrid/how-to-connect-sso.md).
- För att stödja federerad autentisering (eller användare/lösen ord för Windows-autentiseringsuppgifter) krävs kommunikation med ADFS-block.

Mer information om Hybrid identiteter för Azure Active Directory, installation och synkronisering finns i följande artiklar:

- Password hash-autentisering – [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Direktautentisering – [Azure Active Directory direktautentisering](../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federerad autentisering – [distribuera Active Directory Federation Services (AD FS) i Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) och [Azure AD Connect och Federation](../active-directory/hybrid/how-to-connect-fed-whatis.md)

Ett exempel på en federerad autentisering med ADFS-infrastruktur (eller användare/lösen ord för Windows-autentiseringsuppgifter) finns i diagrammet nedan. Pilarna visar kommunikations vägar.

![AAD-auth-diagram][1]

Följande diagram visar de Federations-, förtroende-och värd relationer som gör det möjligt för en klient att ansluta till en databas genom att skicka en token. Token autentiseras av en Azure AD och är betrodd av databasen. Kund 1 kan representera ett Azure Active Directory med interna användare eller en Azure AD med federerade användare. Kund 2 representerar en möjlig lösning inklusive importerade användare. i det här exemplet kommer från en federerad Azure Active Directory med ADFS synkroniseras med Azure Active Directory. Det är viktigt att förstå att åtkomst till en databas med Azure AD-autentisering kräver att värd prenumerationen är kopplad till Azure AD. Samma prenumeration måste användas för att skapa SQL Server som är värd för Azure SQL Database eller Azure-Synapse.

![prenumerations relation][2]

## <a name="administrator-structure"></a>Administratörs struktur

När du använder Azure AD-autentisering finns det två administratörs konton för SQL Database-servern och en hanterad instans. den ursprungliga SQL Server administratören och Azure AD-administratören. Samma koncept gäller för Azure-Synapse. Endast administratören som baseras på ett Azure AD-konto kan skapa den första Azure AD-inneslutna databas användaren i en användar databas. Inloggningen för Azure AD-administratören kan vara en Azure AD-användare eller en Azure AD-grupp. När administratören är ett grupp konto kan den användas av alla grupp medlemmar, vilket möjliggör flera Azure AD-administratörer för SQL Server-instansen. Om du använder grupp kontot som administratör ökar hanterbarheten genom att du kan lägga till och ta bort grupp medlemmar centralt i Azure AD utan att ändra användare eller behörigheter i SQL Database. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![administratörs struktur][3]

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya användare måste du ha `ALTER ANY USER` behörighet i-databasen. `ALTER ANY USER` Behörigheten kan beviljas till alla databas användare. `ALTER ANY USER` Behörigheten hålls också av Server administratörs kontona och databas användare med behörigheten `CONTROL ON DATABASE` eller `ALTER ON DATABASE` för databasen, och av medlemmar i `db_owner` databas rollen.

Om du vill skapa en innesluten databas användare i Azure SQL Database, hanterad instans eller Azure-Synapse måste du ansluta till databasen eller instansen med hjälp av en Azure AD-identitet. Om du vill skapa den första inneslutna databas användaren måste du ansluta till databasen med hjälp av en Azure AD-administratör (som är ägare till databasen). Detta visas i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure-Synapse](sql-database-aad-authentication-configure.md). Alla Azure AD-autentiseringar är bara möjliga om Azure AD-administratören har skapats för Azure SQL Database eller Azure-Synapse. Om Azure Active Directory administratören har tagits bort från servern kan befintliga Azure Active Directory användare som skapats tidigare i SQL Server inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="azure-ad-features-and-limitations"></a>Funktioner och begränsningar i Azure AD

- Följande Azure AD-medlemmar kan tillhandahållas i Azure SQL Server eller Azure Synapse:

  - Ursprungliga medlemmar: en medlem som skapats i Azure AD i den hanterade domänen eller i en kund domän. Mer information finns i [lägga till ditt eget domän namn i Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
  - Medlemmar i en Active Directory domän som är federerad med Azure Active Directory på en hanterad domän som kon figurer ATS för sömlös enkel inloggning med hash-autentisering direkt eller lösenordsautentisering. Mer information finns i [Microsoft Azure stöder nu Federation med Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) och [Azure Active Directory sömlös enkel inloggning](../active-directory/hybrid/how-to-connect-sso.md).
  - Importerade medlemmar från andra Azure AD-medlemmar som är interna eller federerade domän medlemmar.
  - Active Directory grupper som skapats som säkerhets grupper.

- Azure AD-användare som är en del av en grupp `db_owner` som har Server rollen kan inte använda syntaxen **[skapa databasens begränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** mot Azure SQL Database och Azure-Synapse. Följande fel meddelande visas:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Ge `db_owner` rollen direkt till den enskilda Azure AD-användaren för att minimera problemet med att **skapa databasen med begränsade autentiseringsuppgifter** .

- Dessa system funktioner returnerar NULL-värden när de körs under Azure AD-huvud konton:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>Hanterade instanser

- Azure AD server-Huvudkonton (inloggningar) och användare stöds som en förhands gransknings funktion för [hanterade instanser](sql-database-managed-instance.md).
- Att ställa in Azure AD server-Huvudkonton (inloggningar) som är mappade till en Azure AD-grupp som databas ägare stöds inte i [hanterade instanser](sql-database-managed-instance.md).
    - En förlängning av detta är att när en grupp läggs till som en del av `dbcreator` Server rollen kan användare från den här gruppen ansluta till den hanterade instansen och skapa nya databaser, men kommer inte att kunna komma åt databasen. Detta beror på att den nya databas ägaren är SA och inte Azure AD-användaren. Det här problemet visar inte om den enskilda användaren läggs till i `dbcreator` Server rollen.
- SQL Agent Management och jobb körning stöds för Azure AD server-Huvudkonton (inloggningar).
- Åtgärder för databassäkerhetskopiering och -återställning kan köras av Azure AD-serverhuvudkonton (inloggningar).
- Granskning av alla satser som rör Azure AD server-Huvudkonton (inloggningar) och autentiserings händelser stöds.
- Dedikerad administratörs anslutning för Azure AD server-Huvudkonton (inloggningar) som är medlemmar i Server rollen sysadmin stöds.
    - Stöds via SQLCMD-verktyget och SQL Server Management Studio.
- Inloggningsutlösare stöds för inloggningshändelser som kommer från Azure AD-serverhuvudkonton (inloggningar).
- Service Broker-och DB-e-post kan konfigureras med hjälp av ett Azure AD server-huvudobjekt (inloggning).


## <a name="connecting-using-azure-ad-identities"></a>Ansluta med Azure AD-identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med hjälp av Azure AD-identiteter:

- Azure Active Directory lösen ord
- Azure Active Directory integrerad
- Azure Active Directory universell med MFA
- Använda autentisering med program-token

Följande autentiseringsmetoder stöds för Azure AD server-Huvudkonton (inloggningar):

- Azure Active Directory lösen ord
- Azure Active Directory integrerad
- Azure Active Directory universell med MFA


### <a name="additional-considerations"></a>Annat som är bra att tänka på

- För att förbättra hanterbarheten rekommenderar vi att du etablerar en dedikerad Azure AD-grupp som administratör.   
- Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras för en Azure SQL Database Server eller Azure-Synapse när som helst.
  - Tillägget av Azure AD server-huvudobjekten (inloggningar) för hanterade instanser gör det möjligt att skapa flera Azure AD server-huvudobjekt (inloggningar) som kan läggas `sysadmin` till i rollen.
- Endast en Azure AD-administratör för SQL Server kan ansluta till Azure SQL Database-servern, hanterad instans eller Azure-Synapse med ett Azure Active Directory-konto. Active Directory-administratören kan konfigurera efterföljande Azure AD Database-användare.   
- Vi rekommenderar att du ställer in tids gränsen för anslutningen på 30 sekunder.   
- SQL Server 2016 Management Studio och SQL Server Data Tools för Visual Studio 2015 (version 14.0.60311.1 april 2016 eller senare) stöder Azure Active Directory autentisering. (Azure AD-autentisering stöds av **.NET Framework Data Provider för SQLServer**; minst version .NET Framework 4,6). Därför är de senaste versionerna av dessa verktyg och data skikts program (DAC och. BACPAC) kan använda Azure AD-autentisering.   
- Från och med version 15.0.1, [SQLCMD-verktyget](/sql/tools/sqlcmd-utility) och BCP- [verktyget](/sql/tools/bcp-utility) stöds Active Directory interaktiv autentisering med MFA.
- SQL Server Data Tools för Visual Studio 2015 kräver minst april 2016-versionen av data verktyg (version 14.0.60311.1). För närvarande visas inte Azure AD-användare i SSDT Object Explorer. Som en lösning kan du Visa användarna i [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC-drivrutin 6,0 för SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) stöder Azure AD-autentisering. Se även [Ange anslutnings egenskaper](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase kan inte autentiseras med Azure AD-autentisering.   
- Azure AD-autentisering stöds för SQL Database av bladet Azure Portal **Importera databas** och **Exportera databas** . Import och export med Azure AD-autentisering stöds också från PowerShell-kommandot.   
- Azure AD-autentisering stöds för SQL Database, hanterad instans och Azure-Synapse med hjälp av CLI. Mer information finns i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure-Synapse](sql-database-aad-authentication-configure.md) och [SQL Server-AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar och fyller i Azure AD och sedan konfigurerar Azure AD med Azure SQL Database eller Azure-Synapse finns i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database, hanterad instans eller Azure Synapse](sql-database-aad-authentication-configure.md).
- En själv studie kurs om hur du använder Azure AD server-huvudobjekt (inloggningar) med hanterade instanser finns i [Azure AD server-Huvudkonton (inloggningar) med hanterade instanser](sql-database-managed-instance-aad-security-tutorial.md)
- En översikt över inloggningar, användare, databas roller och behörigheter i SQL Database finns i [inloggningar, användare, databas roller och behörigheter](sql-database-manage-logins.md).
- Mer information om huvudkonton finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
- Mer information om databasroller finns [Databasroller](https://msdn.microsoft.com/library/ms189121.aspx).
- En syntax för att skapa Azure AD server-huvudobjekt (inloggningar) för hanterade instanser finns i [Skapa inloggning](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).
- Mer information om brandväggsregler i SQL Database finns [SQL Database-brandväggsregler](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
