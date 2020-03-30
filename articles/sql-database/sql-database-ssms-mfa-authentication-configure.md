---
title: Konfigurera multifaktorautentisering
description: Lär dig hur du använder multifaktorerad autentisering med SSMS för SQL Database och SQL Data Warehouse.
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
ms.openlocfilehash: 5d4d410f6fca566dab14e601972952b5996c331a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124891"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurera multifaktorautentisering för SQL Server Management Studio och Azure AD

Det här avsnittet visar hur du använder Azure Active Directory multifaktorautentisering (MFA) med SQL Server Management Studio. Azure AD MFA kan användas vid anslutning av SSMS eller SqlPackage.exe till Azure [SQL Database](sql-database-technical-overview.md) och SQL [Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt över Azure SQL Database multifaktorautentisering finns i [Universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

## <a name="configuration-steps"></a>Konfigurationssteg

1. **Konfigurera en Azure Active Directory** – Mer information finns i Administrera din Azure [AD-katalog,](https://msdn.microsoft.com/library/azure/hh967611.aspx) [Integrera dina lokala identiteter med Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), Lägg till ditt eget domännamn i Azure [AD,](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) [Microsoft Azure stöder nu federation med Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)och [Hantera Azure AD med Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurera MFA** – För steg-för-steg-instruktioner, se [Vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md), [Villkorlig åtkomst (MFA) med Azure SQL Database och Data Warehouse](sql-database-conditional-access.md). (Fullständig villkorlig åtkomst kräver en Premium Azure Active Directory (Azure AD). Begränsad MFA är tillgänglig med en standard Azure AD.)
3. **Konfigurera SQL Database eller SQL Data Warehouse för Azure AD-autentisering** – för steg-för-steg-instruktioner finns i [Ansluta till SQL Database eller SQL Data Warehouse med hjälp av Azure Active Directory Authentication](sql-database-aad-authentication.md).
4. **Ladda ner SSMS** - Ladda ner den senaste SSMS-datorn på klientdatorn från [Download SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/mt238290.aspx) För alla funktioner i det här avsnittet använder du åtminstone juli 2017, version 17.2.  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ansluta med universell autentisering med SSMS

Följande steg visar hur du ansluter till SQL Database eller SQL Data Warehouse med hjälp av den senaste SSMS.

1. Om du vill ansluta med universell autentisering väljer du **Active Directory - Universal med MFA-stöd**i dialogrutan Anslut till **server** . (Om du ser **Universell autentisering i Active Directory** finns du inte med i den senaste versionen av SSMS.)  
   ![1mfa-universal-anslut][1]  
2. Fyll i rutan **Användarnamn** med Azure Active Directory-autentiseringsuppgifterna i formatet `user_name@domain.com`.  
   ![1mfa-universal-connect-användare](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Om du ansluter som gästanvändare behöver du inte längre fylla i fältet AD-domännamn eller klient-ID för gästanvändare eftersom SSMS 18.x eller senare automatiskt känner igen det. Mer information finns i [Universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-no-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-no-tenant-ssms.png)

   Om du ansluter som gästanvändare med SSMS 17.x eller äldre måste du dock klicka på **Alternativ**och i dialogrutan **Anslutningsegenskap** och fylla i rutan **AD-domännamn eller klient-ID.**
   ![mfa-hyresgäst-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)

4. Som vanligt för SQL Database och SQL Data Warehouse måste du klicka på **Alternativ** och ange databasen i dialogrutan **Alternativ.** (Om den anslutna användaren är gästanvändare joe@outlook.com( dvs. ) måste du markera rutan och lägga till det aktuella AD-domännamnet eller klient-ID som en del av Alternativ. Se [Universell autentisering med SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md). Klicka sedan på **Anslut**.  
5. När dialogrutan **Logga in på ditt konto** visas anger du kontot och lösenordet för din Azure Active Directory-identitet. Inget lösenord krävs om en användare är en del av en domän som federerar med Azure AD.  
   ![2mfa-logga in][2]  

   > [!NOTE]
   > För universell autentisering med ett konto som inte kräver MFA ansluter du nu. För användare som behöver MFA fortsätter du med följande steg:
   >  
   
6. Två dialogrutor för MFA-inställningar kan visas. Den här en gångåtgärden beror på MFA-administratörsinställningen och kan därför vara valfri. För en MFA-aktiverad domän är det här steget ibland fördefinierat (till exempel kräver domänen att användarna använder ett smartkort och en pin).  
   ![3mfa-setup][3]  
7. Med den andra möjliga dialogrutan en gång kan du välja information om din autentiseringsmetod. De möjliga alternativen konfigureras av administratören.  
   ![4mfa-verifiera-1][4]  
8. Azure Active Directory skickar bekräftande information till dig. När du får verifieringskoden anger du den i rutan **Ange verifieringskod** och klickar på **Logga in**.  
   ![5mfa-verifiera-2][5]  

När verifieringen är klar ansluter SSMS normalt förutsätter giltiga autentiseringsuppgifter och brandväggsåtkomst.

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure SQL Database multifaktorautentisering finns i Universell autentisering med [SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](sql-database-ssms-mfa-authentication.md).  
- Ge andra åtkomst till databasen: [SQL Database-autentisering och auktorisering: Bevilja åtkomst](sql-database-manage-logins.md)  
- Se till att andra kan ansluta via brandväggen: [Konfigurera en brandväggsregel på Azure SQL Database-servernivå med Azure-portalen](sql-database-configure-firewall-settings.md)  
- När du använder **Active Directory- Universal med MFA-autentisering** är ADAL-spårning tillgänglig från och med [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Av som standard kan du aktivera ADAL-spårning med hjälp av **menyn Verktyg**, **Alternativ,** under **Azure Services**, **Azure Cloud**, **ADAL Output Window Trace Level**, följt av att aktivera **Utdata** på **Visa-menyn.** Spårningarna är tillgängliga i utdatafönstret när du väljer **Azure Active Directory-alternativ**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

