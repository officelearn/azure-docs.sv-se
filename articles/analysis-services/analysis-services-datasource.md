---
title: Datakällor som stöds i Azure Analysis Services | Microsoft-dokument
description: Beskriver datakällor och kopplingar som stöds för tabell 1200 och högre datamodeller i Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461665"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Datakällor som stöds i Azure Analysis Services

Datakällor och kopplingar som visas i guiden Hämta data eller tabellimport i Visual Studio med Analysis Services-projekt visas för både Azure Analysis Services och SQL Server Analysis Services. Alla datakällor och kopplingar som visas stöds dock inte i Azure Analysis Services. Vilka typer av datakällor som du kan ansluta till beror på många faktorer, till exempel modellkompatibilitetsnivå, tillgängliga dataanslutningar, autentiseringstyp och lokalt stöd för datagateway. I följande tabeller beskrivs datakällor som stöds för Azure Analysis Services.

## <a name="azure-data-sources"></a>Azure-datakällor

|Datakälla  |Minnesintern  |DirectQuery  |Anteckningar |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ja      |    Ja      |<sup>[2,](#azprovider)</sup> <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL Data Warehouse)      |   Ja      |   Ja       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Ja       |    Inga      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   Ja       |    Inga      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Ja        |  Inga        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Ja       |    Inga      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Ja       |    Inga      |<sup>[1,](#tab1400a)</sup> <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Ja     |   Inga       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Ja       |   Inga       |<sup>[1,](#tab1400a)</sup> <sup> [4](#databricks)</sup>|
||||

**Anteckningar:**   
<a name="tab1400a">1</a> - Tabell 1400 och högre modeller endast.  
<a name="azprovider">2</a> - När det anges som en *providerdatakälla* i tabellform 1200 och högre modeller kräver både modeller i minnet och DirectQuery Microsoft OLE DB Driver för SQL Server MSOLEDBSQL (rekommenderas), SQL Server Native Client 11.0 eller .NET Framework Data Provider för SQL Server.    
<a name="azsqlmanaged">3</a> - Azure SQL Database Managed Instance stöds. Eftersom hanterad instans körs i Azure VNet med en privat IP-adress måste offentlig slutpunkt aktiveras på instansen. Om den inte är aktiverad krävs en [lokal datagateway.](analysis-services-gateway.md)    
<a name="databricks">4</a> - Azure Databricks med Spark-kopplingen stöds för närvarande inte.   
<a name="gen2">5</a> - ADLS Gen2-anslutning stöds för närvarande, men Azure Blob Storage-anslutning kan användas med en ADLS Gen2-datakälla.   

## <a name="other-data-sources"></a>Andra datakällor

|Datakälla | Minnesintern | DirectQuery |Anteckningar   |
|  --- | --- | --- | --- |
|Access-databas     |  Ja | Inga |  |
|Active Directory     |  Ja | Inga | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Ja | Inga |  |
|System för analysplattform     |  Ja | Inga |  |
|CSV-fil  |Ja | Inga |  |
|Dynamics 365     |  Ja | Inga | <sup>[6](#tab1400b)</sup> |
|Excel-arbetsbok     |  Ja | Inga |  |
|Exchange      |  Ja | Inga | <sup>[6](#tab1400b)</sup> |
|Mapp      |Ja | Inga | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Ja | Inga |  |
|JSON-dokument      |  Ja | Inga | <sup>[6](#tab1400b)</sup> |
|Rader från binära      | Ja | Inga | <sup>[6](#tab1400b)</sup> |
|MySQL-databas     | Ja | Inga |  |
|OData-feed      |  Ja | Inga | <sup>[6](#tab1400b)</sup> |
|ODBC-fråga     | Ja | Inga |  |
|OLE DB     |   Ja | Inga |  |
|Oracle  | Ja  |Ja  | <sup>[9](#oracle)</sup> |
|PostgreSQL-databas   | Ja | Inga | <sup>[6](#tab1400b)</sup> |
|Salesforce-objekt|  Ja | Inga | <sup>[6](#tab1400b)</sup> |
|Salesforce-rapporter |Ja | Inga | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Ja | Inga |  |
|SAP Business Warehouse    |  Ja | Inga | <sup>[6](#tab1400b)</sup> |
|SharePoint-lista      |   Ja | Inga | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Ja   | Ja  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> | 
|SQL Server-datalager |Ja   | Ja  | <sup>[7,](#sqlim)</sup> <sup> [8](#instgw)</sup> |
|Sybase-databas     |  Ja | Inga |  |
|Teradata | Ja  | Ja  | <sup>[10](#teradata)</sup> |
|TXT-fil  |Ja | Inga |  |
|XML-tabell    |  Ja | Inga | <sup>[6](#tab1400b)</sup> |
| | | |

**Anteckningar:**   
<a name="tab1400b">6</a> - Tabell 1400 och högre modeller endast.  
<a name="sqlim">7</a> - När det anges som en *providerdatakälla* i tabellform 1200 och högre modeller anger du Microsoft OLE DB Driver för SQL Server MSOLEDBSQL (rekommenderas), SQL Server Native Client 11.0 eller .NET Framework Data Provider för SQL Server Server.  
<a name="instgw">8</a> - Om du anger MSOLEDBSQL som dataprovider kan det vara nödvändigt att hämta och installera [Microsoft OLE DB Driver för SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) på samma dator som den lokala datagatewayen.  
<a name="oracle">9</a> - För tabellform 1200-modeller, eller som *leverantörsdatakälla* i tabell 1400+ modeller, anger Oracle Data Provider för .NET.  
<a name="teradata">10</a> - För tabellform 1200-modeller, eller som *en leverantör* datakälla i tabell 1400 + modeller, ange Teradata Data Provider för .NET.   
<a name="filesSP">11</a> - Filer i lokala SharePoint stöds inte.

För anslutning till lokala datakällor från en Azure Analysis [Services-server](analysis-services-gateway.md)krävs en lokal gateway . När du använder en gateway krävs 64-bitarsleverantörer. 

## <a name="understanding-providers"></a>Förstå leverantörer

När du skapar tabellprojekt på 1400 och högre modellprojekt i Visual Studio anger du som standard inte en dataprovider när du ansluter till en datakälla med hjälp av **Hämta data**. Tabell 1400 och högre modeller använder [Power Query-kopplingar](/power-query/power-query-what-is-power-query) för att hantera anslutningar, datafrågor och kombinationer mellan datakällan och Analysis Services. Dessa kallas ibland *strukturerade* datakällanslutningar i anslutningsegenskapsinställningarna för dig. Du kan dock aktivera äldre datakällor. När det är aktiverat kan du använda **guiden Importera tabell** för att ansluta till vissa datakällor som traditionellt stöds i tabell 1200 och lägre modeller som *äldre*eller *providerdatakällor.* När det anges som en providerdatakälla kan du ange en viss dataleverantör och andra avancerade anslutningsegenskaper. Du kan till exempel ansluta till ett lokalt SQL Server Data Warehouse eller till och med en Azure SQL-databas som en äldre datakälla. Du kan sedan välja OLE DB Driver för SQL Server MSOLEDBSQL-dataprovidern. I det här fallet kan valet av OLE DB-dataprovider ge bättre prestanda via Power Query-anslutningen. 

När du använder guiden Importera tabell i Visual Studio kräver anslutningar till alla datakällor en dataprovider. En standarddataleverantör har valts åt dig. Du kan ändra dataleverantören om det behövs. Vilken typ av leverantör du väljer kan bero på prestanda, oavsett om modellen använder minneslagring eller DirectQuery, och vilken Analysis Services-plattform du distribuerar din modell till.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Ange providerdatakällor i tabellform 1400 och högre modellprojekt

Om du vill aktivera providerdatakällor klickar du på**Options** >  > **Alternativanalystjänster** > **Data Import**i Visual Studio och väljer **Aktivera äldre datakällor**. **Tools**

![Aktivera äldre datakällor](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

När äldre datakällor är aktiverade högerklickar du på **Datakällor** > **importera från datakälla (äldre)** i **Tabellmodellutforskaren**.

![Äldre datakällor i Tabellmodellutforskaren](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Precis som med tabellprojekt på 1200-modell använder du **guiden Importera tabell** för att ansluta till en datakälla. Klicka på **Avancerat**på sidan Ansluta . Ange dataprovider och andra anslutningsinställningar i **Ange avancerade egenskaper**.

![Avancerade egenskaper för äldre datakällor](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Personifiering
I vissa fall kan det vara nödvändigt att ange ett annat personifieringskonto. Personifieringskonto kan anges i Visual Studio eller SSMS.

För lokala datakällor:

* Om du använder SQL-autentisering bör personifiering vara tjänstkonto.
* Om du använder Windows-autentisering anger du Windows-användare/lösenord. För SQL Server stöds Windows-autentisering med ett specifikt personifieringskonto endast för datamodeller i minnet.

För molndatakällor:

* Om du använder SQL-autentisering bör personifiering vara tjänstkonto.

## <a name="oauth-credentials"></a>OAuth-autentiseringsuppgifter

För tabellmodeller på 1400- och högrekompatibilitetsnivå med hjälp av minnesläge stöder Azure SQL Database, Azure Synapse Analytics (SQL Data Warehouse), Dynamics 365 och SharePoint List OAuth-autentiseringsuppgifter. Azure Analysis Services hanterar tokenuppdatering för OAuth-datakällor för att undvika timeout för tidskrävande uppdateringsåtgärder. Om du vill generera giltiga token anger du autentiseringsuppgifter med hjälp av SSMS.

Direktfrågeläget stöds inte med OAuth-autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg
[Lokal gateway](analysis-services-gateway.md)   
[Hantera servern](analysis-services-manage.md)   

