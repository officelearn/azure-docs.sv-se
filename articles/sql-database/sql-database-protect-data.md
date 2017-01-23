---
title: "Översikt över dataskydd i Azure SQL Database | Microsoft Docs"
description: "Lär dig mer om hur du skyddar data i Azure SQL Database."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 12/21/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f4712d70c0323e607ddcc021809f8097a621730d
ms.openlocfilehash: 67b32b224d86e8d0ed06d8248c3a2c5f5569c5cb


---
# <a name="protecting-data-within-your-sql-database"></a>Skydda data inom din SQL Database

SQL Database skyddar dina data med hjälp av kryptering.  

## <a name="overview"></a>Översikt

SQL Database skyddar dina data genom att tillhandahålla kryptering för data i rörelse med [Transport Layer Security](https://support.microsoft.com/en-us/kb/3135244), för vilande data med [transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242) och för data under användning med [alltid krypterad](https://msdn.microsoft.com/library/mt163865.aspx). En beskrivning av hur man använder de här dataskyddsfunktionerna i SQL Database finns i [Dataskydd och säkerhet](sql-database-protect-data.md).

För andra sätt att kryptera dina data, kan du överväga:

* [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
* Om du behöver en Hardware Security Module eller central hantering av krypteringsnyckelns hierarki, bör du använda [Azure Key Vault med SQL Server i en virtuell Azure-dator](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).


SQL Database skyddar dina data genom att tillhandahålla funktioner för granskning och hotidentifiering. 

### <a name="auditing"></a>Granskning
SQL Database-granskning spårar databasaktiviteter och hjälper dig att upprätthålla regelefterlevnad, genom att registrera databashändelser till en granskningslogg på ditt Azure Storage-konto. Granskning låter dig förstå pågående databasaktiviteter, samt analysera och undersöka historiska aktiviteter för att identifiera potentiella hot eller misstänkt missbruk och säkerhetsöverträdelser. Ytterligare information finns i [Kom igång med SQL Database-granskning](sql-database-auditing-get-started.md).  

### <a name="threat-detection"></a>Hotidentifiering
Hotidentifiering komplimenterar granskning genom att erbjuda ytterligare ett lager säkerhetsinformation inbyggt i Azure SQL Database-tjänsten. Den fungerar dygnet runt för att lära, profilera och identifiera avvikande databasaktiviteter. Du aviseras om misstänkta aktiviteter, potentiella svagheter, SQL-filinmatningsattacker och avvikande mönster i databasåtkomst. Du kan svara på aviseringarna genom att följa den handlingsbara och informativa instruktionerna som du får. Mer information finns i [Kom igång med SQL Database Threat Detection](sql-database-threat-detection-get-started.md).  

## <a name="connection-security"></a>Anslutningssäkerhet
Anslutningssäkerhet avser hur du begränsar och säkrar anslutningar till databasen med hjälp av brandväggsregler och krypterad anslutning.

Brandväggsregler används av både servern och databasen för att avvisa anslutningsförsök från IP-adresser som inte uttryckligen finns på listan över godkända. Om du vill tillåta att programmets eller klientdatorns offentlig IP-adress försöker ansluta till en ny databas, måste du först skapa en brandväggsregel på servernivå med den klassiska Azure-portalen, REST API eller PowerShell. Ett bra tips är att du begränsar de IP-adressintervall som tillåts via serverbrandväggen så mycket som möjligt. Mer information finns i [Azure SQL Database-brandvägg](https://msdn.microsoft.com/library/ee621782).

Alla anslutningar till Azure SQL Database kräver filkryptering (SSL/TLS) hela tiden medan data är "under överföringen" till och från databasen. I anslutningssträngen för ditt program måste du ange parametrar för att kryptera anslutningen och *inte* lita på servercertifikatet (detta görs för dig om du kopierar anslutningssträngen utanför den klassiska Azure-portalen), annars kan inte anslutningen verifiera serverns identitet och är sårbar för "man-in-the-middle"-attacker. För t.ex. ADO.NET-drivrutinen är dessa parametrar för anslutningssträngen **Encrypt=True** och **TrustServerCertificate=False**. Mer information finns i [Anslutningskryptering för Azure SQL Database och certifikatverifiering](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## <a name="authentication"></a>Autentisering
Autentisering refererar till hur du styrkt din identitet vid anslutning till databasen. SQL Database stöder två typer av autentisering:

* **SQL-autentisering**, som använder ett användarnamn och lösenord
* **Azure Active Directory-autentisering**, som använder identiteter som hanteras av Azure Active Directory och har stöd för hanterade och integrerade domäner

När du skapade den logiska servern för databasen angav du en "serveradministratörsinloggning” med ett användarnamn och lösenord. Med dessa autentiseringsuppgifter kan du autentisera till en databas på servern som databasens ägare eller "dbo." Om du vill använda Azure Active Directory-autentisering måste du skapa en annan serveradministratör som kallas "Azure AD-admin," som tillåts administrera Azure AD-användare och -grupper. Den här administratören kan också utföra alla åtgärder som en vanlig serveradministratören kan. Se [Ansluta till SQL Database genom att använda Azure Active Directory-autentisering](sql-database-aad-authentication.md) för en genomgång av hur du skapar en Azure AD-administratör för att aktivera Azure Active Directory-autentisering.

Ett bra tips är att ditt program bör använda ett annat konto för autentisering – på det här sättet kan du begränsa behörigheterna för programmet och minska riskerna för skadlig programvara om din programkod är sårbar för ett SQL-angrepp. Den rekommenderade metoden är att skapa en [innesluten databasanvändare](https://msdn.microsoft.com/library/ff929188), vilket gör att din app kan autentisera direkt till databasen. Du kan skapa en innesluten databasanvändare som använder SQL-autentisering genom att köra följande T-SQL-kommando när du är ansluten till databasen som serveradministratör:

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Om du har skapat en Azure AD-administratör kan du skapa en innesluten databasanvändare som använder Azure Active Directory-autentisering genom att köra följande T-SQL-kommando när du är ansluten till databasen som Azure AD-administratör:

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

I båda fallen bör anslutningssträngen för ditt program ange autentiseringsuppgifterna för användaren, i stället för serveradministratörens, för att ansluta till databasen.

Mer information om autentisering till en SQL Database finns i [Hantera databaser och -inloggningsnamn i Azure SQL Database](sql-database-manage-logins.md).

## <a name="authorization"></a>Auktorisering
Auktorisering hänvisar till vad du kan göra i en Azure SQL Database och detta styrs av ditt användarkontos rollmedlemskap och behörigheter. Ett bra tips är att du ska ge användare så få behörigheter som möjligt. Azure SQL Database gör det lättare att hantera roller i T-SQL:

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Serveradministratörskontot som du ansluter med är medlem i db_owner som har behörighet att göra vad som helst i databasen. Spara det här kontot för att distribuera schemauppgraderingar och andra hanteringsåtgärder. Använd kontot "ApplicationUser" med mer begränsade behörigheter för att ansluta från ditt program till databasen med den minsta behörigheten som krävs av programmet.

Det finns olika sätt att ytterligare begränsa vad en användare kan göra med Azure SQL Database:

* Andra [Databasroller](https://msdn.microsoft.com/library/ms189121) än db_datareader och db_datawriter kan användas för att skapa mer kraftfulla programanvändarkonton eller mindre kraftfulla hanteringskonton.
* Detaljerade [behörigheter](https://msdn.microsoft.com/library/ms191291) gör att du styra vilka åtgärder du kan göra på enskilda kolumner, tabeller, vyer, procedurer och andra objekt i databasen.
* [Personifiering](https://msdn.microsoft.com/library/vstudio/bb669087) och [modulsignering](https://msdn.microsoft.com/library/bb669102) kan användas för att säkert höja behörigheter tillfälligt.
* [Säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131) kan användas som en begränsning av vilka rader en användare kan komma åt.
* [Datamaskning](sql-database-dynamic-data-masking-get-started.md) kan användas för att begränsa exponering av känsliga data.
* [Lagrade procedurer](https://msdn.microsoft.com/library/ms190782) kan användas för att begränsa de åtgärder som kan utföras i databasen.

Att hantera databaser och logiska servrar från den klassiska Azure-portalen eller via Azure Resource Manager API styrs av portalanvändarkontots rolltilldelningar. Mer information om det här avsnittet finns i [Rollbaserad åtkomstkontroll i Azure Portal](../active-directory/role-based-access-control-configure.md).

## <a name="encryption"></a>Kryptering
Azure SQL Database kan skydda dina data genom att kryptera data när de är "i vila" eller lagras i databasfiler och säkerhetskopior, med hjälp av [Transparent datakryptering](http://go.microsoft.com/fwlink/?LinkId=526242). För att kryptera din databas, ansluter du som databasägare och kör:

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Andra sätt att kryptera datahemligheter är:

* [Kryptering på cellnivå](https://msdn.microsoft.com/library/ms179331.aspx) för att kryptera vissa kolumner eller även celler av data med olika krypteringsnycklar.
* Om du behöver en Hardware Security Module eller central hantering av krypteringsnyckelns hierarki, bör du använda [Azure Key Vault med SQL Server i en virtuell Azure-dator](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Alltid krypterad](https://msdn.microsoft.com/library/mt163865.aspx) (i förhandsversion) gör kryptering transparent för program och tillåter klienter att kryptera känsliga data i klientprogram utan att dela krypteringsnycklarna med SQL Database.

## <a name="auditing"></a>Granskning
Gransknings- och spåra databashändelser kan hjälpa dig att upprätthålla regelefterlevnad och identifiera misstänkt aktivitet. SQL Database Auditing gör att du kan registrera händelser i databasen till en granskningslogg i ditt Azure Storage-konto. SQL Database Auditing är integrerat med Microsoft Power BI för att underlätta ingående rapporter och analyser. Mer information finns i [Kom igång med SQL Database Auditing](sql-database-auditing-get-started.md).

SQL Database Threat Detection (hotidentifiering) ger ett extra lager av säkerhet ovanpå Auditing. Detta gör att du ska svara på hot när de inträffar genom att tillhandahålla säkerhetsaviseringar på avvikande aktiviteter. Mer information finns i [Kom igång med SQL Database Threat Detection](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Efterlevnad
Förutom ovanstående egenskaper och funktioner som kan hjälpa programmet att uppfylla olika säkerhetskrav, deltar Azure SQL Database också i regelbundna granskningar och har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i [Microsoft Azure säkerhetscenter](https://azure.microsoft.com/support/trust-center/), där du hittar den senaste listan med [SQL Database-kompatibilitetscertifieringar](https://azure.microsoft.com/support/trust-center/services/).

## <a name="next-steps"></a>Nästa steg

- En översikt över alla säkerhetsfunktioner i SQL Database finns i [SQL Säkerhetsöversikt](sql-database-security-overview.md).
- En beskrivning av åtkomstkontrollfunktionerna i SQL Database finns i [Kontrollera åtkomst](sql-database-control-access.md).
- För en diskussion om proaktiv övervakning, se [Kom igång med SQL Database-granskning](sql-database-auditing-get-started.md) och [Kom igång med SQL Database-hotidentifiering](sql-database-threat-detection-get-started.md).




<!--HONumber=Dec16_HO4-->


