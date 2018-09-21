---
title: Utforma din första Azure SQL-databas – C# | Microsoft Docs
description: Lär dig hur du utformar din första Azure SQL-databas och ansluter till den med ett C#-program med hjälp av ADO.NET.
services: sql-database
author: MightyPen
manager: craigg-msft
ms.reviewer: CarlRabeler
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.topic: tutorial
ms.author: genemi
ms.date: 06/07/2018
ms.openlocfilehash: 9fdc23eb2e607d1264e1bd2bc9dc85ef8d9be642
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498172"
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Utforma en Azure SQL databas och anslut med C&#x23; och ADO.NET

Azure SQL Database är en relationsdatabas-som-tjänst (DBaaS) som bygger på Microsoft Cloud (Azure). I de här självstudierna får du lära dig att använda Azure-portalen och ADO.NET med Visual Studio för att: 

> [!div class="checklist"]
> * Skapa en databas på Azure-portalen
> * Skapade en brandväggsregel på servernivå på Azure-portalen
> * Ansluta till databasen med ADO.NET och Visual Studio
> * Skapa tabeller med ADO.NET
> * Infoga, uppdatera och ta bort data med ADO.NET 
> * Fråga efter data med ADO.NET

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

En installation av [Visual Studio Community 2017, Visual Studio Professional 2017 eller Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du lärt dig grundläggande databasuppgifter, till exempel att skapa en databas och tabeller, läsa in och fråga efter data, samt återställa databasen till en tidigare tidpunkt. Du har lärt dig att:
> [!div class="checklist"]
> * Skapa en databas
> * Konfigurera en brandväggsregel
> * Ansluta till databasen med [Visual Studio och C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Skapa tabeller
> * Infoga, uppdatera och ta bort data
> * Frågedata

Gå vidare till nästa självstudie om du vill lära dig mer om hur du migrerar dina data.

> [!div class="nextstepaction"]
> [Migrera din SQL Server-databas till Azure SQL Database](sql-database-migrate-your-sql-server-database.md)

