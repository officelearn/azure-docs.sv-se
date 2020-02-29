---
title: Villkorlig åtkomst
description: Lär dig hur du konfigurerar villkorlig åtkomst för Azure SQL Database och Azure-Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: f2431ee7c62079a3691a5ea99e562460df8f9309
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197580"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>Villkorlig åtkomst (MFA) med Azure SQL Database-och Azure Synapse-analys

Azure [SQL Database](sql-database-technical-overview.md), [hanterad instans](sql-database-managed-instance.md)och [Azure Synapse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) stöder villkorlig åtkomst från Microsoft. 

> [!NOTE]
> Det här avsnittet gäller för Azure SQL Server och för både SQL Database och Azure-Synapse som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure-Synapse.

Följande steg visar hur du konfigurerar SQL Database att tillämpa en princip för villkorlig åtkomst.  

## <a name="prerequisites"></a>Förutsättningar  
- Du måste konfigurera SQL Database eller SQL-poolen i Azure-Synapse för att stödja Azure Active Directory autentisering. Detaljerade anvisningar finns i [Konfigurera och hantera Azure Active Directory autentisering med SQL Database eller Azure Synapse](sql-database-aad-authentication-configure.md).  
- När Multi-Factor Authentication har Aktiver ATS måste du ansluta till ett verktyg som stöds, till exempel den senaste SSMS. Mer information finns i [konfigurera Azure SQL Database Multi-Factor Authentication för SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Konfigurera CA för Azure SQL DB/DW  
1. Logga in på portalen, Välj **Azure Active Directory**och välj sedan **villkorlig åtkomst**. Mer information finns i [teknisk referens för Azure Active Directory villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   blad](./media/sql-database-conditional-access/conditional-access-blade.png) för villkorlig åtkomst ![ 
     
2. I bladet **villkorlig åtkomst – principer** , klickar du på **ny princip**, anger ett namn och klickar sedan på **Konfigurera regler**.  
3. Under **tilldelningar**väljer **du användare och grupper**, markerar **Välj användare och grupper**och väljer sedan användaren eller gruppen för villkorlig åtkomst. Klicka på **Välj**och sedan på **Slutför** för att godkänna ditt val.  
   ![välja användare och grupper](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Välj **molnappar**och klicka på **Välj appar**. Du ser alla appar som är tillgängliga för villkorlig åtkomst. Välj **Azure SQL Database**, klicka på **Välj**längst ned på sidan och klicka sedan på **färdig**.  
   ![Välj SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Om du inte hittar **Azure SQL Database** som anges i följande tredje skärm bild, utför du följande steg:   
   - Logga in på din Azure SQL DB/DW-instans med hjälp av SSMS med ett AAD-administratörskonto.  
   - Kör `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Logga in på AAD och kontrol lera att Azure SQL Database och Azure-Synapse visas i programmen i din AAD.  

5. Välj **åtkomst kontroller**, Välj **bevilja**och kontrol lera den princip som du vill använda. I det här exemplet väljer vi **Kräv Multi-Factor Authentication**.  
   ![väljer bevilja åtkomst](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Sammanfattning  
Det valda programmet (Azure SQL Database) som gör det möjligt att ansluta till Azure SQL DB/DW med hjälp av Azure AD Premium, tillämpar nu den valda principen för villkorlig åtkomst, **krävs Multi-Factor Authentication.**  
Kontakta MFAforSQLDB@microsoft.comom du vill ha frågor om Azure SQL Database och Azure-Synapse om Multi-Factor Authentication.  

## <a name="next-steps"></a>Nästa steg  

En själv studie kurs finns i [skydda din Azure SQL Database](sql-database-security-tutorial.md).
