---
title: Fivetran Snabbstart med Azure SQL Data Warehouse | Microsoft Docs
description: Kom igång snabbt med Fivetran och Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355539"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Kom igång snabbt med Fivetran och SQL Data Warehouse

Den här snabbstarten förutsätter att du redan har en befintlig instans av SQL Data Warehouse.

## <a name="setup-connection"></a>Konfigurera anslutning

1. Hitta det fullständigt kvalificerade servernamnet och databasnamnet för att ansluta till Azure SQL Data Warehouse.

   [Så här hittar du servernamnet och databasnamnet från portalen?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. Bestäm om du vill ansluta din databas direkt eller via en SSH-tunnel i installationsguiden.

   Om du vill ansluta direkt till databasen behöver du skapa en brandväggsregel för att tillåta åtkomst. Det här är den enklaste och säkraste metoden.

   Om du vill ansluta via en SSH-tunnel ansluter Fivetran till en annan server i nätverket som tillhandahåller en SSH-tunnel till din databas. Den här metoden är nödvändigt om databasen är i ett otillgängligt undernät i ett virtuellt nätverk.

3. Lägg till ”52.0.2.4” IP-adress i server på brandväggen att tillåta inkommande anslutningar till Azure SQL Data Warehouse från Fivetran.

   [Hur du lägger till nivån serverbrandväggen?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>Konfigurera autentiseringsuppgifter för användare

Anslut till Azure SQL Data Warehouse med hjälp av SQL Server Management Studio eller verktyg som administratörsanvändare för server och kör följande SQL-kommandon för att skapa en användare för Fivetran:

I master-databasen: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

I SQL Data Warehouse-databas:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

När användaren fivetran har skapats kan bevilja följande behörigheter till ditt informationslager:

```
GRANT CONTROL to fivetran;
```

Lägg till en lämplig resursklass till den skapade användaren, beroende på minneskravet för skapande av columnstore-index. Till exempel integreringar som Marketo och Salesforce behöver högre resursklass på grund av det stora antalet kolumner / större mängden data som kräver mer minne för att skapa columnstore-index.

Du bör använda statiska resursklasser. Du kan börja med resursklass `staticrc20`, vilket allokerar 200 MB för användare oavsett den prestandanivå som du använder. Om kolumnlagringsindexering misslyckas med den aktuella resursklassen kan behöva vi öka resursklassen.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

Mer information finns i dokumenten för [minne och samtidighet gränser](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) och [resursklasser](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)

Behörighet krävs för att skapa databasbegränsade autentiseringsuppgifter som ska användas vid inläsning av filer från Blob Storage med PolyBase.

Ange autentiseringsuppgifter för att få åtkomst till Azure SQL Data Warehouse

1. Värd (namnet på servern)
2. Port
3. Databas
4. Användare (användarnamnet ska vara `fivetran@<server_name>` där `<server_name>` är en del av din azure-värd-uri: `<server_name>.database.windows.net`)
5. Lösenord