---
title: Multifaktorautentisering - Azure SQL | Microsoft Docs
description: "Azure SQL Database och Azure SQL Data Warehouse stöder anslutningar från SQL Server Management Studio (SSMS) med hjälp av Active Directory Universal-autentisering."
services: sql-database
documentationcenter: 
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 09/29/2017
ms.author: mireks
ms.openlocfilehash: 3a950ac837b543cbcbfd4ae43cf4b9ebeb537cd5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Universal autentisering med SQL Database och SQL Data Warehouse (SSMS stöd för MFA)
Azure SQL Database och Azure SQL Data Warehouse stöder anslutningar från SQL Server Management Studio (SSMS) med hjälp av *Active Directory Universal autentisering*. 
**Hämta den senaste SSMS** - på klientdatorn, ladda ned den senaste versionen av SSMS, från [Hämta SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). För alla funktioner i det här avsnittet Använd minst version 17,2 juli 2017.  Dialogrutan senaste anslutning ser ut så här: ![1mfa universal ansluta](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "har slutförts rutan användarnamn.")  

## <a name="the-five-authentication-options"></a>Fem autentiseringsalternativ  
- Active Directory Universal autentisering stöder två metoder för icke-interaktiv autentisering (`Active Directory - Password` autentisering och `Active Directory - Integrated` autentisering). Icke-interaktiv `Active Directory - Password` och `Active Directory - Integrated` autentiseringsmetoder kan användas i många olika program (ADO.NET, JDBC, ODBC, etc.). Dessa två metoder innebär aldrig popup-dialogrutor.

- `Active Directory - Universal with MFA` autentisering är en interaktiv metod som också stöder *Azure Multi-Factor Authentication* (MFA). Azures MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Den ger stark autentisering med ett antal alternativ för enkel verifiering (telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp), så att användare kan välja metoden som de själva föredrar. Interaktiv MFA med Azure AD kan resultera i en dialogruta för verifiering.

En beskrivning av Multi-Factor Authentication finns [Multifaktorautentisering](../multi-factor-authentication/multi-factor-authentication.md).
Konfigurationssteg finns [konfigurera Azure SQL Database Multi-Factor authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD domain name eller klient ID-parametern   

Från och med [SSMS version 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), kan användare som har importerats till den aktuella Active Directory från andra Azure Active kataloger som gästanvändare, ange domännamnet för Azure AD eller klient-ID när de ansluter. Gästanvändare innehåller användare som bjudits in från andra Azure Active Directory, Microsoft-konton som outlook.com, hotmail.com, live.com eller andra konton som gmail.com. Den här informationen kan **Active Directory Universal med MFA autentisering** att identifiera rätt autentiseringsutfärdare saknas. Det här alternativet krävs också stöd för Microsoft-konton (MSA), till exempel outlook.com, hotmail.com, live.com eller andra mejlkonton än Microsoft-konton. Dessa användare som vill autentiseras med hjälp av Universal autentisering måste ange sina Azure AD-domän eller klient-ID. Den här parametern representerar den aktuella Azure AD domain namn/klient-ID Azure-servern är kopplad till. Om Azure-Server som är associerad med Azure AD-domänen till exempel `contosotest.onmicrosoft.com` där användaren `joe@contosodev.onmicrosoft.com` värdbaserad som en importerad användare från Azure AD-domänen `contosodev.onmicrosoft.com`, domännamnet krävs för att autentisera den här användaren är `contosotest.onmicrosoft.com`. När användaren är en inbyggd länkad till Azure Server Azure AD-användare och är inte en MSA-konto, krävs ingen domän namn eller klient-ID. Att ange parametern (som börjar med SSMS version 17,2), i den **Anslut till databas** dialogrutan Fyll i dialogrutan Markera **Active Directory - Universal med MFA** autentisering, klickar du på  **Alternativ**, fullständig den **användarnamn** rutan och klicka på den **anslutningsegenskaper** fliken. Kontrollera den **AD domän namn eller klient-ID** rutan och ange autentiseringsutfärdare saknas, till exempel namnet på en domän (**contosotest.onmicrosoft.com**) eller GUID för klient-ID.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Azure AD-stöd för företag   
Azure AD-användare som stöds i Azure AD B2B-scenarier som gästanvändare (se [vad är Azure B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kan ansluta till SQL Database och SQL Data Warehouse endast som en del av medlemmar i en grupp som skapats i aktuell Azure AD och mappa manuellt med hjälp av Transact-SQL `CREATE USER` instruktionen i en viss databas. Till exempel om `steve@gmail.com` uppmanas att Azure AD `contosotest` (med Azure Ad-domänen `contosotest.onmicrosoft.com`), en Azure AD gruppen som `usergroup` måste skapas i Azure AD som innehåller den `steve@gmail.com` medlem. Sedan den här gruppen måste skapas för en viss databas (d.v.s. mindatabas) av Azure AD-SQL-administratör eller Azure AD DBO genom att köra Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` instruktionen. När databasanvändaren har skapats kan sedan användaren `steve@gmail.com` kan logga in på `MyDatabase` med alternativet SSMS autentisering `Active Directory – Universal with MFA support`. Usergroup, har som standard bara behörigheten connect och eventuella ytterligare åtkomst till data som måste beviljas på normalt sätt. Observera att användaren `steve@gmail.com` som gästanvändare måste du kryssrutan och lägga till domännamn AD `contosotest.onmicrosoft.com` i SSMS **Anslutningsegenskapen** dialogrutan. Den **AD domän namn eller klient-ID** alternativet stöds bara för Universal med MFA anslutningsalternativ, annars den är nedtonat.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Universal autentisering begränsningar för SQL Database och SQL Data Warehouse
- SSMS och SqlPackage.exe är endast verktyg som aktiverats för MFA via Active Directory Universal autentisering.
- SSMS version 17,2, stöder flera samtidiga användaråtkomst med Universal autentisering med MFA. Version 17,0 och 17,1, begränsad en logg i för en instans av SSMS med Universal autentisering till ett enda Azure Active Directory-konto. Logga in som en annan Azure AD-kontot, måste du använda en annan instans av SSMS. (Den här begränsningen är begränsad till Active Directory Universal autentisering, du kan logga in till andra servrar som använder Active Directory-lösenordsautentisering, Active Directory-integrerad autentisering eller SQL Server-autentisering).
- SSMS stöder Active Directory Universal autentisering för visualisering Object Explorer och frågeredigeraren Query Store.
- SSMS version 17,2 stöder DacFx guiden Distribuera-Export/extrahera Data-databas. När en viss användare autentiseras via dialogrutan första autentiseringen med hjälp av Universal autentisering, DacFx guiden fungerar på samma sätt som för alla andra metoder för autentisering.
- Tabelldesign SSMS stöder inte universella autentisering.
- Det finns inga ytterligare programvarukrav för Active Directory Universal autentisering förutom att du måste använda en version som stöds av SSMS.  
- Active Directory Authentication Library (ADAL)-version för Universal autentisering har uppdaterats till den senaste ADAL.dll 3.13.9 tillgängliga versionen. Se [Active Directory Authentication Library 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Nästa steg

- Konfigurationssteg finns [konfigurera Azure SQL Database Multi-Factor authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Ge andra användare åtkomst till databasen: [SQL Database-autentisering och auktorisering: bevilja åtkomst](sql-database-manage-logins.md)  
- Kontrollera att andra kan ansluta genom brandväggen: [konfigurera en Azure SQL Database servernivå brandväggsregel med Azure-portalen](sql-database-configure-firewall-settings.md)  
- [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Importera en BACPAC-fil till en ny Azure SQL-databas](../sql-database/sql-database-import.md)  
- [Exportera en Azure SQL database till en BACPAC-fil](../sql-database/sql-database-export.md)  
- C#-gränssnitt [IUniversalAuthProvider gränssnitt](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- När du använder **Active Directory - Universal med MFA** autentisering, ADAL spårning är tillgängliga från och med [SSMS 17,3 tum](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Inaktiverar som standard, du kan aktivera ADAL spårning med hjälp av den **verktyg**, **alternativ** menyn under **Azure Services**, **Azure-molnet**,  **ADAL utdata fönstret spårningsnivån**följt genom att aktivera **utdata** i den **visa** menyn. Spåren är tillgängliga i utdatafönstret när du väljer **Azure Active Directory-alternativet**.  
