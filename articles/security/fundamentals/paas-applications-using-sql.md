---
title: Skydda PaaS-databaser i Azure | Microsoft-dokument
description: 'Lär dig mer om bästa metoder för Azure SQL Database och SQL Data Warehouse för att skydda dina PaaS-webb- och mobilappar. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: c73f585e3102618cea378716491f9354810a6db8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124998"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Metodtips för att skydda PaaS-databaser i Azure

I den här artikeln diskuterar vi en samling av [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) och SQL Data [Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) säkerhetspraxis för att skydda din plattform-som-en-tjänst (PaaS) webb och mobila program. Dessa metodtips härleds från vår erfarenhet av Azure och erfarenheter från kunder som du själv.

Azure SQL Database och SQL Data Warehouse tillhandahåller en relationsdatabastjänst för dina internetbaserade program. Låt oss titta på tjänster som hjälper till att skydda dina program och data när du använder Azure SQL Database och SQL Data Warehouse i en PaaS-distribution:

- Azure Active Directory-autentisering (i stället för SQL Server-autentisering)
- Azure SQL-brandvägg
- Transparent datakryptering (TDE)

## <a name="use-a-centralized-identity-repository"></a>Använda en centraliserad identitetsdatabas
Azure SQL-databaser kan konfigureras för att använda en av två typer av autentisering:

- **SQL-autentisering** använder ett användarnamn och lösenord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med hjälp av dessa autentiseringsuppgifter kan du autentisera till valfri databas på den servern som databasägare.

- **Azure Active Directory-autentisering** använder identiteter som hanteras av Azure Active Directory och stöds för hanterade och integrerade domäner. Om du vill använda Azure Active Directory-autentisering måste du skapa en annan serveradministratör som kallas "Azure AD-administratör", som tillåts administrera Azure AD-användare och -grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan.

[Azure Active Directory-autentisering](../../active-directory/develop/authentication-scenarios.md) är en mekanism för anslutning till Azure SQL Database och SQL Data Warehouse med hjälp av identiteter i Azure Active Directory (AD). Azure AD är ett alternativ till SQL Server-autentisering så att du kan stoppa spridningen av användaridentiteter över databasservrar. Med Azure AD-autentisering kan du centralt hantera identiteterna för databasanvändare och andra Microsoft-tjänster på en central plats. Central ID-hantering ger en enda plats för hantering av databasanvändare och förenklar behörighetshanteringen.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Fördelar med att använda Azure AD i stället för SQL-autentisering
- Tillåter lösenordsrotation på ett enda ställe.
- Hanterar databasbehörigheter med externa Azure AD-grupper.
- Eliminerar lagring av lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure AD.
- Använder innehållna databasanvändare för att autentisera identiteter på databasnivå.
- Stöder tokenbaserad autentisering för program som ansluter till SQL Database.
- Stöder domänfederation med Active Directory Federation Services (ADFS) eller inbyggd användar-/lösenordsautentisering för en lokal Azure AD utan domänsynkronisering.
- Stöder anslutningar från SQL Server Management Studio som använder Active Directory Universal Authentication, som innehåller [MFA (Multi-Factor Authentication).](/azure/active-directory/authentication/multi-factor-authentication) I MFA används stark autentisering via en rad enkla verifieringsalternativ – telefonsamtal, SMS, smarta kort med PIN-kod eller avisering i mobilappen. Mer information finns i [Universell autentisering med SQL Database och SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

Mer information om Azure AD-autentisering finns i:

- [Använda Azure Active Directory-autentisering för autentisering med SQL Database, Hanterad instans eller SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md)
- [Autentisera till Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Tokenbaserat autentiseringsstöd för Azure SQL DB med Azure AD-autentisering](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> För att säkerställa att Azure Active Directory passar din miljö, se [Azure AD-funktioner och begränsningar](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Begränsa åtkomsten baserat på IP-adress
Du kan skapa brandväggsregler som anger intervall för godtagbara IP-adresser. Dessa regler kan riktas mot både server- och databasnivå. Vi rekommenderar att du använder brandväggsregler på databasnivå när det är möjligt för att öka säkerheten och göra databasen mer portabel. Brandväggsregler på servernivå används bäst för administratörer och när du har många databaser som har samma åtkomstkrav men du vill inte lägga tid på att konfigurera varje databas individuellt.

Standardbegränsningar för SQL-databas-käll-IP-adress tillåter åtkomst från valfri Azure-adress, inklusive andra prenumerationer och klienter. Du kan begränsa detta så att du bara tillåter att ip-adresser kommer åt instansen. Även med din SQL-brandvägg och IP-adressbegränsningar krävs fortfarande stark autentisering. Se rekommendationerna tidigare i den här artikeln.

Mer information om Azure SQL-brandvägg och IP-begränsningar finns i:

- [Azure SQL Database och SQL Data Warehouse-åtkomstkontroll](../../sql-database/sql-database-manage-logins.md)
- [Brandväggsregler för SQL Database och SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Kryptera data i vila
[Transparent datakryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) är aktiverat som standard. TDE krypterar sql server, Azure SQL-databas och Azure SQL Data Warehouse-data och loggfiler. TDE skyddar mot en kompromiss av direkt åtkomst till filerna eller deras säkerhetskopiering. På så sätt kan du kryptera data i vila utan att ändra befintliga program. TDE ska alltid vara aktiverat. Detta kommer dock inte att stoppa en angripare med hjälp av den normala åtkomstsökvägen. TDE ger möjlighet att följa många lagar, förordningar och riktlinjer som fastställts i olika branscher.

Azure SQL hanterar viktiga relaterade problem för TDE. Precis som med TDE måste särskild försiktighet vidtas för att säkerställa återvinningsbarhet och när databaser flyttas. I mer sofistikerade scenarier kan nycklarna uttryckligen hanteras i Azure Key Vault via utökningsbar nyckelhantering. Se [Aktivera TDE på SQL Server med EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Detta gör det också möjligt att ta med din egen nyckel (BYOK) via Azure Key Vaults BYOK-funktionen.

Azure SQL tillhandahåller kryptering för kolumner via [Alltid krypterad](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Detta ger endast behöriga program åtkomst till känsliga kolumner. Om du använder den här typen av kryptering begränsas SQL-frågor för krypterade kolumner till likhetsbaserade värden.

Kryptering på programnivå bör också användas för selektiva data. Datasuveränitetsproblem kan ibland minskas genom att kryptera data med en nyckel som finns i rätt land/region. Detta förhindrar även oavsiktlig dataöverföring från att orsaka ett problem eftersom det är omöjligt att dekryptera data utan nyckeln, förutsatt att en stark algoritm används (till exempel AES 256).

Du kan använda ytterligare försiktighetsåtgärder för att skydda databasen, till exempel utforma ett säkert system, kryptera konfidentiella resurser och bygga en brandvägg runt databasservrarna.

## <a name="next-steps"></a>Nästa steg
I den här artikeln introducerades du en samling av bästa metoder för SQL Database- och SQL Data Warehouse-säkerhet för att skydda dina PaaS-webb- och mobilappar. Mer information om hur du skyddar dina PaaS-distributioner finns i:

- [Skydda PaaS-distributioner](paas-deployments.md)
- [Skydda PaaS-webb- och mobilappar med Azure App Services](paas-applications-using-app-services.md)
