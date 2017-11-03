---
title: "Utforma din första Azure SQL database - C# | Microsoft Docs"
description: "Lär dig att utforma din första Azure SQL-databas och ansluta till den med ett C#-program med hjälp av ADO.NET."
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg-msft
editor: CarlRabeler
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 08/25/2017
ms.author: genemi
ms.openlocfilehash: 7d6c1ca0f48974f8a7d839b23f71d43bfeb400ba
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Utforma en Azure SQL database och ansluta med C & #x23; och ADO.NET

Azure SQL Database är en relationell databas-som-en tjänst (DBaaS) i Microsoft Cloud (”Azure”). I kursen får du lära dig hur du använder Azure portal och ADO.NET med Visual Studio för att: 

> [!div class="checklist"]
> * Skapa en databas på Azure-portalen
> * Konfigurera en brandväggsregel på servernivå i Azure-portalen
> * Ansluta till databasen med ADO.NET och Visual Studio
> * Skapa tabeller med ADO.NET
> * Infoga, uppdatera och ta bort data med ADO.NET 
> * Fråga efter data ADO.NET

Om du inte har en Azure-prenumeration [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

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

I kursen får du lärt dig grundläggande uppgifter som skapar en databas och tabeller, läsa in fråga efter data och återställa databasen till en tidigare tidpunkt. Du har lärt dig att:
> [!div class="checklist"]
> * Skapa en databas
> * Konfigurera en brandväggsregel
> * Ansluta till databasen med [Visual Studio och C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Skapa tabeller
> * Infoga, uppdatera och ta bort data
> * Frågedata

Gå vidare till nästa kurs att lära dig om att migrera dina data.

> [!div class="nextstepaction"]
>[Migrera din SQL Server-databas till Azure SQL Database](sql-database-migrate-your-sql-server-database.md)

