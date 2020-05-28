---
title: Använda Multi-Factor AAD-autentisering
description: Azure SQL Database, Azure SQL-hanterad instans och stöd anslutningar för Azure Synapse Analytics från SQL Server Management Studio (SSMS) med Active Directory Universal Authentication.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 04/23/2020
tags: azure-synapse
ms.openlocfilehash: 574999686c38ce4ce1e6d40b3148b70e3602fe00
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041891"
---
# <a name="using-multi-factor-aad-authentication"></a>Använda Multi-Factor AAD-autentisering
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure Managed instance och Azure Synapse Analytics stöder anslutningar från SQL Server Management Studio (SSMS) med *Azure Active Directory-Universal med MFA-* autentisering. I den här artikeln beskrivs skillnaderna mellan olika autentiseringsalternativ och de begränsningar som är associerade med att använda Universal Authentication.

**Hämta den senaste SSMS** – på klient datorn laddar du ned den senaste versionen av SSMS, från [nedladdnings SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

För alla funktioner som beskrivs i den här artikeln använder du minst juli 2017, version 17,2. Dialog rutan senaste anslutning bör se ut ungefär som på följande bild:

  ![1mfa – Universal-Connect](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png "Slutför rutan användar namn.")  

## <a name="the-five-authentication-options"></a>De fem alternativen för autentisering  

Active Directory Universal Authentication stöder de två icke-interaktiva autentiseringsmetoderna:
    - `Azure Active Directory - Password`anspråksautentisering
    - `Azure Active Directory - Integrated`anspråksautentisering

Det finns två icke-interaktiva autentiseringsscheman och kan användas i många olika program (ADO.NET, JDCB, ODC osv.). Dessa två metoder resulterar aldrig i popup-dialog rutor:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

Den interaktiva metoden som också stöder Azure Multi-Factor Authentication (MFA) är:`Active Directory - Universal with MFA`

Azures MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Den ger stark autentisering med en rad enkla verifierings alternativ (telefonsamtal, textmeddelande, smartkort med PIN-kod eller meddelande för mobilapp), så att användarna kan välja den metod de föredrar. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering.

En beskrivning av Multi-Factor Authentication finns i [Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).
Konfigurations steg finns i [konfigurera Azure SQL Database Multi-Factor Authentication för SQL Server Management Studio](authentication-mfa-ssms-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domän namn eller klient-ID-parameter

Från och med [SSMS version 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)kan användare som importeras till den aktuella Active Directory från andra Azure Active Directory som gäst användare ange Azure AD-domännamnet eller klient-ID när de ansluter. Gäst användare inkluderar användare som har bjudits in från andra Azure-annonser, Microsoft-konton som outlook.com, hotmail.com, live.com eller andra konton som gmail.com. Den här informationen tillåter **Active Directory universell med MFA-autentisering** för att identifiera rätt autentiserings-utfärdare. Det här alternativet krävs också för att stödja Microsoft-konton (MSA) som outlook.com, hotmail.com, live.com eller icke-MSA konton. Alla dessa användare som ska autentiseras med Universal Authentication måste ange sitt Azure AD-domännamn eller klient-ID. Den här parametern representerar det aktuella namnet på Azure AD-domänen eller klient-ID: t, så är Azure-servern länkad till. Om till exempel Azure Server är associerad med Azure AD-domän `contosotest.onmicrosoft.com` där användaren `joe@contosodev.onmicrosoft.com` är värd för en importerad användare från Azure AD `contosodev.onmicrosoft.com` -domän, är domän namnet som krävs för att autentisera användaren `contosotest.onmicrosoft.com` . När användaren är en inbyggd användare av Azure AD som är länkad till Azure Server och inte är ett MSA-konto krävs inget domän namn eller klient-ID. För att ange parametern (från och med SSMS version 17,2) i dialog rutan **Anslut till databas** , fyller du i dialog rutan, väljer **Active Directory-universell med MFA** -autentisering, klickar på **alternativ**, fyller i rutan **användar namn** och klickar sedan på fliken **anslutnings egenskaper** . kontrol lera **AD-domännamnet eller klient** -ID-rutan och tillhandahålla autentiserings utfärdare, till exempel domän namnet (**contosotest.onmicrosoft.com**) eller GUID för klient-ID.  
   ![MFA-Tenant-SSMS](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Om du kör SSMS 18. x eller senare behövs inte längre AD-domännamnet eller klient-ID: t för gäst användare eftersom 18. x eller senare identifierar det automatiskt.

   ![MFA-Tenant-SSMS](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Support för Azure AD Business till företag

Azure AD-användare som stöds för Azure AD B2B-scenarier som gäst användare (se [Vad är Azure B2B-samarbete](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kan ansluta till SQL Database och Azure Synapse endast som en del av medlemmarna i en grupp som skapats i den aktuella Azure AD och mappas manuellt med Transact-SQL- `CREATE USER` uttrycket i en specifik databas. Om `steve@gmail.com` till exempel är inbjuden till Azure AD `contosotest` (med Azure AD-domänen `contosotest.onmicrosoft.com` ) måste en Azure AD-grupp, till exempel, `usergroup` skapas i Azure AD som innehåller `steve@gmail.com` medlemmen. Sedan måste den här gruppen skapas för en särskild databas (dvs. databas) av Azure AD SQL-administratören eller Azure AD DBO genom att köra ett Transact-SQL- `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` uttryck. När databas användaren har skapats `steve@gmail.com` kan användaren logga in till `MyDatabase` med alternativet SSMS-autentisering `Active Directory – Universal with MFA support` . Usergroup har som standard bara Connect-behörigheten och all ytterligare data åtkomst som behöver beviljas på normalt sätt. Observera att användaren `steve@gmail.com` som gäst användare måste markera kryss rutan och lägga till AD-domännamnet `contosotest.onmicrosoft.com` i dialog rutan SSMS **Connection Property** . Alternativet **AD-domännamn eller klient-ID** stöds bara för universellt med MFA-anslutnings alternativ, annars är det nedtonad.

## <a name="universal-authentication-limitations"></a>Begränsningar för universell autentisering

- SSMS och SqlPackage. exe är de enda verktyg som är aktiverade för MFA genom Active Directory Universal Authentication.
- SSMS version 17,2, stöder samtidig åtkomst till flera användare med hjälp av Universal Authentication med MFA. Version 17,0 och 17,1 begränsar en inloggning för en instans av SSMS med hjälp av Universal Authentication till ett enda Azure Active Directory konto. Om du vill logga in som ett annat Azure AD-konto måste du använda en annan instans av SSMS. (Den här begränsningen är begränsad till Active Directory Universal Authentication. du kan logga in på en annan server med Active Directory lösenordsautentisering, Active Directory integrerad autentisering eller SQL Server autentisering).
- SSMS stöder Active Directory Universal Authentication för Object Explorer, Frågeredigeraren och visualisering av fråge arkiv.
- SSMS version 17,2 ger stöd för DacFx-guiden för export/extrahering/distribution av data databaser. När en enskild användare har autentiserats via dialog rutan inledande autentisering med hjälp av Universal Authentication, fungerar DacFx-guiden på samma sätt som för alla andra autentiseringsmetoder.
- SSMS-Tabelldesigner stöder inte Universal-autentisering.
- Det finns inga ytterligare program varu krav för Active Directory Universal Authentication, förutom att du måste använda en version av SSMS som stöds.  
- Active Directory-autentiseringsbibliotek-versionen (ADAL) för Universal Authentication har uppdaterats till den senaste ADAL. dll-3.13.9 tillgänglig version. Se [Active Directory-autentiseringsbibliotek 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Nästa steg

- Konfigurations steg finns i [konfigurera Azure SQL Database Multi-Factor Authentication för SQL Server Management Studio](authentication-mfa-ssms-configure.md).
- Ge andra åtkomst till databasen: [SQL Database autentisering och auktorisering: bevilja åtkomst](logins-create-manage.md)  
- Se till att andra kan ansluta genom brand väggen: [Konfigurera en brand Väggs regel på server nivå med hjälp av Azure Portal](firewall-configure.md)  
- [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure Synapse](authentication-aad-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importera en BACPAC-fil till en ny Azure SQL-databas](database-import.md)  
- [Exportera en Azure SQL-databas till en BACPAC-fil](database-export.md)  
- C# interface [IUniversalAuthProvider-gränssnitt](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- När du använder **Active Directory-universell med MFA-** autentisering, är ADAL-spårning tillgängligt från och med [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Av som standard kan du aktivera ADAL-spårning genom att använda menyn **verktyg**, **alternativ** , under Azure- **tjänster**, **Azure Cloud**, **ADAL fönstret utmatning spårnings nivå**, och sedan aktivera **utdata** i menyn **Visa** . Spårningarna är tillgängliga i fönstret utdata när du väljer **Azure Active Directory alternativ**.  
