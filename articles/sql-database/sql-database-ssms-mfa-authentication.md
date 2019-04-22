---
title: Med flera faktorer AAD-autentisering med Azure SQL Database och Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Database och Azure SQL Data Warehouse stöder anslutningar från SQL Server Management Studio (SSMS) med hjälp av Active Directory Universal-autentisering.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: ccb78e201b90dfc27f52523348e76da57087bcc8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494909"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Med flera faktorer AAD-autentisering med Azure SQL Database och Azure SQL Data Warehouse (SSMS-stöd för MFA)
Azure SQL Database och Azure SQL Data Warehouse stöder anslutningar från SQL Server Management Studio (SSMS) med hjälp av *Active Directory Universal-autentisering*. Den här artikeln beskrivs skillnaderna mellan de olika autentiseringsalternativen och begränsningar med hjälp av universell autentisering. 

**Ladda ned den senaste SSMS** – på klientdatorn, ladda ned den senaste versionen av SSMS, från [ladda ned SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


För alla de funktioner som beskrivs i den här artikeln, Använd minst version 17,2 i juli 2017.  Dialogrutan senaste anslutning bör likna följande bild:
 
  ![1mfa-universal-ansluta](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "har slutförts rutan användarnamn.")  

## <a name="the-five-authentication-options"></a>Fem autentiseringsalternativ  

Active Directory Universal-autentisering har stöd för de två metoderna för icke-interaktiv autentisering:
    - `Active Directory - Password` Autentisering
    - `Active Directory - Integrated` Autentisering

Det finns två icke-interaktiv autentiseringsmodeller, som kan användas i många olika program (ADO.NET, JDCB, ODC osv.). Dessa två metoder innebär aldrig popup-dialogrutor: 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

Den interaktiva metoden är som också har stöd för Azure Multi-Factor authentication (MFA) är: 
- `Active Directory - Universal with MFA` 


Azures MFA bidrar till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål om en enkel inloggningsprocess. Du får stark autentisering med en rad enkla verifieringsalternativ (telefonsamtal, textmeddelande, smartkort och PIN-kod eller mobilapp), så att användarna kan välja metoden de föredrar. Interaktiv MFA med Azure AD kan resultera i en popup-dialogruta för verifiering.

En beskrivning av Multifaktorautentisering finns i [Multifaktorautentisering](../active-directory/authentication/multi-factor-authentication.md).
Konfigurationsanvisningar finns i [konfigurera Azure SQL Database Multi-Factor authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD domain name eller klient ID-parameter   

Från och med [SSMS version 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), kan användare som har importerats till aktuell Active Directory från andra aktiva Azure-kataloger som gästanvändare, ange domännamnet för Azure AD eller klient-ID när de ansluter. Gästanvändare inkludera användare bjuds in från andra Azure Active Directory, Microsoft-konton, till exempel outlook.com, hotmail.com, live.com eller andra konton som gmail.com. Den här informationen kan **Active Directory Universal med MFA-autentiseringen** att identifiera rätt autentiserande behörighet. Det här alternativet krävs även för Microsoft-konton (MSA), till exempel outlook.com, hotmail.com, live.com eller icke-MSA-konton. Dessa användare som vill autentiseras med hjälp av universell autentisering måste ange sina Azure AD-domän eller klient-ID. Den här parametern representerar det aktuella Azure AD-domän namn/klient-ID Azure Server är kopplad till. Exempel: om Azure-servern som är associerad med Azure AD-domän `contosotest.onmicrosoft.com` där användaren `joe@contosodev.onmicrosoft.com` finns som importerade användare från Azure AD-domän `contosodev.onmicrosoft.com`, domännamnet som krävs för att autentisera den här användaren är `contosotest.onmicrosoft.com`. När användaren är en intern användare av Azure AD länkad till Azure-Server och inte är en MSA-konto, krävs ingen domän eller klient-ID. Ange parametern (som börjar med SSMS version 17,2), i den **Anslut till databas** dialogrutan Fyll dialogrutan att välja **Active Directory - Universal med MFA** autentisering, klickar du på  **Alternativ**gör den **användarnamn** rutan och klicka sedan på den **anslutningsegenskaper** fliken. Kontrollera den **AD-domän eller klient-ID** rutan och ange autentiserande utfärdare, till exempel domännamnet (**contosotest.onmicrosoft.com**) eller GUID för klient-ID.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Stöd för Azure AD-företag   
Azure AD-användare som stöds för Azure AD B2B-scenarier som gästanvändare (se [vad är Azure B2B-samarbete](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) kan ansluta till SQL Database och SQL Data Warehouse endast som en del av medlemmar i en grupp som skapats i aktuella Azure AD och mappa manuellt med hjälp av Transact-SQL `CREATE USER` instruktionen i en viss databas. Till exempel om `steve@gmail.com` bjuds in till Azure AD `contosotest` (med Azure Ad-domänen `contosotest.onmicrosoft.com`), en Azure AD gruppen som `usergroup` måste skapas i Azure AD som innehåller den `steve@gmail.com` medlem. Sedan den här gruppen måste skapas för en viss databas (det vill säga MyDatabase) av Azure AD-SQL-administratör eller Azure AD DBO genom att köra ett Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` instruktionen. När databasanvändaren har skapats kan sedan användaren `steve@gmail.com` kan logga in till `MyDatabase` med hjälp av SSMS autentiseringsalternativet `Active Directory – Universal with MFA support`. Usergroup, har som standard bara behörigheten connect och eventuella ytterligare dataåtkomst som måste beviljas på normalt sätt. Observera att användaren `steve@gmail.com` som en gästanvändare måste markera kryssrutan och lägga till AD-domännamn `contosotest.onmicrosoft.com` i SSMS **Anslutningsegenskapen** dialogrutan. Den **AD-domän eller klient-ID** alternativet stöds bara för ett universellt med MFA-anslutningsalternativ, annars den är nedtonat.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Universell autentisering begränsningar för SQL Database och SQL Data Warehouse
- SSMS och SqlPackage.exe är de enda verktyg som aktiverats för MFA via Active Directory Universal-autentisering.
- SSMS version 17,2, har stöd för flera användare samtidig åtkomst med hjälp av universell autentisering med MFA. Version 17,0 och 17,1, begränsad en inloggning för en instans av SSMS med universell autentisering till ett enda Azure Active Directory-konto. Om du vill logga in som en annan Azure AD-konto, måste du använda en annan instans av SSMS. (Den här begränsningen är begränsad till Active Directory Universal-autentisering; du kan logga in på olika servrar med Active Directory-lösenordsautentisering, Active Directory-integrerad autentisering eller SQL Server-autentisering).
- SSMS stöder Active Directory Universal-autentisering för Object Explorer och Query Editor Query Store visualisering.
- SSMS version 17,2 har stöd för DacFx-guiden för Export/extrahera/distribuera Data database. När en viss användare autentiseras via dialogrutan den inledande autentiseringen med Universal-autentisering måste guiden DacFx-funktioner på samma sätt som för alla andra autentiseringsmetoder.
- Tabelldesign SSMS stöder inte universell autentisering.
- Det finns inga ytterligare programvarukrav för Active Directory Universal-autentisering, förutom att du måste använda en version som stöds av SSMS.  
- Active Directory Authentication Library (ADAL)-version för universell autentisering har uppdaterats till den senaste tillgängliga utgivna ADAL.dll 3.13.9-versionen. Se [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Nästa steg

- Konfigurationsanvisningar finns i [konfigurera Azure SQL Database Multi-Factor authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Bevilja andra åtkomst till din databas: [SQL Database, autentisering och auktorisering: Bevilja åtkomst](sql-database-manage-logins.md)  
- Kontrollera att andra kan ansluta genom brandväggen: [Konfigurera en Azure SQL Database-brandväggsregel på servernivå med hjälp av Azure portal](sql-database-configure-firewall-settings.md)  
- [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importera en BACPAC-fil till en ny Azure SQ Database](../sql-database/sql-database-import.md)  
- [Exportera en Azure SQL-databas till en BACPAC-fil](../sql-database/sql-database-export.md)  
- C#-gränssnitt [IUniversalAuthProvider gränssnitt](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- När du använder **Active Directory - Universal med MFA** autentisering, ADAL spårning är tillgängliga från och med [SSMS 17,3 tum](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Inaktivera som standard, du kan aktivera ADAL spårning med hjälp av den **verktyg**, **alternativ** menyn under **Azure Services**, **Azure-molnet**,  **ADAL utdata fönstret spårningsnivån**, följt av att aktivera **utdata** i den **visa** menyn. Spårningarna är tillgängliga i utdatafönstret när du väljer **Azure Active Directory-alternativet**.  
