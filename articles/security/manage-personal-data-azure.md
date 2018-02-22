---
title: Hantera personliga data i Microsoft Azure | Microsoft Docs
description: anvisningar om hur du korrigera, uppdatera, ta bort och exportera personliga data i Azure Active Directory och Azure SQL Database
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 51dca8955745b40a9126b142ea15e707fe58bc72
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="manage-personal-data-in-microsoft-azure"></a>Hantera personliga data i Microsoft Azure

Den här artikeln innehåller råd om hur du korrigera, uppdatera, ta bort och exportera personliga data i Azure Active Directory och Azure SQL Database.

## <a name="scenario"></a>Scenario

En Dublin-baserade företaget tillhandahåller en arbetsuppgift för avancerad mål bröllop i Irland och hela världen för både en lokal och kundbas. De har kontor, kunder, anställda och leverantörer finns runtom i världen fullständigt tjänsten handelsplatser de erbjuder.

Företaget håller reda på svar som inkluderar Matallergier och dietetisk inställningar mellan många andra objekt. Bröllop gäster kan registrera för olika aktiviteter som horseback barnet, surfa, beredskapsbåten bilar, osv., och även interagera med varandra på en central webbsida under de månader som ledde till händelsen. Företaget samlar in personlig information från anställda, leverantörer, kunder och bröllop gäster. Företaget måste överensstämma med flera nivåer av förordning på grund av den internationella typ av verksamhet.

## <a name="problem-statement"></a>Problembeskrivning

- Data-administratörer måste kunna rätt stämmer och uppdatera ofullständiga eller ändra personlig information.

- Data-administratörer måste kunna ta bort personlig information på begäran för en registrerade.

- Data-administratörer måste du exportera data och förse en registrerade i ett vanligt, strukturerade format på sin begäran.

## <a name="company-goals"></a>Företagets mål

- Felaktiga eller ofullständiga kund, Bröllop gäst, medarbetare och personlig information om leverantören måste korrigeras eller uppdateras i Azure Active Directory och Azure SQL Database.

- Personlig information måste tas bort i Azure Active Directory och Azure SQL Database på begäran för en registrerade.

- Personliga data måste exporteras i ett vanligt, strukturerade format på begäran för en registrerade.

## <a name="solutions"></a>Lösningar

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory: åtgärda/korrigera felaktiga eller ofullständiga personliga data och radera/ta bort personliga data/användarprofiler

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) är Microsofts molnbaserade, flera innehavare katalog och identity management-tjänsten.
Du kan korrigera, uppdatera eller ta bort kund- och användarprofiler och arbete användarinformation som innehåller dina personliga data, till exempel en användares namn, arbete rubrik, adress eller telefonnummer i din [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD)-miljö med hjälp av den [Azure-portalen](https://portal.azure.com/).

Du måste logga in med ett konto som är en global administratör för katalogen.

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>Hur jag korrigera eller uppdatera användarprofil och arbete information i Azure Active Directory?

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.

2. Välj **alla tjänster**, ange **användare och grupper** i textrutan och välj sedan **RETUR**.

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. På den **användare och grupper** bladet väljer **användare**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. På den **användare och grupper – användare** bladet Välj en användare i listan och i bladet för den valda användaren kan markera **profil** visa profilinformation för användare som behöver rättas eller uppdateras.

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. Korrigera eller uppdatera informationen och markera i kommandofältet **spara.**

6.  På bladet för den valda användaren väljer **fungerar Info** att visa användarinformation för arbete som behöver rättas eller uppdateras.

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. Korrigera eller uppdatera användarinformation för arbete och markera i kommandofältet **spara.**

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>Hur tar jag bort en användarprofil i Azure Active Directory?

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.

2. Välj **alla tjänster**, ange **användare och grupper** i textrutan och välj sedan **RETUR**.

    ![](media/manage-personal-data-azure/image001.png)

3. På den **användare och grupper** bladet väljer **användare**.

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. På bladet **Användare och grupper – användare** väljer du en användare i listan.

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. På bladet för den valda användaren väljer **översikt**, och välj sedan i kommandofältet **ta bort**.

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL Database: åtgärda/korrigera felaktiga eller ofullständiga personuppgifter. Radera/ta bort personuppgifter. Exportera personliga data 

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) är en databas i molnet som gör att utvecklare kan skapa och hantera program.

Personliga data uppdateras i [Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) med standard SQL-frågor och kan också tas bort. Dessutom kan personuppgifter exporteras från SQL-databas med hjälp av olika metoder, inklusive Azure SQL Server importera och exportera guiden och i olika format, inklusive en BACPAC-fil.

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>Hur jag korrigera, uppdatera eller ta bort personliga data i SQL-databas?

Ta reda på hur du korrigera eller uppdatera personliga data i SQL-databas på den [uppdatering (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql), [uppdatera Text](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql), [uppdatera med vanliga tabelluttryck](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql), eller [uppdatera skriva Text](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql) dokumentation.

Ta reda på hur du tar bort personliga data i SQL-databas på den [ta bort (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) dokumentation.

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>Hur exporterar personliga data till en BACPAC-fil i SQL-databas?

En BACPAC-filen innehåller data från SQL-databasen och metadata och en zip-fil med filnamnstillägget BACPAC. Detta kan göras med hjälp av den [Azure-portalen](https://portal.azure.com/), SQLPackage kommandoradsverktyg, SQL Server Management Studio (SSMS) eller PowerShell.

Ta reda på hur du exporterar data till en BACPAC-fil på den [exportera en Azure SQL database till en BACPAC fil](https://docs.microsoft.com/azure/sql-database/sql-database-export) som innehåller detaljerade anvisningar för varje metod som anges ovan.

Hur exporterar personliga data från SQL-databas med SQL Server importera och exportera guiden?

Den här guiden hjälper dig att kopiera data från en källa till ett mål. En introduktion till guiden, inklusive hur du hämta den, behörighetsinformation och hur du få hjälp med verktyget finns på [importera och exportera Data med SQL Server importera och exportera guiden](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard) webbsida.

En översikt över stegen för att guiden finns i [stegen i guiden SQL Server importera och exportera](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard) webbsida.

## <a name="next-steps"></a>Nästa steg:

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

