---
title: Konfigurera multifaktorautentisering
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Lär dig hur du använder multi-factord Authentication med SSMS för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 791402f5d9648c8d235f8853de1b6c41f8082e1b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018338"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Konfigurera Multi-Factor Authentication för SQL Server Management Studio och Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) med SQL Server Management Studio (SSMS). Azure AD MFA kan användas när du ansluter SSMS eller SqlPackage.exe till [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) och [azure Synapse Analytics (tidigare Azure SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). En översikt över Multi-Factor Authentication finns i [Universal Authentication with SQL Database, SQL-hanterad instans och Azure Synapse (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> Databaser i Azure SQL Database, Azure SQL-hanterad instans och Azure-Synapse (tidigare Azure SQL Data Warehouse) kallas kollektivt i resten av den här artikeln som databaser, och servern refererar till den [Server](logical-servers.md) som är värd för databaser för Azure SQL Database och Azure Synapse.

## <a name="configuration-steps"></a>Konfigurationssteg

1. **Konfigurera en Azure Active Directory** – mer information finns i [Administrera Azure AD-katalogen](https://msdn.microsoft.com/library/azure/hh967611.aspx), [integrera dina lokala identiteter med Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [lägga till ditt eget domän namn i Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure nu stöder Federation med Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)och [Hantera Azure AD med hjälp av Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Konfigurera MFA** -för steg-för-steg-instruktioner, se [vad är Azure Multi-Factor Authentication?](../../active-directory/authentication/multi-factor-authentication.md), [villkorlig åtkomst (MFA) med Azure SQL Database och informations lager](conditional-access-configure.md). (Fullständig villkorlig åtkomst kräver en Premium-Azure Active Directory. Begränsad MFA är tillgängligt med en standard Azure AD.)
3. **Konfigurera Azure AD-autentisering** – stegvisa anvisningar finns i [ansluta till SQL Database, SQL-hanterad instans eller Azure-Synapse med Azure Active Directory autentisering](authentication-aad-overview.md).
4. **Hämta SSMS** – på klient datorn laddar du ned de senaste SSMS från [nedladdnings SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ansluta med hjälp av Universal Authentication med SSMS

Följande steg visar hur du ansluter med den senaste SSMS.

1. Om du vill ansluta med Universal Authentication går du till dialog rutan **Anslut till Server** i SQL Server Management Studio (SSMS) och väljer **Active Directory-Universal med MFA-stöd**. (Om du ser **Active Directory Universal Authentication** får du inte den senaste versionen av SSMS.)

   ![1mfa – Universal-Connect](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Fyll i rutan **användar namn** med Azure Active Directory autentiseringsuppgifter i formatet `user_name@domain.com` .

   ![1mfa – Universal-Connect-User](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Om du ansluter som en gäst användare behöver du inte längre fylla i fältet AD-domännamn eller klient-ID för gäst användare eftersom SSMS 18. x eller senare identifierar det automatiskt. Mer information finns i [Universal Authentication with SQL Database, SQL-hanterad instans och Azure Synapse (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md).

   ![MFA-No-Tenant-SSMS](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Men om du ansluter som en gäst användare som använder SSMS 17. x eller äldre, måste du klicka på **alternativ**och i dialog rutan **anslutnings egenskaper** och fylla i rutan **AD-domännamn eller klient-ID** .

   ![MFA-Tenant-SSMS](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Välj **alternativ** och ange databasen i dialog rutan **alternativ** . (Om den anslutna användaren är gäst användare (d.v.s. joe@outlook.com ) måste du markera kryss rutan och lägga till det aktuella AD-domännamnet eller klient-ID som en del av alternativen. Se [Universal Authentication with SQL Database och SQL Data Warehouse (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md). Klicka sedan på **Anslut**.  
5. När dialog rutan **Logga in på ditt konto** visas anger du Azure Active Directory identitetens konto och lösen ord. Inget lösen ord krävs om en användare är en del av en domän som är federerad med Azure AD.

   ![2mfa – logga in](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > För Universal Authentication med ett konto som inte kräver MFA ansluter du nu. Fortsätt med följande steg för användare som behöver MFA:
   >  

6. Dialog rutor för två MFA-inställningar kan visas. Denna tids åtgärd beror på inställningen MFA-administratör och kan därför vara valfri. För en MFA-aktiverad domän är det här steget ibland fördefinierat (till exempel kräver domänen att användare använder smartkort och PIN-kod).

   ![3mfa – installation](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. I dialog rutan andra möjliga en gång kan du välja information om autentiseringsmetoden. De möjliga alternativen konfigureras av administratören.

   ![4mfa-verifiera-1](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory skickar bekräftelse informationen till dig. När du tar emot verifierings koden anger du den i rutan **Ange verifierings kod** och klickar på **Logga**in.

   ![5mfa-verifiera-2](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

När verifieringen är klar, antas SSMS-anslutningar normalt giltiga autentiseringsuppgifter och brand Väggs åtkomst.

## <a name="next-steps"></a>Nästa steg

- En översikt över Multi-Factor Authentication finns i [Universal Authentication with SQL Database, SQL-hanterad instans och Azure Synapse (SSMS-stöd för MFA)](../database/authentication-mfa-ssms-overview.md).  
- Ge andra åtkomst till databasen: [SQL Database autentisering och auktorisering: bevilja åtkomst](logins-create-manage.md)  
- Se till att andra kan ansluta genom brand väggen: [Konfigurera en brand Väggs regel på server nivå med hjälp av Azure Portal](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)  
- När du använder **Active Directory-universell med MFA-** autentisering, är ADAL-spårning tillgängligt från och med [SSMS 17,3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Av som standard kan du aktivera ADAL-spårning genom att använda menyn **verktyg**, **alternativ** , under Azure- **tjänster**, **Azure Cloud**, **ADAL fönstret utmatning spårnings nivå**, och sedan aktivera **utdata**  i menyn **Visa** . Spårningarna är tillgängliga i fönstret utdata när du väljer **Azure Active Directory alternativ**.
