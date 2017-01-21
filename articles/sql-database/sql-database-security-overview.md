---
title: "Säkerhetsöversikt för Azure SQL Database | Microsoft Docs"
description: "Lär dig mer om Azure SQL Database och SQL Server-säkerhet, inklusive skillnaderna mellan molnet och lokal SQL Server när det gäller autentisering, auktorisering, anslutningssäkerhet, kryptering och efterlevnad."
services: sql-database
documentationcenter: 
author: tmullaney
manager: jhubbard
editor: 
ms.assetid: a012bb85-7fb4-4fde-a2fc-cf426c0a56bb
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 06/09/2016
ms.author: thmullan;jackr
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 9ebf3cd290151449c05c89307500fa5679de3b09


---
# <a name="securing-your-sql-database"></a>Säkra din SQL Database

Den här artikeln går igen grunderna för att skydda datanivån hos ett program med Azure SQL Database. Den här artikeln i synnerhet, hjälper dig att komma igång med resurser för att skydda data, kontrollera åtkomst och proaktiv övervakning. Följande diagram visar de säkerhetslager som SQL Database tillhandahåller.

![Säkerhet och efterlevnad i SQL](./media/sql-database-security-overview/diagram.png)

En fullständig översikt över tillgängliga säkerhetsfunktioner på alla stilar av SQL finns i [Security Center för SQL Server Database Engine och Azure SQL Database](https://msdn.microsoft.com/library/bb510589). Mer information finns också i [Tekniskt faktablad om säkerhet och Azure SQL Database](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="protect-data"></a>Skydda data
SQL Database skyddar dina data genom att tillhandahålla kryptering för data i rörelse med [Transport Layer Security](https://support.microsoft.com/en-us/kb/3135244), för vilande data med [transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242) och för data under användning med [alltid krypterad](https://msdn.microsoft.com/library/mt163865.aspx). En beskrivning av hur man använder de här dataskyddsfunktionerna i SQL Database finns i [Dataskydd och säkerhet](sql-database-protect-data.md).

> [!IMPORTANT]
>Alla anslutningar till Azure SQL Database kräver filkryptering (SSL/TLS) hela tiden medan data är "under överföringen" till och från databasen. I anslutningssträngen för ditt program, måste du ange parametrar för att kryptera anslutningen och *inte* lita på servercertifikatet (detta görs för dig om du kopierar anslutningssträngen utanför den klassiska Azure-portalen), annars kommer inte anslutningen att verifiera serverns identitet och kommer vara sårbar för "man-in-the-middle"-attacker. För t.ex. ADO.NET-drivrutinen är dessa parametrar för anslutningssträngen **Encrypt=True** och **TrustServerCertificate=False**. 

För andra sätt att kryptera dina data, kan du överväga:

* [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
* Om du behöver en Hardware Security Module eller central hantering av krypteringsnyckelns hierarki, bör du använda [Azure Key Vault med SQL Server i en virtuell Azure-dator](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).

## <a name="control-access"></a>Styr åtkomst
SQL Database skyddar dina data genom att begränsa åtkomsten till databasen med hjälp av brandväggsregler, autentiseringsmekanismer som kräver att användare bevisar sin identitet och auktorisering till data via rollbaserade medlemskap och behörigheter, såväl som säkerhet på radnivå och dynamisk datamaskering. En beskrivning av åtkomstkontrollfunktionerna i SQL Database finns i [Kontrollera åtkomst](sql-database-control-access.md).

> [!IMPORTANT]
> Hantering av databaser och logiska servrar inom Azure kontrolleras av din portalanvändarkontos rolltilldelningar. Mer information om det här avsnittet finns i [Rollbaserad åtkomstkontroll i Azure Portal](../active-directory/role-based-access-control-configure.md).
>

### <a name="firewall-and-firewall-rules"></a>Brandvägg och brandväggsregler
För att hjälpa skydda dina data, förhindrar brandväggar all åtkomst till din databasserver tills du anger vilka datorer som har behörighet med hjälp av [brandväggsregler](sql-database-firewall-configure.md). Brandväggen ger åtkomst till databaser baserat på vilken IP-adress som varje begäran kommer från.

### <a name="authentication"></a>Autentisering
SQL-databasautentisering refererar till hur du styrker din identitet när du ansluter till databasen. SQL Database stöder två typer av autentisering:

* **SQL-autentisering**, som använder ett användarnamn och lösenord. När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan du autentisera till en databas på servern som databasens ägare eller "dbo." 
* **Azure Active Directory-autentisering**, som använder identiteter som hanteras av Azure Active Directory och stöder hanterade och integrerade domäner. Använd Active Directory-autentisering (integrerad säkerhet) [närhelst det går](https://msdn.microsoft.com/library/ms144284.aspx). Om du vill använda Azure Active Directory-autentisering måste du skapa en annan serveradministratör som kallas "Azure AD-admin," som tillåts administrera Azure AD-användare och -grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan. Se [Ansluta till SQL Database genom att använda Azure Active Directory-autentisering](sql-database-aad-authentication.md) för en genomgång av hur du skapar en Azure AD-administratör för att aktivera Azure Active Directory-autentisering.

### <a name="authorization"></a>Auktorisering
Auktorisering hänvisar till vad en användare kan göra inom en Azure SQL-databas och det styrs av ditt användarkontos databas rollmedlemskap och behörigheter på objektnivå. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

### <a name="row-level-security"></a>Säkerhet på radnivå
Säkerhet på radnivå ger kunder möjlighet att styra åtkomsten till rader i en databastabell baserat på egenskaperna för användaren som kör en fråga (t.ex. grupmedlemskap eller körningskontext). Mer information finns i [Säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131).

### <a name="data-masking"></a>Datamaskning 
SQL Database dynamisk datamaskning begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. Dynamisk datamaskning identifierar automatiskt potentiellt känsliga data i Azure SQL Database och ger handlingsbara rekommendationer att maskera fälten, med minimal inverkan på programlagret. Det fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad. Mer information finns i [Kom igång med SQL Database dynamisk datamaskning](sql-database-dynamic-data-masking-get-started.md) kan användas för att begränsa exponering av känsliga data.

## <a name="proactive-monitoring"></a>Proaktiv övervakning
SQL Database skyddar dina data genom att tillhandahålla funktioner för granskning och hotidentifiering. 

### <a name="auditing"></a>Granskning
SQL Database-granskning spårar databasaktiviteter och hjälper dig att upprätthålla regelefterlevnad, genom att registrera databashändelser till en granskningslogg på ditt Azure Storage-konto. Granskning låter dig förstå pågående databasaktiviteter, samt analysera och undersöka historiska aktiviteter för att identifiera potentiella hot eller misstänkt missbruk och säkerhetsöverträdelser. Ytterligare information finns i [Kom igång med SQL Database-granskning](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Hotidentifiering
Hotidentifiering komplimenterar granskning genom att erbjuda ytterligare ett lager säkerhetsinformation inbyggt i Azure SQL Database-tjänsten. Den fungerar dygnet runt för att lära sig, undersöka och identifiera avvikande databasaktiviteter. Du får aviseringar om misstänkta aktiviteter, potentiella svagheter, SQL-filinmatningsattacker och avvikande mönster i databasåtkomst. Du kan svara på aviseringarna genom att följa den handlingsbara och informativa instruktionerna som du får. Mer information finns i [Kom igång med SQL Database Threat Detection](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Efterlevnad
Förutom ovanstående egenskaper och funktioner som kan hjälpa programmet att uppfylla olika säkerhetskrav, deltar Azure SQL Database också i regelbundna granskningar och har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i [Microsoft Azure säkerhetscenter](https://azure.microsoft.com/support/trust-center/), där du hittar den senaste listan med [SQL Database-kompatibilitetscertifieringar](https://azure.microsoft.com/support/trust-center/services/).

## <a name="next-steps"></a>Nästa steg

- En beskrivning av användningen av funktioner för dataskydd i SQL Database finns i [Dataskydd och säkerhet](sql-database-protect-data.md).
- En beskrivning av åtkomstkontrollfunktionerna i SQL Database finns i [Kontrollera åtkomst](sql-database-control-access.md).
- För en diskussion om proaktiv övervakning, se [Kom igång med SQL Database-granskning](sql-database-auditing-get-started.md) och [Kom igång med SQL Database-hotidentifiering](sql-database-threat-detection-get-started.md).


<!--HONumber=Dec16_HO4-->


