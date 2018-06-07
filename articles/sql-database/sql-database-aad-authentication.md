---
title: Azure Active Directory-auth - Azure SQL (översikt) | Microsoft Docs
description: Lär dig mer om hur du använder Azure Active Directory för autentisering med SQL-databas, hanterade-instans och SQL Data Warehouse
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: c016d593f62e2f0616b426a87baf8d9390645b4a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645073"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Använda Azure Active Directory-autentisering för autentisering med SQL-databas, hanteras instans eller SQL Data Warehouse
Azure Active Directory-autentisering är en mekanism för anslutning till Microsoft Azure SQL Database och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (AD Azure). Med Azure AD-autentisering kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera användare och förenklar hantering av behörighet. Följande: fördelar

* Det ger ett alternativ till SQL Server-autentisering.
* Hjälper till att stoppa spridning av användaridentiteter över databasservrar.
* Tillåter lösenord rotation i en enda plats.
* Kunder kan hantera databasen med hjälp av externa (Azure AD)-grupper.
* Men du kan inte lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
* Azure AD-autentisering använder oberoende databasanvändare för att autentisera identiteter på databasnivå.
* Azure AD stöder tokenbaserad autentisering för program som ansluter till SQL-databas.
* Azure AD authentication stöder AD FS (domänfederation) eller intern användarlösenord autentisering för en lokal Active Directory med Azure utan domänsynkronisering.  
* Azure AD stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal-autentisering, vilket innefattar Multi-Factor Authentication (MFA).  MFA ingår stark autentisering med ett antal alternativ för enkel verifiering – telefonsamtal, textmeddelande, smartkort och PIN-kod eller meddelande i mobilappen. Mer information finns i [SSMS stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  Ansluter till SQL Server körs på en Azure VM stöds inte med ett Azure Active Directory-konto. Använd en domän Active Directory-konto i stället.  

Konfigurationsstegen innehåller följande procedurer för att konfigurera och använda Azure Active Directory-autentisering.

1. Skapa och fylla i Azure AD.
2. Valfritt: Koppla eller ändra active directory som associeras med din Azure-prenumeration.
3. Skapa en Azure Active Directory-administratör för Azure SQL Database-server, hanterade-instans eller [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurera klientdatorer.
5. Skapa oberoende databasanvändare i din databas som mappats till Azure AD identiteter.
6. Ansluta till databasen med hjälp av Azure AD identiteter.

> [!NOTE]
> Information om hur du skapar och fylla i Azure AD och konfigurera Azure AD med Azure SQL Database, hanterade-instans och SQL Data Warehouse finns [konfigurera Azure AD med Azure SQL Database](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Förtroendearkitektur
Följande övergripande diagram sammanfattar lösningsarkitektur för genom att använda Azure AD med Azure SQL Database. Samma koncept gäller för SQL Data Warehouse. För att stödja interna användarlösenord för Azure AD, anses endast molndelen och Azure AD-/ Azure SQL Database. Till stöd för externa autentisering (eller användarlösenord för Windows-autentiseringsuppgifter) krävs kommunikation med AD FS-block. Pilarna visar kommunikation vägarna.

![diagram för aad-autentisering][1]

Följande diagram visar federation, förtroende och värdrelationer som gör att en klient för att ansluta till en databas genom att skicka en token. Token autentiseras av en Azure AD och är betrott av databasen. Kund 1 motsvarar ett Azure Active Directory med interna användare eller en Azure AD med externa användare. Kunden 2 representerar en möjlig lösning inklusive importerade användare. i det här exemplet kommer från en extern Azure Active Directory med AD FS som synkroniseras med Azure Active Directory. Det är viktigt att förstå att åtkomst till en databas med Azure AD-autentisering kräver att värd-prenumerationen är kopplad till Azure AD. Samma prenumeration måste användas för att skapa SQL-Server som värd för Azure SQL Database eller SQL Data Warehouse.

![prenumerationen relation][2]

## <a name="administrator-structure"></a>Administratören struktur
När du använder Azure AD-autentisering, finns det två administratörskonton för SQL Database-server och hanterade instans; den ursprungliga SQL Server-administratören och Azure AD-administratör. Samma koncept gäller för SQL Data Warehouse. Endast administratören baserat på en Azure AD-kontot kan skapa den första databasanvändaren för Azure AD finns i en användardatabas. Inloggningen för Azure AD-administratör kan vara en Azure AD-användare eller en grupp i Azure AD. När administratören är ett gruppkonto, kan den användas av en gruppmedlem så att flera Azure AD-administratörer för SQL Server-instansen. Med hjälp av gruppkontot som administratör förbättrar hanterbarhet genom att du kan lägga till och ta bort medlemmar i Azure AD utan att ändra användare eller behörighet i SQL-databas centralt. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![Admin-struktur][3]

## <a name="permissions"></a>Behörigheter
Om du vill skapa nya användare, måste du ha den `ALTER ANY USER` behörighet i databasen. Den `ALTER ANY USER` behörighet kan beviljas åtkomst till alla databasanvändare. Den `ALTER ANY USER` behörighet också innehas av server-administratörskonton och databasanvändare med den `CONTROL ON DATABASE` eller `ALTER ON DATABASE` behörighet för den här databasen och av medlemmar i den `db_owner` databasrollen.

Om du vill skapa en innesluten databasanvändare i Azure SQL Database, hanteras instans eller SQL Data Warehouse, måste du ansluta till databasen eller -instans med en Azure AD-identitet. Om du vill skapa den första användaren innesluten databas måste du ansluta till databasen med hjälp av en Azure AD-administratör (som är ägare till databasen). Detta visas i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md). Alla Azure AD-autentisering är bara möjligt om Azure AD-administratör har skapats för Azure SQL Database eller SQL Data Warehouse-server. Om Azure Active Directory-administratören har tagits bort från servern, kan befintliga Azure Active Directory-användare som skapade tidigare i SQL Server inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-funktioner och begränsningar
Följande medlemmar av Azure AD kan etableras i Azure SQL-server eller SQL Data Warehouse:

- Intern medlemmar: en medlem som skapats i Azure AD i den hanterade domänen eller en kund-domän. Mer information finns i [lägga till ditt eget domännamn i Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Federerad domänmedlemmar: en medlem som skapats i Azure AD med en federerad domän. Mer information finns i [Microsoft Azure stöder nu federation med Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Importerade medlemmar från andra Azure AD som är medlemmar i en intern eller extern domän.
- Active Directory-grupper skapas som säkerhetsgrupper.

Azure AD-begränsningar som rör hanterade instans:
- Endast Azure AD-administratör kan skapa databaser, Azure AD-användare är begränsade till en enda DB och har inte denna behörighet
- Ägarskap för databasen:
  - Azure AD-säkerhetsobjekt kan inte ändra ägarskap för databasen (ändra TILLSTÅNDET för databas) och kan inte anges som ägare.
  - Ingen ägare har angetts för databaser som skapats i Azure AD-administratör (owner_sid fält i sys.sysdatabases är 0x1).
- SQL-agenten kan inte hanteras när du har loggat in med Azure AD-säkerhetsobjekt. 
- Azure AD-administratör kan inte personifieras med EXECUTE AS
- DAC-anslutning stöds inte med Azure AD-säkerhetsobjekt. 

Dessa systemfunktioner returnera NULL-värden när den körs under Azure AD säkerhetsobjekt:
- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Ansluta med hjälp av Azure AD identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med Azure AD identiteter:

* Med hjälp av integrerad Windows-autentisering
* Med hjälp av en Azure AD-huvudnamn och ett lösenord
* Med hjälp av autentisering för token

### <a name="additional-considerations"></a>Annat som är bra att tänka på

* Om du vill förbättra hanterbarhet, rekommenderar vi du etablerar en dedikerad Azure AD som en administratör.   
* Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras för en Azure SQL Database-server, hanteras instans eller Azure SQL Data Warehouse när som helst.   
* Endast en Azure AD-administratör för SQL Server kan först ansluta till Azure SQL Database-server, hanteras instans eller Azure SQL Data Warehouse med ett Azure Active Directory-konto. Active Directory-administratören kan konfigurera följande Azure AD-databas användare.   
* Vi rekommenderar att du anger timeout för anslutningen till 30 sekunder.   
* SQL Server 2016 Management Studio och SQL Server Data Tools för Visual Studio 2015 (version 14.0.60311.1April 2016 eller senare) stöder Azure Active Directory-autentisering. (Azure AD-autentisering stöds av den **.NET Framework-dataprovider för SqlServer**; minst version .NET Framework 4.6). Därför de senaste versionerna av dessa verktyg och -datanivåprogram (DAC och. BACPAC) kan använda Azure AD-autentisering.   
* [ODBC version 13,1](https://www.microsoft.com/download/details.aspx?id=53339) stöder Azure Active Directory-autentisering men `bcp.exe` kan inte ansluta med Azure Active Directory-autentisering, eftersom den använder en äldre ODBC-provider.   
* `sqlcmd` stöder Azure Active Directory-autentisering från och med version 13,1 som är tillgängliga från den [Download Center](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools för Visual Studio 2015 kräver minst April 2016-versionen av Data Tools (version 14.0.60311.1). Azure AD-användare visas för närvarande inte i SSDT Object Explorer. Som en tillfällig lösning kan du visa användarna i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* [Microsoft JDBC Driver 6.0 för SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) stöder Azure AD-autentisering. Se även [inställning av anslutningsegenskaper](https://msdn.microsoft.com/library/ms378988.aspx).   
* PolyBase kan inte autentiseras med hjälp av Azure AD-autentisering.   
* Azure AD-autentisering stöds för SQL-databas i Azure Portal **Importera databas** och **exportera databasen** blad. Importera och exportera med Azure AD-autentisering stöds också från PowerShell-kommandot.   
* Azure AD-autentisering stöds för SQL-databas, hanterade-instans och SQL Data Warehouse med hjälp av CLI. Mer information finns i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md) och [SQL Server - az SQLServer](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Nästa steg
- Information om hur du skapar och fylla i Azure AD och konfigurera Azure AD med Azure SQL Database eller Azure SQL Data Warehouse finns [konfigurera och hantera Azure Active Directory-autentisering med SQL-databas, hanteras instans eller SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
- En översikt över åtkomst och kontroll i SQL Database finns i [Åtkomst och kontroll för SQL Database](sql-database-control-access.md).
- En översikt över inloggningar, användare och databasroller i SQL Database finns i [Inloggningar, användare och databasroller](sql-database-manage-logins.md).
- Mer information om huvudkonton finns i [Huvudkonton](https://msdn.microsoft.com/library/ms181127.aspx).
- Mer information om databasroller finns [Databasroller](https://msdn.microsoft.com/library/ms189121.aspx).
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

