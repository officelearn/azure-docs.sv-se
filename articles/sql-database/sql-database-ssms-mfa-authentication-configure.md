---
title: Konfigurera Multi-Factor authentication - Azure SQL | Microsoft Docs
description: "Lär dig hur du använder Multi-Factored autentisering med SSMS för SQL Database och SQL Data Warehouse."
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 09/27/2017
ms.author: mireks
ms.openlocfilehash: 7b74cd6b62686fd03d9f42316701f44daf99eed8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurera Multi-Factor authentication för SQL Server Management Studio och Azure AD

Det här avsnittet visar hur du använder Azure Active Directory multifaktorautentisering (MFA) med SQL Server Management Studio. Azure AD MFA kan användas när du ansluter SSMS eller SqlPackage.exe till Azure SQL Database och Azure SQL Data Warehouse.

En översikt över Azure SQL Database multifaktorautentisering finns [Universal autentisering med SQL Database och SQL Data Warehouse (SSMS stöd för MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Konfigurationssteg

1. **Konfigurera ett Azure Active Directory** – mer information finns i [administrera Azure AD-katalogen](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrera dina lokala identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md), [ Lägga till ett eget domännamn i Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure stöder nu federation med Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), och [hantera Azure AD med hjälp av Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurera MFA** - stegvisa anvisningar finns i [vad är Azure Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md), [villkorlig åtkomst (MFA) med Azure SQL Database och datalagret](sql-database-conditional-access.md). (Fullständig villkorlig åtkomst kräver en Premium Azure Active Directory (AD Azure). Begränsad MFA är tillgängligt med en Azure AD som standard.)
3. **Konfigurera SQL Database eller SQL Data Warehouse för Azure AD Authentication** - stegvisa anvisningar finns i [ansluta till SQL Database eller SQL Data Warehouse med hjälp av Azure Active Directory Authentication](sql-database-aad-authentication.md).
4. **Hämta SSMS** - på klientdatorn, ladda ned den senaste SSMS från [Hämta SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). För alla funktioner i det här avsnittet Använd minst version 17,2 juli 2017.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ansluta med hjälp av universal autentisering med SSMS

Följande steg visar hur du ansluter till SQL Database eller SQL Data Warehouse med hjälp av den senaste SSMS.

1. Att ansluta med Universal autentisering på den **Anslut till Server** dialogrutan **Active Directory - Universal med stöd för MFA**. (Om du ser **Active Directory Universal autentisering** du inte är den senaste versionen av SSMS.)  
   ![1mfa-universal-connect][1]  
2. Slutför den **användarnamn** rutan med Azure Active Directory-autentiseringsuppgifterna i formatet `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Om du ansluter som gästanvändare, måste du klicka **alternativ**, och på den **Anslutningsegenskapen** dialogrutan fullständig den **AD domän namn eller klient-ID** rutan. Mer information finns i [Universal autentisering med SQL Database och SQL Data Warehouse (SSMS stöd för MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Som vanligt för SQL Database och SQL Data Warehouse, måste du klicka på **alternativ** och ange databasen på den **alternativ** dialogrutan. (Om den anslutna användaren är en gästanvändare (d.v.s. joe@outlook.com), måste du markera kryssrutan och lägga till namnet på aktuella AD-domän eller klient-ID som en del av alternativen. Se [Universal autentisering med SQL Database och SQL Data Warehouse (SSMS stöd för MFA)]()(sql-databasen-ssms-mfa-authentication.md. Klicka på **Anslut**.  
5. När den **logga in på ditt konto** i dialogrutan Ange konto och lösenord för din Azure Active Directory-identitet. Inget lösenord krävs om en användare är en del av en domän som federerade med Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > För Universal autentisering med ett konto som inte kräver MFA, ansluter du nu. För användare som kräver MFA, fortsätter du med följande steg:
   >  
   
6. Två MFA dialogrutor visas. En gång åtgärden beror på MFA-administratören ställa in och kan därför valfria. För en domän för MFA är aktiverat i det här steget ibland är fördefinierad (till exempel domänen kräver att användare använder ett smartkort och PIN-kod).  
   ![3mfa-setup][3]  
7. Andra möjliga en gång dialogrutan kan du välja information om din autentiseringsmetod. Möjliga alternativ har konfigurerats av administratören.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory skickar bekräfta informationen till dig. När du har fått verifieringskoden anger du det i den **ange verifieringskoden** och på **logga in**.  
   ![5mfa-verify-2][5]  

När verifieringen är klar ansluter SSMS normalt förutsatt att giltiga autentiseringsuppgifter och åtkomst för brandväggen.

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure SQL Database multifaktorautentisering finns i Universal autentisering med [SQL Database och SQL Data Warehouse (SSMS stöd för MFA)](sql-database-ssms-mfa-authentication.md).  
- Ge andra användare åtkomst till databasen: [SQL Database-autentisering och auktorisering: bevilja åtkomst](sql-database-manage-logins.md)  
- Kontrollera att andra kan ansluta genom brandväggen: [konfigurera en Azure SQL Database servernivå brandväggsregel med Azure-portalen](sql-database-configure-firewall-settings.md)  
- När du använder **Active Directory - Universal med MFA** autentisering, ADAL spårning är tillgängliga från och med [SSMS 17,3 tum](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Inaktiverar som standard, du kan aktivera ADAL spårning med hjälp av den **verktyg**, **alternativ** menyn under **Azure Services**, **Azure-molnet**,  **ADAL utdata fönstret spårningsnivån**följt genom att aktivera **utdata** i den **visa** menyn. Spåren är tillgängliga i utdatafönstret när du väljer **Azure Active Directory-alternativet**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

