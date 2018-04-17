---
title: Datakällor som stöds i Azure Analysis Services | Microsoft Docs
description: Beskriver de datakällor som stöds för datamodeller i Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3b60a5b96d7b8a0c48aacc916b1ba933dcd83705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Datakällor som stöds i Azure Analysis Services

Datakällor och kopplingar som visas i hämta Data eller Importguiden i Visual Studio visas för både Azure Analysis Services och SQL Server Analysis Services. Dock inte alla datakällor och kopplingar som visas stöds i Azure Analysis Services. Typer av datakällor som du kan ansluta till beror på många faktorer som modellen kompatibilitetsnivå, tillgängliga data kopplingar, autentiseringstyp, leverantörer och stöd för lokala data gateway. 

## <a name="azure-data-sources"></a>Azure-datakällor

|Datakälla  |Minnesintern  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   Ja      |    Ja      |
|Azure SQL Data Warehouse     |   Ja      |   Ja       |
|Azure Blob Storage*     |   Ja       |    Nej      |
|Azure Table Storage *    |   Ja       |    Nej      |
|Azure Cosmos DB *     |  Ja        |  Nej        |
|Azure Data Lake Store *     |   Ja       |    Nej      |
|Azure HDInsight HDFS *     |     Ja     |   Nej       |
|Azure HDInsight Spark *     |   Ja       |   Nej       |
||||

\* 1400 tabellmodeller endast.

**Providern**   
Använd .NET Framework Data Provider för SQL Server i minnet och DirectQuery-modeller som ansluter till Azure-datakällor.

## <a name="on-premises-data-sources"></a>Lokala datakällor

Ansluter till lokala datakällor från och Azure-AS-servern kräver en lokal gateway. När du använder en gateway, krävs en 64-bitars providers.

### <a name="in-memory-and-directquery"></a>I minnet och DirectQuery

|Datakälla | InMemory-provider | DirectQuery-provider |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 Microsoft OLE DB Provider för SQLServer, .NET Framework dataprovider för SQLServer | .NET framework dataprovider för SQLServer |
| SQL Server Data Warehouse |SQL Server Native Client 11.0 Microsoft OLE DB Provider för SQLServer, .NET Framework dataprovider för SQLServer | .NET framework dataprovider för SQLServer |
| Oracle |Microsoft OLE DB Provider för Oracle, Oracle dataprovider för .NET |Oracle dataprovider för .NET | |
| Teradata |OLE DB Provider för Teradata Teradata-dataprovidern för .NET |Teradata-dataprovidern för .NET | |
| | | |

### <a name="in-memory-only"></a>I minnet enbart

|Datakälla  |  
|---------|---------|
|Access-databas     |  
|Active Directory *     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Dynamics CRM *     |  
|Excel-arbetsbok     |  
|Exchange*     |  
|Mappen *     | 
|JSON-dokumentet *     |  
|Rader från binary *     | 
|MySQL-databas     | 
|OData-Feed *     |  
|ODBC-fråga     | 
|OLE DB     |   
|Postgre SQL-databasen *    | 
|SAP HANA *    |  
|SAP Business Warehouse *    |  
|SharePoint*     |   
|Sybase-databas     |  
|XML-tabellen *    |  
|||
 
\* 1400 tabellmodeller endast.

## <a name="specifying-a-different-provider"></a>Ange en annan provider

Datamodeller i Azure Analysis Services kan kräva olika dataleverantörer vid anslutning till vissa datakällor. I vissa fall kan tabellmodeller som ansluter till datakällor med hjälp av inbyggda providrar, till exempel SQL Server Native Client (SQLNCLI11) returnerar ett fel. Om du använder inbyggda providers än SQLOLEDB, kanske du ser felmeddelandet: **providern 'SQLNCLI11.1' inte är registrerad**. Om du har en DirectQuery-modell som ansluter till lokala datakällor och du använder inbyggda providers, kan du läsa felmeddelande: **fel OLE DB raden skapades. Felaktig syntax nära 'LIMIT'**.

När du migrerar en lokal SQL Server Analysis Services-tabellmodell till Azure Analysis Services, kan det vara nödvändigt att ändra providern.

**Ange en provider**

1. I SSDT > **Tabular modellen Explorer** > **datakällor**, högerklicka på anslutning till en datakälla och klicka sedan på **redigera datakällan**.
2. I **Redigera anslutning**, klickar du på **Avancerat** att öppna fönstret Avancerade egenskaper.
3. I **avancerade egenskaper för** > **Providers**, Välj lämplig provider.

## <a name="impersonation"></a>Personifiering
I vissa fall kan vara det nödvändigt att ange en annan personifieringskontot. Personifieringskontot kan anges i Visual Studio (SSDT) eller SSMS.

För lokala datakällor:

* Om du använder SQL-autentisering vara personifiering tjänstkontot.
* Ange Windows användarlösenord om du använder Windows-autentisering. Windows-autentisering med en specifik personifieringskontot stöds endast för datamodeller i minnet för SQL Server.

För molnet datakällor:

* Om du använder SQL-autentisering vara personifiering tjänstkontot.

## <a name="next-steps"></a>Nästa steg
[Lokal gateway](analysis-services-gateway.md)   
[Hantera servern](analysis-services-manage.md)   

