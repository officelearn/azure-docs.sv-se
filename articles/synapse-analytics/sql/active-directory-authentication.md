---
title: Azure Active Directory
description: Lär dig mer om hur du använder Azure Active Directory för autentisering med SQL Database, Hanterad instans och Synaps SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 5808f892f189bd6cb2cc39bd157be1d61c966763
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424757"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Använda Azure Active Directory-autentisering för autentisering med Synapse SQL

Azure Active Directory-autentisering är en mekanism för anslutning till [Azure Synapse Analytics](../overview-faq.md) med hjälp av identiteter i Azure Active Directory (Azure AD).

Med Azure AD-autentisering kan du centralt hantera identiteterna för användare som har åtkomst till Azure Synapse för att förenkla behörighetshantering. Här är några av fördelarna:

- Det ger ett alternativ till vanlig användarnamn och lösenord autentisering.
- Hjälper till att stoppa spridningen av användaridentiteter över databasservrar.
- Tillåter lösenordsrotation på ett enda ställe.
- Kunder kan hantera behörigheter med externa (Azure AD) grupper.
- Det kan eliminera lagring av lösenord genom att aktivera integrerad Windows-autentisering och andra former av autentisering som stöds av Azure Active Directory.
- Azure AD stöder tokenbaserad autentisering för program som ansluter till Azure Synapse.
- Azure AD-autentisering stöder ADFS (domänfederation) eller inbyggd användar-/lösenordsautentisering för en lokal Azure Active Directory utan domänsynkronisering.
- Azure AD har stöd för anslutning från SQL Server Management Studio som använder Active Directory Universal Authentication, inklusive Multi-Factor Authentication (MFA).  I MFA används stark autentisering via en rad enkla verifieringsalternativ – telefonsamtal, SMS, smarta kort med PIN-kod eller avisering i mobilappen. Mer information finns i [SSMS-stöd för Azure AD MFA med Synapse SQL](mfa-authentication.md).
- Azure AD har stöd för liknande anslutningar från SSDT (SQL Server Data Tools) som använder Active Directory Interactive Authentication. Läs mer i [Stöd för Azure Active Directory i SSDT (SQL Server Data Tools)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Konfigurationsstegen innehåller följande procedurer för att konfigurera och använda Azure Active Directory-autentisering.

1. Skapa och fyll i Azure AD.
2. Skapa en Azure Active Directory-identitet
3. Tilldela roll till skapade Azure Active Directory-identitet i Synapse-arbetsytan (förhandsversion)
4. Anslut till Synapse Studio med hjälp av Azure AD-identiteter.

## <a name="aad-pass-through-in-azure-synapse-analytics"></a>AAD-genomgång i Azure Synapse Analytics

Med Azure Synapse Analytics kan du komma åt data i datasjön med din Azure Active Directory-identitet.

Genom att definiera åtkomsträttigheter för filer och data som respekteras i olika datamotorer kan du förenkla dina datasjölösningar genom att ha en enda plats där behörigheterna definieras i stället för att behöva definiera dem på flera ställen.

## <a name="trust-architecture"></a>Förtroendearkitektur

Följande högnivådiagram sammanfattar lösningsarkitekturen för att använda Azure AD-autentisering med Synapse SQL. För att stödja Azure AD-inbyggda användarlösenord beaktas endast Cloud-delen och Azure AD/Synapse Synapse SQL. För att stödja Federerad autentisering (eller användar/lösenord för Windows-autentiseringsuppgifter) krävs kommunikation med ADFS-block. Pilarna visar kommunikationsvägar.

![aad auth diagram][1]

Följande diagram visar federations-, förtroende- och värdrelationer som gör att en klient kan ansluta till en databas genom att skicka en token. Token autentiseras av en Azure AD och är betrodd av databasen. Kund 1 kan representera en Azure Active Directory med inbyggda användare eller en Azure AD med federerade användare. Kund 2 representerar en möjlig lösning inklusive importerade användare. i det här exemplet kommer från en federerad Azure Active Directory med ADFS synkroniseras med Azure Active Directory. Det är viktigt att förstå att åtkomst till en databas med Azure AD-autentisering kräver att värdprenumerationen är associerad med Azure AD. Samma prenumeration måste användas för att skapa SQL Server som är värd för Azure SQL Database eller SQL-poolen.

![prenumerationsrelation][2]

## <a name="administrator-structure"></a>Administratörsstruktur

När du använder Azure AD-autentisering finns det två administratörskonton för Synapse SQL. den ursprungliga SQL Server-administratören och Azure AD-administratören. Endast administratören baserat på ett Azure AD-konto kan skapa den första Azure AD-innehållna databasanvändaren i en användardatabas. Azure AD-administratörsinloggningen kan vara en Azure AD-användare eller en Azure AD-grupp. 

När administratören är ett gruppkonto kan det användas av valfri gruppmedlem, vilket aktiverar flera Azure AD-administratörer för Synapse SQL-instansen. Genom att använda gruppkontot som administratör kan du öka hanterbarheten genom att du kan lägga till och ta bort gruppmedlemmar centralt i Azure AD utan att ändra användare eller behörigheter i Synapse Analytics-arbetsytan. Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras när som helst.

![administratörsstruktur][3]

## <a name="permissions"></a>Behörigheter

Om du vill skapa nya `ALTER ANY USER` användare måste du ha behörighet i databasen. Behörigheten `ALTER ANY USER` kan beviljas alla databasanvändare. `ALTER ANY USER` Behörigheten lagras också av serveradministratörskontona och `CONTROL ON DATABASE` `ALTER ON DATABASE` databasanvändare med databasens eller `db_owner` behörighet och av medlemmar i databasrollen.

Om du vill skapa en innehållen databasanvändare i Synapse SQL måste du ansluta till databasen eller instansen med hjälp av en Azure AD-identitet. Om du vill skapa den första databasanvändaren måste du ansluta till databasen med hjälp av en Azure AD-administratör (som är ägare till databasen). All Azure AD-autentisering är endast möjlig om Azure AD-administratören skapades för Synapse SQL. Om Azure Active Directory-administratören har tagits bort från servern kan befintliga Azure Active Directory-användare som skapats tidigare i Synapse SQL inte längre ansluta till databasen med sina Azure Active Directory-autentiseringsuppgifter.

## <a name="azure-ad-features-and-limitations"></a>Azure AD-funktioner och begränsningar

- Följande medlemmar i Azure AD kan etableras i Synapse SQL:

  - Inbyggda medlemmar: En medlem som skapats i Azure AD i den hanterade domänen eller i en kunddomän. Mer information finns i [Lägga till ditt eget domännamn i Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
  - Federerade domänmedlemmar: En medlem som skapats i Azure AD med en federerad domän. Mer information finns i [Microsoft Azure stöder nu federation med Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/).
  - Importerade medlemmar från andra Azure AD:er som är inbyggda eller federerade domänmedlemmar.
  - Active Directory-grupper som skapats som säkerhetsgrupper.

- Azure AD-användare som ingår i `db_owner` en grupp som har serverroll kan inte använda syntaxen **[skapa databasscoped autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** mot Synapse SQL. Följande fel visas:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Bevilja `db_owner` rollen direkt till den enskilda Azure AD-användaren för att minska problemet **skapa databasscoped.**

- Dessa systemfunktioner returnerar NULL-värden när de körs under Azure AD-huvudnamn:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Ansluta med Azure AD-identiteter

Azure Active Directory-autentisering stöder följande metoder för att ansluta till en databas med Hjälp av Azure AD-identiteter:

- Azure Active Directory-lösenord
- Integrerad i Azure Active Directory
- Azure Active Directory Universal med MFA
- Använda autentisering av programtoken

Följande autentiseringsmetoder stöds för Azure AD-serverhuvudnamn (inloggningar) (**offentlig förhandsversion):**

- Azure Active Directory-lösenord
- Integrerad i Azure Active Directory
- Azure Active Directory Universal med MFA

### <a name="additional-considerations"></a>Annat som är bra att tänka på

- För att förbättra hanterbarheten rekommenderar vi att du etablerar en dedikerad Azure AD-grupp som administratör.
- Endast en Azure AD-administratör (en användare eller grupp) kan konfigureras för Synapse SQL-pool när som helst.
  - Tillägget av Azure AD-serverhuvudnamn (inloggningar) för SQL on-demand (preview) gör det möjligt att skapa flera Azure `sysadmin` AD-serverobjekt (inloggningar) som kan läggas till i rollen.
- Endast en Azure AD-administratör för Synapse SQL kan inledningsvis ansluta till Synapse SQL med ett Azure Active Directory-konto. Active Directory-administratören kan konfigurera efterföljande Azure AD-databasanvändare.
- Vi rekommenderar att du ställer in tidsgränsen för anslutningen till 30 sekunder.
- SQL Server 2016 Management Studio och SQL Server Data Tools för Visual Studio 2015 (version 14.0.60311.1April 2016 eller senare) stöder Azure Active Directory-autentisering. (Azure AD-autentisering stöds av **.NET Framework Data Provider för SqlServer**; åtminstone version .NET Framework 4.6). Därför de senaste versionerna av dessa verktyg och data-tier applikationer (DAC och . BACPAC) kan använda Azure AD-autentisering.
- Från och med version 15.0.1 stöder [sqlcmd-verktyget](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) och [bcp-verktyget](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Active Directory Interactive-autentisering med MFA.
- SQL Server Data Tools för Visual Studio 2015 kräver minst april 2016-versionen av dataverktygen (version 14.0.60311.1). Azure AD-användare visas inte i SSDT Object Explorer. Som en lösning visar du användarna i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- [Microsoft JDBC Driver 6.0 för SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) stöder Azure AD-autentisering. Se också [ange anslutningsegenskaper](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Nästa steg

En översikt över åtkomst och kontroll i Synapse SQL finns i [Synapse SQL-åtkomstkontroll](../sql/access-control.md). Mer information om databasens huvudnamn finns i [Huvudnamn](https://msdn.microsoft.com/library/ms181127.aspx). Ytterligare information om databasroller finns i artikeln [Databasroller.](https://msdn.microsoft.com/library/ms189121.aspx)
 

<!--Image references-->

[1]: ./media/aad-authentication/1-active-directory-authentication-diagram.png
[2]: ./media/aad-authentication/2-subscription-relationship.png
[3]: ./media/aad-authentication/3-admin-structure.png
[4]: ./media/aad-authentication/4-select-subscription.png
[5]: ./media/aad-authentication/5-active-directory-settings-portal.png
[6]: ./media/aad-authentication/6-edit-directory-select.png
[7]: ./media/aad-authentication/7-edit-directory-confirm.png
[8]: ./media/aad-authentication/8-choose-active-directory.png
[9]: ./media/aad-authentication/9-active-directory-settings.png
[10]: ./media/aad-authentication/10-choose-admin.png
[11]: ./media/aad-authentication/11-connect-using-integrated-authentication.png
[12]: ./media/aad-authentication/12-connect-using-password-authentication.png
[13]: ./media/aad-authentication/13-connect-to-db.png
