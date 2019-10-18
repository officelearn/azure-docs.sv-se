---
title: Data källor som stöds i Azure Analysis Services | Microsoft Docs
description: Beskriver data källor som stöds för data modeller i Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c6043d9e18c364073ad1ea46e26335577e0ec3b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512454"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Data källor som stöds i Azure Analysis Services

Data källor och anslutningar som visas i guiden hämta data eller importera i Visual Studio visas för både Azure Analysis Services och SQL Server Analysis Services. Dock stöds inte alla data källor och anslutningar som visas i Azure Analysis Services. De typer av data källor som du kan ansluta till beror på många faktorer, till exempel modellens kompatibilitetsnivå, tillgängliga data anslutningar, autentiseringstyp, providers och stöd för lokal datagateway. 

## <a name="azure-data-sources"></a>Azure-datakällor

|Data Källa  |Minnesintern  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Ja      |    Ja      |
|Azure SQL Data Warehouse     |   Ja      |   Ja       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Ja       |    Nej      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   Ja       |    Nej      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Ja        |  Nej        |
|Azure Data Lake Store (gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Ja       |    Nej      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Ja     |   Nej       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Ja       |   Nej       |
||||

<a name="tab1400a">1</a> – tabell 1400 och högre modeller.   
<a name="azsqlmanaged">2</a> -Azure SQL Database Hanterad instans stöds. Eftersom den hanterade instansen körs i Azure VNet med en privat IP-adress måste den offentliga slut punkten vara aktive rad på instansen. Om den inte är aktive rad krävs en lokal datagateway.    
<a name="databricks">3</a> -Azure Databricks med Spark-anslutningen stöds inte för närvarande.   
<a name="gen2">4</a> -ADLS Gen2 stöds inte för närvarande.


**Provider**   
InMemory-och DirectQuery-modeller som ansluter till Azure-datakällor använder .NET Framework Data Provider för SQL Server.

## <a name="other-data-sources"></a>Andra data källor

Anslutning till lokala data källor från och Azure AS Server kräver en lokal gateway. När du använder en gateway krävs 64-bitars providers.

### <a name="in-memory-and-directquery"></a>InMemory och DirectQuery

|Data Källa | InMemory-Provider | DirectQuery-Provider |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11,0, Microsoft OLE DB Provider för SQL Server .NET Framework Data Provider för SQL Server | .NET Framework data leverantör för SQL Server |
| SQL Server informations lager |SQL Server Native Client 11,0, Microsoft OLE DB Provider för SQL Server .NET Framework Data Provider för SQL Server | .NET Framework data leverantör för SQL Server |
| Oracle | OLE DB Provider för Oracle, Oracle Data Provider för .NET |Oracle Data Provider för .NET |
| Teradata |OLE DB Provider för Teradata, Teradata Data Provider för .NET |Teradata Data Provider för .NET |
| | | |

### <a name="in-memory-only"></a>Endast minnes intern

|Data Källa  |  
|---------|
|Access-databas     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analys plattforms system     |  
|CSV-fil  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel-arbetsbok     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Mapp<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (beta) |
|JSON-dokument<sup>[1](#tab1400b)</sup>     |  
|Rader från binär<sup>[1](#tab1400b)</sup>     | 
|MySQL-databas     | 
|OData-feed<sup>[1](#tab1400b)</sup>     |  
|ODBC-fråga     | 
|OLE DB     |   
|PostgreSQL databas<sup>[1](#tab1400b)</sup>    | 
|Salesforce-objekt<sup>[1](#tab1400b)</sup> |  
|Salesforce-rapporter<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint-lista<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Sybase-databas     |  
|TXT-fil  |
|XML-tabell<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> – tabell 1400 och högre modeller.   
<a name="filesSP">2</a> -filer i lokala SharePoint-nätverk stöds inte.

## <a name="specifying-a-different-provider"></a>Ange en annan provider

Data modeller i Azure Analysis Services kan kräva olika data leverantörer vid anslutning till vissa data källor. I vissa fall kan tabell modeller som ansluter till data källor med inbyggda leverantörer som SQL Server Native Client (SQLNCLI11) returnera ett fel. Om du använder andra inbyggda providers än SQLOLEDB kan du se fel meddelandet: **providern ' SQLNCLI 11.1 ' är inte registrerad**. Eller, om du har en DirectQuery-modell som ansluter till lokala data källor och använder inbyggda providers, kan du se fel meddelandet: det **gick inte att skapa OLE DB rad uppsättning. Felaktig syntax nära LIMIT**.

När du migrerar en lokal SQL Server Analysis Services tabell modell till Azure Analysis Services kan du behöva ändra providern.

**Ange en provider**

1. I SSDT > **tabell modell utforskaren** > **data källor**högerklickar du på en anslutning till en data källa och klickar sedan på **Redigera data källa**.
2. I **Redigera anslutning**klickar du på **Avancerat** för att öppna fönstret avancerade egenskaper.
3. I **Ange avancerade egenskaper** > -**providers**väljer du lämplig Provider.

## <a name="impersonation"></a>Personifiering
I vissa fall kan det vara nödvändigt att ange ett annat konto för personifiering. Personifierings konto kan anges i Visual Studio (SSDT) eller SSMS.

För lokala data Källor:

* Om du använder SQL-autentisering ska personifiering vara ett tjänst konto.
* Ange Windows-användare och lösen ord om du använder Windows-autentisering. För SQL Server stöds Windows-autentisering med ett särskilt personifierings konto endast för minnesbaserade data modeller.

För moln data Källor:

* Om du använder SQL-autentisering ska personifiering vara ett tjänst konto.

## <a name="oauth-credentials"></a>Autentiseringsuppgifter för OAuth

För tabell modeller på nivån 1400 och högre kompatibilitetsnivå Azure SQL Database, Azure SQL Data Warehouse, Dynamics 365 och SharePoint-listan stöder OAuth-autentiseringsuppgifter. Azure Analysis Services hanterar token-uppdatering för OAuth-datakällor för att undvika tids gränser för tids krävande uppdaterings åtgärder. Ange autentiseringsuppgifter genom att använda SSMS för att generera giltiga tokens.

## <a name="next-steps"></a>Nästa steg
[Lokal gateway](analysis-services-gateway.md)   
[Hantera servern](analysis-services-manage.md)   

