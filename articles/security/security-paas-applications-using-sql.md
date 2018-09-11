---
title: Skydda PaaS-databaser i Azure | Microsoft Docs
description: " Läs mer om Azure SQL Database och SQL Data Warehouse-säkerhet metoder för att skydda din PaaS-webbprogram och mobilappar. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 00b2b249f5889888f34d57fd1577ccfea776d00c
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347978"
---
# <a name="securing-paas-databases-in-azure"></a>Skydda PaaS-databaser i Azure

I den här artikeln diskuterar vi en samling [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) och [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) rekommenderade säkerhetsmetoder för att skydda din PaaS-webbprogram och mobilappar. Dessa metodtips härleds från vår erfarenhet av Azure och erfarenheter från kunder som dig själv.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL Database och SQL Data Warehouse
[Azure SQL Database](../sql-database/sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tillhandahåller en relationsdatabastjänst för din Internet-baserade program. Låt oss titta på tjänster som hjälper dig skydda dina program och data när du använder Azure SQL Database och SQL Data Warehouse i en PaaS-distribution:

- Azure Active Directory-autentisering (i stället för SQL Server-autentisering)
- Azure SQL-brandväggen
- Transparent datakryptering (TDE)

## <a name="best-practices"></a>Bästa praxis

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Använda en centraliserad identitetsdatabas för autentisering och auktorisering

Azure SQL-databaser kan konfigureras för att använda en av två typer av autentisering:

- **SQL-autentisering** använder ett användarnamn och lösenord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan autentisera du till valfri databas på servern som databasens ägare.

- **Azure Active Directory-autentisering** använder identiteter som hanteras av Azure Active Directory och har stöd för hanterade och integrerade domäner. Om du vill använda Azure Active Directory-autentisering, måste du skapa en annan serveradministratör som kallas ”Azure AD-administratören”, som tillåts administrera Azure AD-användare och grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan.

[Azure Active Directory-autentisering](../active-directory/develop/authentication-scenarios.md) är en mekanism för att ansluta till Azure SQL Database och SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD). Azure AD tillhandahåller ett alternativ till SQL Server-autentisering så att du kan stoppa spridning av användaridentiteter över databasservrar. Azure AD-autentisering kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera databasanvändare och förenklar hanteringen av behörighet.  

Fördelar med att använda Azure AD-autentisering i stället för SQL-autentisering är:

- Tillåter lösenordsrotation i en och samma plats.
- Hanterar databasbehörigheter med hjälp av externa Azure AD-grupper.
- Eliminerar lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure AD.
- Använder innesluten databasanvändare för att autentisera identiteter på databasnivå.
- Har stöd för tokenbaserad autentisering för program som ansluter till SQL-databas.
- Stöder AD FS (domän federation) eller interna användare/lösenord autentisering för en lokal Azure AD utan domänsynkronisering.
- Stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal-autentisering, vilket innefattar [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). MFA innehåller stark autentisering med en rad enkla verifieringsalternativ – telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp. Mer information finns i [SSMS-stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Om du vill veta mer om Azure AD-autentisering, se:

- [Ansluta till SQL Database eller SQL Data Warehouse med hjälp av Azure Active Directory-autentisering](../sql-database/sql-database-aad-authentication.md)
- [Autentisera till Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Stöd för tokenbaserad autentisering för Azure SQL DB med Azure AD-autentisering](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> För att säkerställa att Azure Active Directory är ett bra alternativ för din miljö, se [Azure AD-funktioner och begränsningar](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), särskilt ytterligare överväganden.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Begränsa åtkomsten baserat på IP-adress
Du kan skapa brandväggsregler som anger intervall med godkända IP-adresser. Dessa regler kan tillämpas på både servern och databasen nivåer. Vi rekommenderar att du använder databasen brandväggsregler på databasnivå när det är möjligt att förbättra säkerheten och göra databasen mer portabel. Brandväggsregler på servernivå är bäst för administratörer och när du har många databaser med samma åtkomstkrav men du inte vill lägga tid på att konfigurera varje databas individuellt.

SQL Database standard källa IP-adressbegränsningar Tillåt åtkomst från alla Azure-adresser (inklusive andra prenumerationer och klienter). Du kan begränsa detta så att endast IP-adresser att komma åt instansen. Även med SQL-brandväggen och IP-adressbegränsningar krävs fortfarande stark autentisering. Se rekommendationer tidigare i den här artikeln.

Mer information om Azure SQL-brandväggen och IP-begränsningar finns:

- [Azure SQL Database-åtkomstkontroll](../sql-database/sql-database-control-access.md)
- [Konfigurera brandväggsregler för Azure SQL Database - översikt](../sql-database/sql-database-firewall-configure.md)
- [Konfigurera en Azure SQL Database-brandväggsregel på servernivå med hjälp av Azure portal](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data
[Transparent datakryptering (TDE)](https://msdn.microsoft.com/library/azure/bb934049) är aktiverat som standard. TDE krypterar transparent SQL Server, Azure SQL Database och Azure SQL Data Warehouse filer för data och loggfiler. TDE skyddar mot en kompromettering av direkt åtkomst till filer eller deras säkerhetskopiering. På så sätt kan du kryptera vilande data utan att ändra befintliga program. TDE bör alltid vara aktiverade; men att detta inte slutar en angripare med hjälp av sökvägen för normal åtkomst. TDE ger möjlighet att följa många lagar, bestämmelser och riktlinjerna i olika branscher.

Azure SQL hanterar viktiga relaterade problem för transparent Datakryptering. Som med transparent Datakryptering, lokala särskild försiktighet måste vidtas för att säkerställa återställning och när du flyttar databaser. I mer avancerade scenarier nycklarna kan uttryckligen hanteras i Azure Key Vault med utökningsbar nyckelhantering (se [aktivera TDE på SQL Server med hjälp av EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm)). Detta kan också för ta med din egen nyckel (BYOK) via Azure Key valv BYOK-funktionen.

Azure SQL ger dig kryptering för kolumnerna till [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). På så sätt kan endast auktoriserade program åtkomst till känsliga kolumner. Med hjälp av den här typen av kryptering begränsar SQL-frågor för krypterade kolumner till likhet-baserade värden.

Programmet filnivåkryptering bör också användas för dataåtkomst. Ibland kan du minimera data datasuveränitet frågor genom att kryptera data med en nyckel som lagras i ett land. Detta förhindrar att även oavsiktlig dataöverföring orsakar problem Eftersom det är omöjligt att dekryptera data utan nyckeln, förutsatt att en stark algoritmen används (till exempel AES-256).

Du kan använda ytterligare säkerhetsåtgärder för att skydda databasen som utformar en säker system, kryptera känsliga tillgångar och att skapa en brandvägg runt database-servrar.

## <a name="next-steps"></a>Nästa steg
Den här artikeln har du introducerats till en samling med SQL Database och SQL Data Warehouse rekommenderade säkerhetsmetoder för att skydda din PaaS-webbprogram och mobilappar. Mer information om hur du skyddar dina PaaS-distributioner finns:

- [Skydda PaaS-distributioner](security-paas-deployments.md)
- [Skydda PaaS-webb- och mobilprogram med Azure App Services](security-paas-applications-using-app-services.md)
