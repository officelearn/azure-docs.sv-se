---
title: Skydda PaaS databaser i Azure | Microsoft Docs
description: " Läs mer om Azure SQL Database och SQL Data Warehouse Säkerhet Metodtips för att skydda din PaaS webb- och mobilprogram. "
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
ms.openlocfilehash: 3e7dc4dfba001228a4d11e2b21cdeed8e7af45ac
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="securing-paas-databases-in-azure"></a>Att säkra PaaS-databaser i Azure

I den här artikeln tar vi upp en samling [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) och [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/) säkerhetsmetoder för att skydda din PaaS webb- och mobilprogram. Följande rekommendationer härleds från våra erfarenhet av Azure och upplevelser för kunder som själv.

## <a name="azure-sql-database-and-sql-data-warehouse"></a>Azure SQL Database och SQL Data Warehouse
[Azure SQL Database](../sql-database/sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) betjäna relationsdatabas för din Internet-baserade program. Nu ska vi titta på tjänster som hjälper dig skydda dina program och data när du använder Azure SQL Database och SQL Data Warehouse i en PaaS-distribution:

- Azure Active Directory-autentisering (i stället för SQL Server-autentisering)
- Azure SQL-brandväggen
- Transparent datakryptering (TDE)

## <a name="best-practices"></a>Bästa praxis

### <a name="use-a-centralized-identity-repository-for-authentication-and-authorization"></a>Använda en centraliserad identitetsdatabas för autentisering och auktorisering

Azure SQL-databaser kan konfigureras för att använda en av två typer av autentisering:

- **SQL-autentisering** använder ett användarnamn och lösenord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter, kan du autentisera till en databas på servern som databasägaren.

- **Azure Active Directory-autentisering** använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerad domäner. Om du vill använda Azure Active Directory-autentisering måste du skapa en annan serveradministratör kallas den ”Azure AD admin”, som tillåts administrera Azure AD-användare och grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan.

[Azure Active Directory-autentisering](../active-directory/develop/active-directory-authentication-scenarios.md) är en mekanism för anslutning till Azure SQL Database och SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD). Azure AD innehåller ett alternativ till SQL Server-autentisering så du kan stoppa spridning av användaridentiteter över databasservrar. Azure AD-autentisering kan du centralt hantera identiteter för databasanvändare och andra Microsoft-tjänster på en central plats. Central hantering av ID innehåller en enda plats för att hantera användare och förenklar hantering av behörighet.  

Fördelarna med att använda Azure AD-autentisering i stället för SQL-autentisering är:

- Tillåter lösenord rotation i en enda plats.
- Hanterar databasen med hjälp av externa Azure AD-grupper.
- Eliminerar lagra lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure AD.
- Använder finns databasanvändare att autentisera identiteter på databasnivå.
- Stöder tokenbaserad autentisering för program som ansluter till SQL-databas.
- Har stöd för ADFS (domänfederation) eller intern användarlösenord autentisering för en lokal Azure AD utan domänsynkronisering.
- Stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal-autentisering, vilket innefattar [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). MFA ingår stark autentisering med ett antal alternativ för enkel verifiering – telefonsamtal, textmeddelande, smartkort och PIN-kod eller meddelande i mobilappen. Mer information finns i [SSMS stöd för Azure AD MFA med SQL Database och SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Mer information om Azure AD-autentisering finns:

- [Ansluter till SQL Database eller SQL Data Warehouse med hjälp av Azure Active Directory-autentisering](../sql-database/sql-database-aad-authentication.md)
- [Autentisera till Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Stöd för tokenbaserad autentisering för Azure SQL-databas med hjälp av Azure AD-autentisering](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)

> [!NOTE]
> För att säkerställa att Azure Active Directory passar bra för din miljö, se [Azure AD-funktioner och begränsningar](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), särskilt ytterligare överväganden.
>
>

### <a name="restrict-access-based-on-ip-address"></a>Begränsa åtkomst baserat på IP-adress
Du kan skapa brandväggsregler som anger godkända IP-adressintervall. Dessa regler kan tillämpas på både server och databas. Vi rekommenderar att du använder databasnivå brandväggsregler när det är möjligt att förbättra säkerheten och göra databasen mer bärbara. Brandväggsregler på servernivå är bäst för administratörer och när du har många databaser som har samma krav för åtkomst, men du inte vill ägna tid konfigurerar varje databas individuellt.

SQL-databas standard källa IP-adressbegränsningar tillåta åtkomst från alla Azure-adresser (inklusive andra prenumerationer och klienter). Du kan begränsa detta så att bara IP-adresser att komma åt instansen. Även om din SQL-brandvägg och IP-adressbegränsningar behövs stark autentisering fortfarande. Se rekommendationer tidigare i den här artikeln.

Mer information om Azure SQL-brandvägg och IP-begränsningar finns:

- [Azure SQL Database-åtkomstkontroll](../sql-database/sql-database-control-access.md)
- [Konfigurera brandväggsregler för Azure SQL Database - översikt](../sql-database/sql-database-firewall-configure.md)
- [Konfigurera en Azure SQL Database servernivå brandväggsregel med Azure-portalen](../sql-database/sql-database-configure-firewall-settings.md)

### <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data
[Transparent Data kryptering (TDE)](https://msdn.microsoft.com/library/azure/bb934049) är aktiverad som standard. TDE krypterar transparent SQL Server, Azure SQL Database och Azure SQL Data Warehouse data och loggfilen filer. TDE skyddar mot en kompromettering av direkt åtkomst till filer eller deras säkerhetskopiering. På så sätt kan du kryptera vilande data utan att ändra befintliga program. TDE ska alltid vara aktiverad; Detta kommer dock inte att stoppa en angripare som använder normal åtkomstsökvägen. TDE ger möjlighet att följa många lagar, och riktlinjerna i olika branscher.

Azure SQL hanterar viktiga problem för TDE. Som med TDE, lokalt måste vara extra försiktig så återställning och när du flyttar databaser. I mer avancerade scenarier, nycklarna kan uttryckligen hanteras i Azure Key Vault via extensible key management (se [aktivera TDE på SQL Server med hjälp av EKM](/security/encryption/enable-tde-on-sql-server-using-ekm)). Detta kan också för ta med din egen nyckel (BYOK) via Azure nyckeln valv BYOK kapaciteten.

Azure SQL ger dig kryptering för kolumnerna till [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). På så sätt kan endast auktoriserade program åtkomst till känsliga kolumner. Med den här typen av kryptering begränsar SQL-frågor för krypterade kolumner till baserat på likheten värden.

Programmet kryptering bör också användas för dataåtkomst. Ibland kan du minimera data suveränitet problem genom att kryptera data med en nyckel som används i ett land. Detta förhindrar att även oavsiktliga dataöverföring orsakar problem Eftersom det är omöjligt att dekryptera data utan nyckel, förutsatt att en stark algoritm används (till exempel AES 256).

Du kan använda ytterligare säkerhetsåtgärder för att skydda databasen som en säker system utformas, kryptera konfidentiell tillgångar och skapa en brandvägg runt databasservrar.

## <a name="next-steps"></a>Nästa steg
Den här artikeln introduceras du till en samling med SQL Database och SQL Data Warehouse säkerhetsmetoder för att skydda din PaaS webb- och mobilprogram. Mer information om hur du skyddar dina PaaS-distributioner finns:

- [Skydda PaaS-distributioner](security-paas-deployments.md)
- [Att säkra PaaS-webb- och mobila program med Azure App Service](security-paas-applications-using-app-services.md)
