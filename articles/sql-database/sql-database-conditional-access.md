---
title: Villkorlig åtkomst
description: Lär dig hur du konfigurerar villkorlig åtkomst för Azure SQL Database och Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124895"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Villkorad åtkomst (MFA) med Azure SQL Database och Azure Synapse Analytics

Azure [SQL Database,](sql-database-technical-overview.md) [Managed Instance](sql-database-managed-instance.md)och Azure [Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) stöder Microsoft Conditional Access. 

> [!NOTE]
> Det här avsnittet gäller Azure SQL-server och både SQL Database och Azure Synapse som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure Synapse.

Följande steg visar hur du konfigurerar SQL Database för att framtvinga en princip för villkorlig åtkomst.  

## <a name="prerequisites"></a>Krav  
- Du måste konfigurera din SQL-databas eller SQL-pool i Azure Synapse för att stödja Azure Active Directory-autentisering. Specifika steg finns i [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller Azure Synapse](sql-database-aad-authentication-configure.md).  
- När multifaktorautentisering är aktiverat måste du ansluta till verktyget som stöds, till exempel den senaste SSMS. Mer information finns i [Konfigurera multifaktorautentisering i Azure SQL Database för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurera certifikatutfärdar för Azure SQL DB/DW  
1. Logga in på portalen, välj **Azure Active Directory**och välj sedan Villkorlig **åtkomst**. Mer information finns i [teknisk referens för Azure Active Directory Villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Bladet Villkorlig åtkomst](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. I bladet **Principer för villkorlig åtkomst** klickar du på Ny **princip,** anger ett namn och klickar sedan på **Konfigurera regler**.  
3. Under **Tilldelningar**väljer du **Användare och grupper**, markerar Välj användare och **grupper**och väljer sedan användaren eller gruppen för villkorlig åtkomst. Klicka på **Markera**och sedan på **Klar** för att acceptera ditt val.  
   ![välja användare och grupper](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Välj **Molnappar**och klicka på **Välj appar**. Du ser alla appar som är tillgängliga för villkorlig åtkomst. Välj **Azure SQL Database**, längst ned klickar du på **Markera**och sedan på **Klar**.  
   ![välj SQL-databas](./media/sql-database-conditional-access/select-sql-database.png)  
   Om du inte hittar **Azure SQL Database** i följande tredje skärmbild gör du följande:   
   - Logga in på din Azure SQL DB/DW-instans med SSMS med ett AAD-administratörskonto.  
   - Kör `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Logga in på AAD och kontrollera att Azure SQL Database och Azure Synapse visas i programmen i din AAD.  

5. Välj **Access-kontroller,** välj **Bevilja**och kontrollera sedan den princip som du vill tillämpa. I det här exemplet väljer vi **Kräv multifaktorautentisering**.  
   ![välj bidragsåtkomst](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Sammanfattning  
Det valda programmet (Azure SQL Database) som gör det möjligt att ansluta till Azure SQL DB/DW med Azure AD Premium, tillämpar nu den valda principen för villkorlig åtkomst, **krävs multifaktorautentisering.**  
Om du vill ha frågor om Azure SQL Database och MFAforSQLDB@microsoft.comAzure Synapse om multifaktorautentisering kontaktar du .  

## <a name="next-steps"></a>Nästa steg  

En självstudiekurs finns i [Skydda din Azure SQL-databas](sql-database-security-tutorial.md).
