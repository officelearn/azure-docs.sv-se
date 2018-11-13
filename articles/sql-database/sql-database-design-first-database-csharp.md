---
title: Utforma din första Azure SQL-databas – C# | Microsoft Docs
description: Lär dig hur du utformar din första Azure SQL-databas och ansluter till den med ett C#-program med hjälp av ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 11/01/2018
ms.openlocfilehash: 82cf0303019d2cbb620c442fd6f750f733930f84
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50912347"
---
# <a name="tutorial-design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Självstudie: Utforma en Azure SQL databas och anslut med C&#x23; och ADO.NET

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
> * Söka i data

Gå vidare till nästa självstudie om du vill lära dig mer om hur du migrerar dina data.

> [!div class="nextstepaction"]
> [Migrera din SQL Server-databas till Azure SQL Database](sql-database-migrate-your-sql-server-database.md)
