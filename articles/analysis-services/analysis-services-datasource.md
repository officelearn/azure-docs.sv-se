---
title: Datakällor som stöds i Azure Analysis Services | Microsoft Docs
description: Beskriver datakällor som stöds för datamodeller i Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6ad95eb45cc208fe2289cb2095214f98a0b250b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442397"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Datakällor som stöds i Azure Analysis Services

Datakällor och anslutningsappar som visas i hämta Data eller Importguiden i Visual Studio visas för både Azure Analysis Services och SQL Server Analysis Services. Men inte alla datakällor och anslutningsappar som visas stöds i Azure Analysis Services. Typer av datakällor som du kan ansluta beror på många faktorer som modell kompatibilitetsnivå, tillgängliga dataanslutningar, autentiseringstyp, leverantörer och stöd för lokala data gateway. 

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

\* 1400-tabellmodeller endast.

**Providern**   
InMemory- och DirectQuery-modeller som ansluter till Azure-datakällor använder du .NET Framework Data Provider för SQL Server.

## <a name="on-premises-data-sources"></a>Lokala datakällor

Ansluta till lokala datakällor från och Azure-AS-servern kräver en lokal gateway. När du använder en gateway, krävs en 64-bitars providers.

### <a name="in-memory-and-directquery"></a>InMemory- och DirectQuery

|Datakälla | InMemory-provider | DirectQuery-providern |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 Microsoft OLE DB Provider för SQLServer, .NET Framework-dataprovidern för SQLServer | .NET framework-dataprovidern för SQLServer |
| SQL Server Data Warehouse |SQL Server Native Client 11.0 Microsoft OLE DB Provider för SQLServer, .NET Framework-dataprovidern för SQLServer | .NET framework-dataprovidern för SQLServer |
| Oracle |Microsoft OLE DB Provider för Oracle, Oracle dataprovider för .NET |Oracle dataprovider för .NET | |
| Teradata |OLE DB Provider för Teradata och Teradata Data Provider för .NET |Teradata Data Provider för .NET | |
| | | |

### <a name="in-memory-only"></a>InMemory-endast

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
|IBM Informix * (Beta) |
|JSON-dokument *     |  
|Rader från binär *     | 
|MySQL-databas     | 
|OData-Feed *     |  
|ODBC-fråga     | 
|OLE DB     |   
|Postgre SQL-databas *    | 
|Salesforce-objekt * |  
|Salesforce-rapporter * |
|SAP HANA *    |  
|SAP Business Warehouse *    |  
|SharePoint*     |   
|Sybase-databas     |  
|XML-tabellen *    |  
|||
 
\* 1400-tabellmodeller endast.

## <a name="specifying-a-different-provider"></a>Ange en annan leverantör

Datamodeller i Azure Analysis Services kan kräva olika dataleverantörer när du ansluter till vissa datakällor. I vissa fall kan tabellmodeller som ansluter till datakällor med hjälp av inbyggda providers, till exempel SQL Server Native Client (SQLNCLI11) returnerar ett fel. Om du använder inbyggda providers än SQLOLEDB, kan du se felmeddelandet: **providern 'SQLNCLI11.1' inte är registrerad**. Eller, om du har en DirectQuery-modell ansluter till lokala datakällor och du använder inbyggda providers, kanske du ser felmeddelandet: **fel vid skapande av OLE DB rad set. Felaktig syntax nära 'LIMIT'**.

När du migrerar en lokal SQL Server Analysis Services-tabellmodell till Azure Analysis Services, kan det vara nödvändigt att ändra providern.

**Ange en provider**

1. I SSDT > **Tabellmodellutforskaren** > **datakällor**, högerklicka på anslutning till en datakälla och klicka sedan på **redigera datakällan**.
2. I **Redigera anslutning**, klickar du på **Avancerat** att öppna fönstret Avancerade egenskaper.
3. I **ange avancerade egenskaper** > **Providers**, välj sedan lämplig leverantör.

## <a name="impersonation"></a>Personifiering
I vissa fall kan det vara nödvändigt att ange en annan personifiering. Personifieringskontot kan anges i Visual Studio (SSDT) eller SSMS.

För lokala datakällor:

* Om du använder SQL-autentisering ska personifiering tjänstkonto.
* Om du använder Windows-autentisering, ange Windows användare/lösenord. För SQL Server stöds Windows-autentisering med en specifik personifieringskontot endast för InMemory-datamodeller.

För cloud-datakällor:

* Om du använder SQL-autentisering ska personifiering tjänstkonto.

## <a name="next-steps"></a>Nästa steg
[En lokal gateway](analysis-services-gateway.md)   
[Hantera servern](analysis-services-manage.md)   

