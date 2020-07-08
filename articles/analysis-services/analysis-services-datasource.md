---
title: Data källor som stöds i Azure Analysis Services | Microsoft Docs
description: Beskriver data källor och anslutningar som stöds för tabell 1200 och högre data modeller i Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc25c853a37de5c310d37e7ee64c6f762283cb0a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077447"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Datakällor som stöds i Azure Analysis Services

Data källor och anslutningar som visas i guiden hämta data eller tabell importera i Visual Studio med Analysis Services projekt visas för både Azure Analysis Services och SQL Server Analysis Services. Dock stöds inte alla data källor och anslutningar som visas i Azure Analysis Services. De typer av data källor som du kan ansluta till beror på många faktorer, till exempel modellens kompatibilitetsnivå, tillgängliga data anslutningar, autentiseringstyp och stöd för lokal datagateway. I följande tabeller beskrivs data källor som stöds för Azure Analysis Services.

## <a name="azure-data-sources"></a>Azure-datakällor

|Datakälla  |Minnesintern  |DirectQuery  |Kommentarer |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ja      |    Yes      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL DW)      |   Ja      |   Yes       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Yes       |    Nej      | <sup>[81.1](#tab1400a)</sup> |
|Azure Table Storage     |   Yes       |    Nej      | <sup>[81.1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Yes        |  Nej        |<sup>[81.1](#tab1400a)</sup> |
|Azure Data Lake Store gen1      |   Yes       |    Nej      |<sup>[81.1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Yes       |    Nej      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Yes     |   Nej       |<sup>[81.1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Yes       |   Nej       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Anteckningar:**

<a name="tab1400a">1</a> – tabell 1400 och högre modeller.  
<a name="azprovider">2</a> – när det har angetts som en *Provider* -datakälla i tabell 1200 och högre modeller, kräver både InMemory-och DirectQuery-modeller Microsoft OLE DB driv rutin för SQL Server MSOLEDBSQL (rekommenderas), SQL Server Native Client 11,0 eller .NET Framework Data Provider för SQL Server.  
<a name="azsqlmanaged">3</a> – Azure SQL-hanterad instans stöds. Eftersom SQL-hanterad instans körs i Azure VNet med en privat IP-adress måste den offentliga slut punkten vara aktive rad på instansen. Om den inte är aktive rad krävs en [lokal datagateway](analysis-services-gateway.md) .  
<a name="databricks">4</a> -Azure Databricks med Spark-anslutningen stöds inte för närvarande.  
<a name="gen2">5</a> -ADLS Gen2 anslutningen stöds inte för närvarande, men Azure Blob Storage Connector kan användas med en ADLS Gen2 data källa.

## <a name="other-data-sources"></a>Andra datakällor

|Datakälla | Minnesintern | DirectQuery |Kommentarer   |
|  --- | --- | --- | --- |
|Access-databas     |  Yes | Nej |  |
|Active Directory     |  Yes | Nej | <sup>[3-6](#tab1400b)</sup>  |
|Analysis Services     |  Yes | Nej |  |
|Analys plattforms system     |  Yes | Nej |  |
|CSV-fil  |Yes | Nej |  |
|Dynamics 365     |  Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|Excel-arbetsbok     |  Yes | Nej |  |
|Exchange      |  Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|Mapp      |Ja | Nej | <sup>[3-6](#tab1400b)</sup> |
|IBM Informix  |Yes | Nej |  |
|JSON-dokument      |  Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|Rader från binär      | Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|MySQL-databas     | Yes | Nej |  |
|OData-feed      |  Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|ODBC-fråga     | Yes | Nej |  |
|OLE DB     |   Yes | Nej |  |
|Oracle  | Ja  |Yes  | <sup>[9](#oracle)</sup> |
|PostgreSQL-databas   | Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|Salesforce-objekt|  Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|Salesforce-rapporter |Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|SAP HANA     |  Yes | Nej |  |
|SAP Business Warehouse    |  Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
|SharePoint-lista      |   Yes | Nej | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |Ja   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|SQL Server informations lager |Ja   | Yes  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Sybase-databas     |  Yes | Nej |  |
|Teradata | Ja  | Yes  | <sup>[10](#teradata)</sup> |
|TXT-fil  |Yes | Nej |  |
|XML-tabell    |  Yes | Nej | <sup>[3-6](#tab1400b)</sup> |
| | | |

**Anteckningar:**  
<a name="tab1400b">6</a> -tabell 1400 och högre modeller.  
<a name="sqlim">7</a> – när du har angett som en *Provider* -datakälla i tabell 1200 och högre modeller, anger du Microsoft OLE DB driv rutin för SQL Server MSOLEDBSQL (rekommenderas), SQL Server Native Client 11,0 eller .NET Framework Data Provider för SQL Server.  
<a name="instgw">8</a> – om du anger MSOLEDBSQL som dataprovider kan det vara nödvändigt att ladda ned och installera [Microsoft OLE DB-drivrutinen för SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) på samma dator som den lokala datagatewayen.  
<a name="oracle">9</a> – för tabell 1200-modeller, eller som en *Provider* -datakälla i tabell 1400 +-modeller, anger du Oracle Data Provider för .net.  
<a name="teradata">10</a> – för tabell 1200-modeller, eller som en *Provider* -datakälla i tabell 1400 +-modeller, anger du Teradata-dataprovider för .net.  
<a name="filesSP">11</a> -filer i lokala SharePoint-versioner stöds inte.

Anslutning till lokala data källor från en Azure Analysis Services server kräver en [lokal gateway](analysis-services-gateway.md). När du använder en gateway krävs 64-bitars providers.

## <a name="understanding-providers"></a>Förstå leverantörer

När du skapar tabell 1400 och högre modell projekt i Visual Studio, anger du som standard ingen dataprovider vid anslutning till en data källa med hjälp av **Hämta data**. I tabell 1400 och högre modeller används [Power Query](/power-query/power-query-what-is-power-query) -anslutningar för att hantera anslutningar, data frågor och kombinations program mellan data källan och Analysis Services. Dessa kallas ibland för *strukturerade* data käll anslutningar i dessa inställningar för anslutnings egenskaper. Du kan dock aktivera äldre data källor för ett modell projekt i Visual Studio. När den är aktive rad kan du använda **guiden Importera tabell** för att ansluta till vissa data källor som traditionellt stöds i tabell 1200 och lägre modeller som *äldre*eller data källor för *Provider* . När du har angett som en data källa för en provider kan du ange en viss data leverantör och andra avancerade anslutnings egenskaper. Du kan till exempel ansluta till en SQL Server informations lager instans eller till och med en Azure SQL Database som en äldre data källa. Du kan sedan välja OLE DB driv rutinen för SQL Server MSOLEDBSQL Data Provider. I det här fallet kan det vara bättre att välja en OLE DB dataprovider över Power Query anslutningen. 

När du använder guiden Importera tabell i Visual Studio kräver anslutningarna till en data källa en data leverantör. En standardprovider för data har valts. Du kan ändra data leverantören om det behövs. Vilken typ av provider du väljer kan bero på prestanda, om modellen använder minnes lagring eller DirectQuery, och vilken Analysis Services plattform som du distribuerar din modell till.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Ange providerns data källor i tabell 1400 och högre modell projekt

Om du vill aktivera providerns data källor går du till Visual Studio, klickar på **verktyg**  >  **alternativ**  >  **Analysis Services tabell**  >  **data import**, väljer **Aktivera äldre data källor**.

![Aktivera äldre data källor](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

När äldre data källor är aktiverade i **tabell modell Utforskaren**, högerklickar du på **data källor**  >  **Importera från data källa (bakåtkompatibelt)**.

![Äldre data källor i tabell modell Utforskaren](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Precis som med tabell 1200 modell projekt kan du använda **tabell import guiden** för att ansluta till en data källa. På sidan Anslut klickar du på **Avancerat**. Ange data leverantör och andra anslutnings inställningar i **Ange avancerade egenskaper**.

![Avancerade egenskaper för äldre data källor](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Personifiering
I vissa fall kan det vara nödvändigt att ange ett annat konto för personifiering. Personifierings konto kan anges i Visual Studio eller SQL Server Management Studio (SSMS).

För lokala data Källor:

* Om du använder SQL-autentisering ska personifiering vara ett tjänst konto.
* Ange Windows-användare och lösen ord om du använder Windows-autentisering. För SQL Server stöds Windows-autentisering med ett särskilt personifierings konto endast för minnesbaserade data modeller.

För moln data Källor:

* Om du använder SQL-autentisering ska personifiering vara ett tjänst konto.

## <a name="oauth-credentials"></a>Autentiseringsuppgifter för OAuth

För tabell modeller på nivån 1400 och högre med i-minnes läge, Azure SQL Database, Azure-Synapse (tidigare SQL Data Warehouse), Dynamics 365 och SharePoint-lista stöder OAuth-autentiseringsuppgifter. Azure Analysis Services hanterar token-uppdatering för OAuth-datakällor för att undvika tids gränser för tids krävande uppdaterings åtgärder. Ange autentiseringsuppgifter genom att använda SSMS för att generera giltiga tokens.

Direct Query-läget stöds inte med OAuth-autentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

* [Lokal gateway](analysis-services-gateway.md)
* [Hantera servern](analysis-services-manage.md)
