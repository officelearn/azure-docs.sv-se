---
title: Konfigurera Multi-Factor Authentication – Azure SQL
description: Lär dig hur du använder multi-Factord Authentication med SSMS för SQL Database och SQL Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 37b962b84e72cf3b0005ad744d81974a55c1a75e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687103"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurera Multi-Factor Authentication för SQL Server Management Studio och Azure AD

Det här avsnittet visar hur du använder Azure Active Directory Multi-Factor Authentication (MFA) med SQL Server Management Studio. Du kan använda Azure AD MFA när du ansluter SSMS eller SqlPackage. exe till Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt över Azure SQL Database Multi-Factor Authentication finns i [Universal Authentication with SQL Database and SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

## <a name="configuration-steps"></a>Konfigurationssteg

1. **Konfigurera en Azure Active Directory** – mer information finns i [Administrera Azure AD-katalogen](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrera dina lokala identiteter med Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [lägga till ditt eget domän namn i Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [ Microsoft Azure stöder nu Federation med Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)och [Hantera Azure AD med hjälp av Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurera MFA** -för steg-för-steg-instruktioner, se [vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [villkorlig åtkomst (MFA) med Azure SQL Database och informations lager](sql-database-conditional-access.md). (Fullständig villkorlig åtkomst kräver ett Premium-Azure Active Directory (Azure AD). Begränsad MFA är tillgängligt med en standard Azure AD.)
3. **Konfigurera SQL Database eller SQL Data Warehouse för Azure AD-autentisering** – stegvisa anvisningar finns i [ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory autentisering](sql-database-aad-authentication.md).
4. **Hämta SSMS** – på klient datorn laddar du ned de senaste SSMS från [nedladdnings SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). För alla funktioner i det här avsnittet använder du minst juli 2017, version 17,2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ansluta med hjälp av Universal Authentication med SSMS

Följande steg visar hur du ansluter till SQL Database eller SQL Data Warehouse med hjälp av den senaste SSMS.

1. Om du vill ansluta med Universal Authentication går du till dialog rutan **Anslut till Server** och väljer **Active Directory-Universal med MFA-stöd**. (Om du ser **Active Directory Universal Authentication** får du inte den senaste versionen av SSMS.)  
   ![1mfa – Universal-Connect][1]  
2. Fyll i rutan **användar namn** med Azure Active Directory autentiseringsuppgifter i formatet `user_name@domain.com`.  
   ![1mfa – Universal-Connect-User](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Om du ansluter som en gäst användare behöver du inte längre fylla i fältet AD-domännamn eller klient-ID för gäst användare eftersom SSMS 18. x eller senare identifierar det automatiskt. Mer information finns i [Universal Authentication with SQL Database and SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).
   ![MFA-No-Tenant-SSMS](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Men om du ansluter som en gäst användare som använder SSMS 17. x eller äldre, måste du klicka på **alternativ**och i dialog rutan **anslutnings egenskaper** och fylla i rutan **AD-domännamn eller klient-ID** .
   ![MFA-Tenant-SSMS](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Som vanligt för SQL Database och SQL Data Warehouse måste du klicka på **alternativ** och ange databasen i dialog rutan **alternativ** . (Om den anslutna användaren är en gäst användare (t. ex. joe@outlook.com) måste du markera kryss rutan och lägga till det aktuella AD-domännamnet eller klient-ID som en del av alternativen. Se [Universal Authentication with SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md). Klicka sedan på **Anslut**.  
5. När dialog rutan **Logga in på ditt konto** visas anger du Azure Active Directory identitetens konto och lösen ord. Inget lösen ord krävs om en användare är en del av en domän som är federerad med Azure AD.  
   ![2mfa-inloggning][2]  

   > [!NOTE]
   > För Universal Authentication med ett konto som inte kräver MFA ansluter du nu. Fortsätt med följande steg för användare som behöver MFA:
   >  
   
6. Dialog rutor för två MFA-inställningar kan visas. Denna tids åtgärd beror på inställningen MFA-administratör och kan därför vara valfri. För en MFA-aktiverad domän är det här steget ibland fördefinierat (till exempel kräver domänen att användare använder smartkort och PIN-kod).  
   ![3mfa – installation][3]  
7. I dialog rutan andra möjliga en gång kan du välja information om autentiseringsmetoden. De möjliga alternativen konfigureras av administratören.  
   ![4mfa-verifiera-1][4]  
8. Azure Active Directory skickar bekräftelse informationen till dig. När du tar emot verifierings koden anger du den i rutan **Ange verifierings kod** och klickar på **Logga**in.  
   ![5mfa-verify-2][5]  

När verifieringen är klar, antas SSMS-anslutningar normalt giltiga autentiseringsuppgifter och brand Väggs åtkomst.

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure SQL Database Multi-Factor Authentication finns i Universal Authentication with [SQL Database and SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).  
- Ge andra åtkomst till databasen: [SQL Database autentisering och auktorisering: bevilja åtkomst](sql-database-manage-logins.md)  
- Se till att andra kan ansluta genom brand väggen: [Konfigurera en Azure SQL Database brand Väggs regel på server nivå med hjälp av Azure Portal](sql-database-configure-firewall-settings.md)  
- När du använder **Active Directory-universell med MFA-** autentisering, är ADAL-spårning tillgängligt från och med [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Av som standard kan du aktivera ADAL-spårning genom att använda menyn **verktyg**, **alternativ** , under Azure- **tjänster**, **Azure Cloud**, **ADAL fönstret utmatning spårnings nivå**, och sedan aktivera **utdata** i menyn **Visa** . Spårningarna är tillgängliga i fönstret utdata när du väljer **Azure Active Directory alternativ**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

