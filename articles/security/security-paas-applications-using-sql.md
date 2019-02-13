---
title: Skydda PaaS-databaser i Azure | Microsoft Docs
description: 'Läs mer om Azure SQL Database och SQL Data Warehouse-säkerhet metoder för att skydda din PaaS-webbprogram och mobilappar. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 22db43413b5c752decf6785a75dff22ff4a68039
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109376"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Metoder för att skydda PaaS-databaser i Azure

I den här artikeln diskuterar vi en samling [Azure SQL Database](../sql-database/sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) rekommenderade säkerhetsmetoder för att skydda din platform-as-a-service (PaaS) webbprogram och mobilappar. Dessa metodtips härleds från vår erfarenhet av Azure och erfarenheter från kunder som dig själv.

Azure SQL Database och SQL Data Warehouse tillhandahåller en relationsdatabastjänst för din internet-baserade program. Låt oss titta på tjänster som hjälper dig skydda dina program och data när du använder Azure SQL Database och SQL Data Warehouse i en PaaS-distribution:

- Azure Active Directory-autentisering (i stället för SQL Server-autentisering)
- Azure SQL-brandväggen
- Transparent datakryptering (TDE)

## <a name="use-a-centralized-identity-repository"></a>Använda en centraliserad identitetsdatabas
Azure SQL-databaser kan konfigureras för att använda en av två typer av autentisering:

- **SQL-autentisering** använder ett användarnamn och lösenord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan autentisera du till valfri databas på servern som databasens ägare.

- **Azure Active Directory-autentisering** använder identiteter som hanteras av Azure Active Directory och har stöd för hanterade och integrerade domäner. Om du vill använda Azure Active Directory-autentisering, måste du skapa en annan serveradministratör som kallas ”Azure AD-administratören”, som tillåts administrera Azure AD-användare och grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan.

[Azure Active Directory-autentisering](../active-directory/develop/authentication-scenarios.md) är en mekanism för att ansluta till Azure SQL Database och SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD). Azure AD tillhandahåller ett alternativ till SQL Server-autentisering så att du kan stoppa spridning av användaridentiteter över databasservrar. Azure AD-autentisering kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera databasanvändare och förenklar hanteringen av behörighet.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Fördelarna med att använda Azure AD i stället för SQL-autentisering
- Tillåter lösenordsrotation i en och samma plats.
- Hanterar databasbehörigheter med hjälp av externa Azure AD-grupper.
- Eliminerar lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure AD.
- Använder innesluten databasanvändare för att autentisera identiteter på databasnivå.
- Har stöd för tokenbaserad autentisering för program som ansluter till SQL-databas.
- Har stöd för domänen federation med Active Directory Federation Services (ADFS) eller interna användare/lösenord autentisering för en lokal Azure AD utan domänsynkronisering.
- Stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal-autentisering, vilket innefattar [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). MFA innehåller stark autentisering med en rad enkla verifieringsalternativ – telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp. Mer information finns i [universell autentisering med SQL Database och SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Om du vill veta mer om Azure AD-autentisering, se:

- [Använda Azure Active Directory-autentisering för autentisering med SQL Database Managed Instance eller SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md)
- [Autentisera till Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Stöd för tokenbaserad autentisering för Azure SQL DB med Azure AD-autentisering](../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> För att säkerställa att Azure Active Directory är ett bra alternativ för din miljö, se [Azure AD-funktioner och begränsningar](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Begränsa åtkomsten baserat på IP-adress
Du kan skapa brandväggsregler som anger intervall med godkända IP-adresser. Dessa regler kan tillämpas på både servern och databasen nivåer. Vi rekommenderar att du använder databasen brandväggsregler på databasnivå när det är möjligt att förbättra säkerheten och göra databasen mer portabel. Brandväggsregler på servernivå är bäst för administratörer och när du har många databaser med samma åtkomstkrav men du inte vill lägga tid på att konfigurera varje databas individuellt.

SQL Database standard källa IP-adressbegränsningar Tillåt åtkomst från alla Azure-adresser, inklusive andra prenumerationer och klienter. Du kan begränsa detta så att endast IP-adresser att komma åt instansen. Även med SQL-brandväggen och IP-adressbegränsningar krävs fortfarande stark autentisering. Se rekommendationer tidigare i den här artikeln.

Mer information om Azure SQL-brandväggen och IP-begränsningar finns:

- [Åtkomstkontroll för Azure SQL Database och SQL Data Warehouse](../sql-database/sql-database-control-access.md)
- [Azure SQL Database och SQL Data Warehouse brandväggsregler](../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Kryptera vilande data
[Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) är aktiverat som standard. TDE krypterar transparent SQL Server, Azure SQL Database och Azure SQL Data Warehouse filer för data och loggfiler. TDE skyddar mot en kompromettering av direkt åtkomst till filer eller deras säkerhetskopiering. På så sätt kan du kryptera vilande data utan att ändra befintliga program. TDE bör alltid vara aktiverade; men att detta inte slutar en angripare med hjälp av sökvägen för normal åtkomst. TDE ger möjlighet att följa många lagar, bestämmelser och riktlinjerna i olika branscher.

Azure SQL hanterar viktiga relaterade problem för transparent Datakryptering. Som med transparent Datakryptering, lokala särskild försiktighet måste vidtas för att säkerställa återställning och när du flyttar databaser. I mer avancerade scenarier kan nycklarna uttryckligen hanteras i Azure Key Vault med utökningsbar nyckelhantering. Se [aktivera transparent Datakryptering på SQLServer med hjälp av EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Detta kan också för ta med din egen nyckel (BYOK) via Azure Key valv BYOK-funktionen.

Azure SQL ger dig kryptering för kolumnerna till [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). På så sätt kan endast auktoriserade program åtkomst till känsliga kolumner. Med hjälp av den här typen av kryptering begränsar SQL-frågor för krypterade kolumner till likhet-baserade värden.

Programmet filnivåkryptering bör också användas för dataåtkomst. Ibland kan du minimera data datasuveränitet frågor genom att kryptera data med en nyckel som lagras i ett land. Detta förhindrar att även oavsiktlig dataöverföring orsakar problem Eftersom det är omöjligt att dekryptera data utan nyckeln, förutsatt att en stark algoritmen används (till exempel AES-256).

Du kan använda ytterligare säkerhetsåtgärder för att skydda databasen, till exempel utforma en säker system, kryptera känsliga tillgångar och skapa en brandvägg runt database-servrar.

## <a name="next-steps"></a>Nästa steg
Den här artikeln har du introducerats till en samling med SQL Database och SQL Data Warehouse rekommenderade säkerhetsmetoder för att skydda din PaaS-webbprogram och mobilappar. Mer information om hur du skyddar dina PaaS-distributioner finns:

- [Skydda PaaS-distributioner](security-paas-deployments.md)
- [Skydda PaaS-webb- och mobilprogram med Azure App Services](security-paas-applications-using-app-services.md)
