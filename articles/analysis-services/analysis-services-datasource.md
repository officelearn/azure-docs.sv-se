---
title: "Datakällor som stöds i Azure Analysis Services | Microsoft Docs"
description: "Beskriver de datakällor som stöds för datamodeller i Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 33115ee35670407c3b046f70a5fbebc47284b4b9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Datakällor som stöds i Azure Analysis Services
Azure Analysis Services-servrar som stöder anslutning till datakällor i molnet och lokalt i din organisation. Ytterligare datakällor läggs hela tiden. Kom tillbaka ofta. 

Följande datakällor stöds:

| Molnet  |
|---|
| Azure Blob Storage*  |
| Azure SQL Database  |
| Azure Data Warehouse |


| Lokal  |   |   |   |
|---|---|---|---|
| Access-databas  | Mappen * | Oracle-databas  | Teradata-databas |
| Active Directory *  | JSON-dokumentet *  | Postgre SQL-databasen *  |XML-tabellen * |
| Analysis Services  | Rader från binary *  | SAP HANA *  |
| Analytics Platform System  | MySQL-databas  | SAP Business Warehouse*  | |
| Dynamics CRM*  | OData Feed*  | SharePoint*  |
| Excel-arbetsbok  | ODBC-fråga  | SQL Database  |
| Exchange*  | OLE DB  | Sybase-databas  |

\* 1400 tabellmodeller endast. 

> [!IMPORTANT]
> Ansluter till lokala datakällor kräver en [lokala datagateway](analysis-services-gateway.md) installeras på en dator i din miljö.

## <a name="data-providers"></a>Dataleverantörer

Datamodeller i Azure Analysis Services kan kräva olika dataleverantörer vid anslutning till vissa datakällor. I vissa fall kan tabellmodeller som ansluter till datakällor med hjälp av inbyggda providrar, till exempel SQL Server Native Client (SQLNCLI11) returnerar ett fel.

För datamodeller som ansluter till en molndata källa, till exempel Azure SQL Database, om du använder inbyggda providers än SQLOLEDB kanske du ser felmeddelandet: **”providern 'SQLNCLI11.1' inte är registrerad”.** Om du har en DirectQuery-modell som ansluter till lokala datakällor, om du använder inbyggda providrar kan du läsa felmeddelande: **”Error OLE DB raden skapades. Felaktig syntax nära 'LIMIT' ”**.

Följande datasource-providers stöds för i minnet eller DirectQuery-datamodeller när du ansluter till datakällor i molnet eller lokalt:

### <a name="cloud"></a>Molnet
| **DataSource** | **Minnesintern** | **DirectQuery** |
|  --- | --- | --- |
| Azure SQL Data Warehouse |.NET framework dataprovider för SQLServer |.NET framework dataprovider för SQLServer |
| Azure SQL Database |.NET framework dataprovider för SQLServer |.NET framework dataprovider för SQLServer | |

### <a name="on-premises-via-gateway"></a>Lokalt (via Gateway)
|**DataSource** | **Minnesintern** | **DirectQuery** |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |.NET framework dataprovider för SQLServer |
| SQL Server |Microsoft OLE DB Provider för SQLServer |.NET framework dataprovider för SQLServer | |
| SQL Server |.NET framework dataprovider för SQLServer |.NET framework dataprovider för SQLServer | |
| Oracle |Microsoft OLE DB Provider för Oracle |Oracle dataprovider för .NET | |
| Oracle |Oracle dataprovider för .NET |Oracle dataprovider för .NET | |
| Teradata |OLE DB Provider för Teradata |Teradata-dataprovidern för .NET | |
| Teradata |Teradata-dataprovidern för .NET |Teradata-dataprovidern för .NET | |
| Analytics Platform System |.NET framework dataprovider för SQLServer |.NET framework dataprovider för SQLServer | |

> [!NOTE]
> Kontrollera 64-bitars providrar är installerade när du använder lokala gateway.
> 
> 

När du migrerar en lokal SQL Server Analysis Services-tabellmodell till Azure Analysis Services, kan det vara nödvändigt att ändra providern.

**Ange en datasource-provider**

1. I SSDT > **Tabular modellen Explorer** > **datakällor**, högerklicka på anslutning till en datakälla och klicka sedan på **redigera datakällan**.
2. I **Redigera anslutning**, klickar du på **Avancerat** att öppna fönstret Avancerade egenskaper.
3. I **avancerade egenskaper för** > **Providers**, Välj lämplig provider.

## <a name="impersonation"></a>Personifiering
I vissa fall kan vara det nödvändigt att ange en annan personifieringskontot. Personifieringskontot kan anges i SSDT eller SSMS.

För lokala datakällor:

* Om du använder SQL-autentisering vara personifiering tjänstkontot.
* Ange Windows användarlösenord om du använder Windows-autentisering. Windows-autentisering med en specifik personifieringskontot stöds endast för datamodeller i minnet för SQL Server.

För molnet datakällor:

* Om du använder SQL-autentisering vara personifiering tjänstkontot.

## <a name="next-steps"></a>Nästa steg
Om du har lokala datakällor, måste du installera den [lokala gateway](analysis-services-gateway.md).   
Mer information om hur du hanterar servern i SSDT eller SSMS finns [hantera servern](analysis-services-manage.md).

