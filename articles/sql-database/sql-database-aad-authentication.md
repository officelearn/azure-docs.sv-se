---
title: Azure Active Directory-autentisering – Azure SQL | Microsoft Docs
description: Lär dig mer om hur du använder Azure Active Directory för autentisering med SQL Database Managed Instance och SQL Data Warehouse
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 86e60f339af3d6d467b68d5d3b27d77a9861add1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244091"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>Använda Azure Active Directory-autentisering för autentisering med SQL

Azure Active Directory-autentisering är en mekanism för att ansluta till Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) med hjälp av identiteter i Azure Active Directory (AD Azure). 

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

Med Azure AD-autentisering kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera databasanvändare och förenklar hanteringen av behörighet. Följande: fördelar

- Det är ett alternativ till SQL Server-autentisering.
- Hjälper dig att stoppa spridning av användaridentiteter över databasservrar.
- Tillåter lösenordsrotation i en och samma plats.
- Kunder kan hantera databasbehörigheter med hjälp av externa (Azure AD)-grupper.
- Men kan inte lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
- Azure AD-autentisering använder oberoende databasanvändare för att autentisera identiteter på databasnivå.
- Azure AD stöder tokenbaserad autentisering för program som ansluter till SQL-databas.
- Azure AD-autentisering har stöd för AD FS (domän federation) eller interna användare/lösenord autentisering för en lokal Azure Active Directory utan domänsynkronisering.  
- Azure AD stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal-autentisering, inklusive Multi-Factor Authentication (MFA).  MFA innehåller stark autentisering med en rad enkla verifieringsalternativ – telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp. Mer information finns i [SSMS-stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

> [!NOTE]  
> Ansluta till SQL Server som körs på en Azure virtuell dator stöds inte med ett Azure Active Directory-konto. Använd en domän Active Directory-konto i stället.  

Konfigurationen omfattar följande procedurer för att konfigurera och använda Azure Active Directory-autentisering.

1. Skapa och fylla i Azure AD.
2. Valfritt: Koppla eller ändra active directory som är associerade till din Azure-prenumeration.
3. Skapa en Azure Active Directory-administratör för Azure SQL Database-servern, den hanterade instansen eller [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Konfigurera klientdatorer.
5. Skapa oberoende databasanvändare i databasen som mappats till Azure AD-identiteter.
6. Ansluta till databasen med hjälp av Azure AD-identiteter.

> [!NOTE]
> Om du vill lära dig mer om att skapa och fylla i Azure AD och konfigurera Azure AD med Azure SQL Database Managed Instance och SQL Data Warehouse, se [konfigurera Azure AD med Azure SQL Database](sql-database-aad-authentication-configure.md).

## <a name="trust-architecture"></a>Förtroendearkitektur

I följande övergripande diagram sammanfattar lösningsarkitekturen för att använda Azure AD-autentisering med Azure SQL Database. Samma koncept gäller för SQL Data Warehouse. För att stödja Azure AD-lösenord för interna användare, anses endast molndelen och Azure AD-/ Azure SQL Database. För federerade autentisering (eller användarlösenord för Windows-autentiseringsuppgifter) kommunikation med AD FS-blocket krävs. Pilarna anger kommunikation sökvägar.

![diagram för aad-autentisering][1]

Följande diagram visar federation, förtroende och värdrelationer som gör att en klient för att ansluta till en databas genom att skicka in en token. Token autentiseras av en Azure AD och är betrodd av databasen. Kund 1 kan representera en Azure Active Directory med interna användare eller en Azure AD med federerade användare. Kund 2 representerar en möjlig lösning, inklusive importerade användare. i det här exemplet kommer från en extern Azure Active Directory med AD FS som synkroniseras med Azure Active Directory. Det är viktigt att förstå att åtkomst till en databas med Azure AD-autentisering kräver att värdbaserade prenumerationen är associerad med Azure AD. Samma prenumeration måste användas för att skapa SQL-Server som är värd för Azure SQL Database eller SQL Data Warehouse.

![prenumeration relation][2]

## <a name="administrator-structure"></a>Struktur för administratör

När du använder Azure AD-autentisering, finns det två administratörskonton för SQL Database-server och hanterad instans den ursprungliga SQL Server-administratören och Azure AD-administratör. Samma koncept gäller för SQL Data Warehouse. Bara administratörer baserat på ett Azure AD-konto kan skapa den första Azure AD finns databasanvändaren i en användardatabas. Inloggningsuppgifter för Azure AD-administratörer kan vara en Azure AD-användare eller en Azure AD-grupp. När administratören är ett gruppkonto, kan den användas av alla medlemmar i gruppen så att flera Azure AD-administratörer för SQL Server-instansen. Med hjälp av gruppkonto som administratör ger ökad hanterbarhet så att du kan lägga till och ta bort medlemmar i Azure AD utan att ändra de användare eller behörigheter i SQL Database centralt. Endast en Azure AD-administratör (användare eller grupp) kan konfigureras när som helst.

![Admin-struktur][3]

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya användare, måste du ha den `ALTER ANY USER` behörighet i databasen. Den `ALTER ANY USER` behörighet kan beviljas till någon databasanvändare. Den `ALTER ANY USER` behörighet lagras också av server-administratörskonton och databasanvändare med den `CONTROL ON DATABASE` eller `ALTER ON DATABASE` behörighet för den databasen och av medlemmar i den `db_owner` databasrollen.

Om du vill skapa en oberoende databasanvändare i Azure SQL Database Managed Instance eller SQL Data Warehouse, måste du ansluta till databasen eller instansen med hjälp av en Azure AD-identitet. Om du vill skapa den första oberoende databasanvändaren, måste du ansluta till databasen med hjälp av en Azure AD-administratör (som är ägaren av databasen). Detta visas i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md). Alla Azure AD-autentisering är bara möjligt om Azure AD-administratören har skapats för Azure SQL Database eller SQL Data Warehouse-server. Om Azure Active Directory-administratör har tagits bort från servern, kan befintliga Azure Active Directory-användare som skapats tidigare i SQL Server inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-funktioner och begränsningar

Följande medlemmar av Azure AD kan etableras i Azure SQL-server eller SQL Data Warehouse:

- Interna medlemmar: en medlem som skapats i Azure AD i den hanterade domänen eller en kund-domän. Mer information finns i [lägga till ditt eget domännamn i Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Federerad domänmedlemmar: en medlem som skapats i Azure AD med en federerad domän. Mer information finns i [Microsoft Azure har nu stöd för federation med Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Importerade medlemmar från andra Azure AD som är interna eller externa domänmedlemmar.
- Active Directory-grupper skapas som säkerhetsgrupper.

Azure AD-begränsningar som rör hanterade instansen:

- Endast Azure AD-administratören kan skapa databaser, Azure AD-användare är begränsade till en enda databas och har inte denna behörighet
- Databasen ägarskap:
  - Azure AD-huvudnamn kan inte ändra ägarskap för databasen (ALTER AUTHORIZATION på databas) och kan inte anges som ägare.
  - Ingen ägare har angetts för databaser som skapats av Azure AD-administratör (owner_sid fält i sys.sysdatabases är 0x1).
- SQL Agent kan inte hanteras när du har loggat in med Azure AD-huvudkonton.
- Azure AD-administratören kan inte personifieras med EXECUTE AS
- DAC-anslutning stöds inte med Azure AD-huvudkonton.

Dessa systemfunktioner returnerar NULL-värden när den körs under Azure AD-huvudkonton:

- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Ansluta med hjälp av Azure AD-identiteter

Azure Active Directory-autentisering har stöd för följande metoder för att ansluta till en databas med Azure AD-identiteter:

- Med integrerad Windows-autentisering
- Med hjälp av en Azure AD-huvudnamn och ett lösenord
- Med hjälp av programmet tokenautentisering

### <a name="additional-considerations"></a>Annat som är bra att tänka på

- För att förbättra hanterbarhet, vi rekommenderar att du etablerar en dedikerad Azure AD som en administratör.   
- Endast en Azure AD-administratör (användare eller grupp) kan konfigureras för en Azure SQL Database-servern, hanterad instans eller Azure SQL Data Warehouse när som helst.
- Endast en Azure AD-administratör för SQL Server kan först ansluta till Azure SQL Database-server, hanterad instans eller Azure SQL Data Warehouse med hjälp av ett Azure Active Directory-konto. Active Directory-administratör kan konfigurera efterföljande Azure AD databasen användare.   
- Vi rekommenderar att du anger timeout för anslutning till 30 sekunder.   
- SQL Server 2016 Management Studio och SQL Server Data Tools för Visual Studio 2015 (version 14.0.60311.1April 2016 eller senare) stöder Azure Active Directory-autentisering. (Azure AD-autentisering som stöds av den **.NET Framework Data Provider Pro SqlServer**; minst version .NET Framework 4.6). Därför de senaste versionerna av dessa verktyg och -datanivåprogram (DAC och. BACPAC) kan använda Azure AD-autentisering.   
- [ODBC version 13,1](https://www.microsoft.com/download/details.aspx?id=53339) stöder Azure Active Directory-autentisering men `bcp.exe` inte kan ansluta med hjälp av Azure Active Directory-autentisering eftersom den använder en äldre ODBC-provider.   
- `sqlcmd` har stöd för Azure Active Directory-autentisering från och med version 13,1 som är tillgängliga från den [Download Center](https://go.microsoft.com/fwlink/?LinkID=825643).
- SQL Server Data Tools för Visual Studio 2015 kräver minst April 2016-versionen av Data Tools (version 14.0.60311.1). Azure AD-användare är för närvarande inte visas i SSDT Object Explorer. Som en lösning kan du visa användarna i [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
- [Microsoft JDBC Driver 6.0 för SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) har stöd för Azure AD-autentisering. Se även [inställning av anslutningsegenskaper](https://msdn.microsoft.com/library/ms378988.aspx).   
- PolyBase kan inte autentisera med hjälp av Azure AD-autentisering.   
- Azure AD-autentisering är stöd för SQL-databas med Azure-portalen **Importera databas** och **exportera databasen** blad. Import och export med Azure AD-autentisering stöds också från PowerShell-kommando.   
- Azure AD-autentisering stöds för SQL Database Managed Instance och SQL Data Warehouse med hjälp av CLI. Mer information finns i [konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md) och [SQL Server - az SQLServer](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig mer om att skapa och fylla i Azure AD och konfigurera Azure AD med Azure SQL Database eller Azure SQL Data Warehouse, se [konfigurera och hantera Azure Active Directory-autentisering med SQL Database Managed Instance eller SQL Data Warehouse ](sql-database-aad-authentication-configure.md).
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
