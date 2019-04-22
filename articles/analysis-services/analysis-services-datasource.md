---
title: Datakällor som stöds i Azure Analysis Services | Microsoft Docs
description: Beskriver datakällor som stöds för datamodeller i Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ed18fd985173a4d978edccedc8b6e5cf527f16dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698740"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Datakällor som stöds i Azure Analysis Services

Datakällor och anslutningsappar som visas i hämta Data eller Importguiden i Visual Studio visas för både Azure Analysis Services och SQL Server Analysis Services. Men inte alla datakällor och anslutningsappar som visas stöds i Azure Analysis Services. Typer av datakällor som du kan ansluta beror på många faktorer som modell kompatibilitetsnivå, tillgängliga dataanslutningar, autentiseringstyp, leverantörer och stöd för lokala data gateway. 

## <a name="azure-data-sources"></a>Azure-datakällor

|Datakälla  |Minnesintern  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Ja      |    Ja      |
|Azure SQL Data Warehouse     |   Ja      |   Ja       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Ja       |    Nej      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   Ja       |    Nej      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Ja        |  Nej        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Ja       |    Nej      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Ja     |   Nej       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Ja       |   Nej       |
||||

<a name="tab1400a">1</a> -tabell 1400 och högre modeller.   
<a name="azsqlmanaged">2</a> – azure SQL Database Managed Instance stöds. Eftersom en hanterad instans körs i Azure virtuellt nätverk med en privat IP-adress, krävs en lokal Datagateway.   
<a name="databricks">3</a> – azure Databricks med Spark connector inte stöds för tillfället.   
<a name="gen2">4</a> -ADLS Gen2 stöds inte för tillfället.


**Providern**   
InMemory- och DirectQuery-modeller som ansluter till Azure-datakällor använder du .NET Framework Data Provider för SQL Server.

## <a name="on-premises-data-sources"></a>Lokala datakällor

Ansluta till lokala datakällor från och Azure-AS-servern kräver en lokal gateway. När du använder en gateway, krävs en 64-bitars providers.

### <a name="in-memory-and-directquery"></a>InMemory- och DirectQuery

|Datakälla | InMemory-provider | DirectQuery-providern |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 Microsoft OLE DB Provider för SQLServer, .NET Framework-dataprovidern för SQLServer | .NET framework-dataprovidern för SQLServer |
| SQL Server Data Warehouse |SQL Server Native Client 11.0 Microsoft OLE DB Provider för SQLServer, .NET Framework-dataprovidern för SQLServer | .NET framework-dataprovidern för SQLServer |
| Oracle |Microsoft OLE DB Provider för Oracle, Oracle dataprovider för .NET |Oracle dataprovider för .NET |
| Teradata |OLE DB Provider för Teradata och Teradata Data Provider för .NET |Teradata Data Provider för .NET |
| | | |

### <a name="in-memory-only"></a>InMemory-endast

|Datakälla  |  
|---------|
|Access-databas     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analytics Platform System     |  
|CSV-fil  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel-arbetsbok     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Folder<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b) </sup> (Beta) |
|JSON-dokumentet<sup>[1](#tab1400b)</sup>     |  
|Rader från binär<sup>[1](#tab1400b)</sup>     | 
|MySQL-databas     | 
|OData Feed<sup>[1](#tab1400b)</sup>     |  
|ODBC-fråga     | 
|OLE DB     |   
|Postgre SQL Database<sup>[1](#tab1400b)</sup>    | 
|Salesforce Objects<sup>[1](#tab1400b)</sup> |  
|Salesforce-rapporter<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint-lista<sup>[1](#tab1400b)</sup>, <sup> [2](#filesSP)</sup>     |   
|Sybase-databas     |  
|TXT-fil  |
|XML-tabeller<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> -tabell 1400 och högre modeller.   
<a name="filesSP">2</a> -filer i en lokal SharePoint stöds inte.

## <a name="specifying-a-different-provider"></a>Ange en annan leverantör

Datamodeller i Azure Analysis Services kan kräva olika dataleverantörer när du ansluter till vissa datakällor. I vissa fall kan tabellmodeller som ansluter till datakällor med hjälp av inbyggda providers, till exempel SQL Server Native Client (SQLNCLI11) returnerar ett fel. Om du använder inbyggda providers än SQLOLEDB, kan det hända att felmeddelandet: **Providern 'SQLNCLI11.1' inte är registrerad**. Eller, om du har en DirectQuery-modell ansluter till lokala datakällor och du använder inbyggda providers, kanske du ser felmeddelandet: **Fel vid skapande av OLE DB-raden ange. Felaktig syntax nära 'LIMIT'**.

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

