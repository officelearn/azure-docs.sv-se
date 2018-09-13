---
title: Konfigurera Multi-Factor authentication - Azure SQL | Microsoft Docs
description: Lär dig hur du använder Multi-Factored autentisering med SSMS för SQL Database och SQL Data Warehouse.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.prod_service: sql-database, sql-data-warehouse
ms.custom: security
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: mireks
ms.reviewer: vanto
ms.openlocfilehash: 504b20dfddb5984c17bba9842fbc1a08671c4175
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719043"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurera Multi-Factor authentication för SQL Server Management Studio och Azure AD

Det här avsnittet visar hur du använder Azure Active Directory multifaktorautentisering (MFA) med SQL Server Management Studio. Azure AD MFA kan användas vid anslutning av SSMS eller SqlPackage.exe till Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt över Azure SQL Database multifaktorautentisering finns i [universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

## <a name="configuration-steps"></a>Konfigurationssteg

1. **Konfigurera en Azure Active Directory** – mer information finns i [administrera Azure AD-katalogen](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrera dina lokala identiteter med Azure Active Directory](../active-directory/active-directory-aadconnect.md), [ Lägga till ditt eget domännamn i Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure har nu stöd för federation med Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), och [hantera Azure AD med hjälp av Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurera MFA** – stegvisa instruktioner finns i [vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [villkorlig åtkomst (MFA) med Azure SQL Database och Data Warehouse](sql-database-conditional-access.md). (Fullständig villkorlig åtkomst kräver en Premium Azure Active Directory (AD Azure). Begränsad MFA är tillgänglig med en Azure AD som standard.)
3. **Konfigurera SQL Database eller SQL Data Warehouse för Azure AD-autentisering** – stegvisa instruktioner finns i [ansluta till SQL Database eller SQL Data Warehouse med hjälp av Azure Active Directory-autentisering](sql-database-aad-authentication.md).
4. **Ladda ned SSMS** – på klientdatorn, ladda ned den senaste SSMS från [ladda ned SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). För alla funktioner i det här avsnittet Använd minst version 17,2 i juli 2017.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ansluta med hjälp av universell autentisering med SSMS

Följande steg visar hur du ansluter till SQL Database eller SQL Data Warehouse med hjälp av den senaste SSMS.

1. Att ansluta med universell autentisering på den **Anslut till Server** dialogrutan **Active Directory - Universal med stöd för MFA**. (Om du ser **Active Directory Universal-autentisering** du inte är den senaste versionen av SSMS.)  
   ![1mfa-universal-ansluta][1]  
2. Slutför den **användarnamn** med Azure Active Directory-autentiseringsuppgifter i formatet `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Om du ansluter som gästanvändare, måste du klicka **alternativ**, och på den **Anslutningsegenskapen** dialogrutan fullständig den **AD-domän eller klient-ID** box. Mer information finns i [universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).
   ![ssms – mfa-klient](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Som vanligt för SQL Database och SQL Data Warehouse kan du klicka på **alternativ** och ange databasen på den **alternativ** dialogrutan. (Om den anslutna användaren är en gästanvändare (d.v.s. joe@outlook.com), måste du markera kryssrutan och lägga till det aktuella AD-domännamnet eller klient-ID som en del av alternativen. Se [universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md). Klicka sedan på **Anslut**.  
5. När den **logga in på ditt konto** dialogruta, anger du kontot och lösenordet för din Azure Active Directory-identitet. Inga lösenord krävs om en användare tillhör en domän som federeras med Azure AD.  
   ![2mfa logga in][2]  

   > [!NOTE]
   > För universell autentisering med ett konto som inte kräver MFA, ansluter du nu. För användare som behöver MFA, fortsätter du med följande steg:
   >  
   
6. Två MFA dialogrutor visas. Det här en gång åtgärden beror på MFA-administratören ställa in och kan därför valfritt. För en domän för MFA-aktiverat det här steget är ibland fördefinierade (till exempel domänen kräver att användare använder ett smartkort och PIN-kod).  
   ![3mfa-installation][3]  
7. Andra möjliga en gång i dialogrutan som visas kan du välja information om autentiseringsmetod. Möjliga alternativ har konfigurerats av administratören.  
   ![4mfa kontrollera 1][4]  
8. Azure Active Directory skickar bekräftande informationen till dig. När du har fått verifieringskoden anger du den i den **ange Verifieringskod** och på **logga in**.  
   ![5mfa Kontrollera 2][5]  

När verifieringen är klar ansluter SSMS normalt förutsätter giltiga autentiseringsuppgifter och brandväggsinställningarna för åtkomst.

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure SQL Database multifaktorautentisering finns i universell autentisering med [SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).  
- Bevilja andra åtkomst till din databas: [SQL Database, autentisering och auktorisering: bevilja åtkomst](sql-database-manage-logins.md)  
- Kontrollera att andra kan ansluta genom brandväggen: [konfigurera en Azure SQL Database-brandväggsregel på servernivå med hjälp av Azure portal](sql-database-configure-firewall-settings.md)  
- När du använder **Active Directory - Universal med MFA** autentisering, ADAL spårning är tillgängliga från och med [SSMS 17,3 tum](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Inaktivera som standard, du kan aktivera ADAL spårning med hjälp av den **verktyg**, **alternativ** menyn under **Azure Services**, **Azure-molnet**,  **ADAL utdata fönstret spårningsnivån**, följt av att aktivera **utdata** i den **visa** menyn. Spårningarna är tillgängliga i utdatafönstret när du väljer **Azure Active Directory-alternativet**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

