---
title: Säkerhetsöverväganden
description: Beskriver grundläggande säkerhetsinfrastruktur som dataflyttningstjänster i Azure Data Factory använder för att skydda dina data.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: bb3f22223bd64c06cfa4a5f6ffabe7b128dff1d5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416470"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Säkerhetsöverväganden för dataflyttning i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuell version](data-movement-security-considerations.md)

 [!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs grundläggande säkerhetsinfrastruktur som dataflyttningstjänster i Azure Data Factory använder för att skydda dina data. Data Factory-hanteringsresurser bygger på Azure-säkerhetsinfrastruktur och använder alla möjliga säkerhetsåtgärder som erbjuds av Azure.

I en Data Factory-lösning skapar du en eller flera data[pipelines](concepts-pipelines-activities.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipelines finns i den region där datafabriken skapades. 

Även om Data Factory bara är tillgängligt i några regioner är dataflytttjänsten [tillgänglig globalt](concepts-integration-runtime.md#integration-runtime-location) för att säkerställa dataefterlevnad, effektivitet och minskade kostnader för utgående nätverk. 

Azure Data Factory lagrar inga data förutom länkade tjänstautentiseringsuppgifter för molndatalager, som krypteras med hjälp av certifikat. Med Data Factory skapar du datadrivna arbetsflöden för att dirigera förflyttning av data mellan [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats)och bearbetning av data med hjälp av [beräkningstjänster](compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också övervaka och hantera arbetsflöden med hjälp av SDK:er och Azure Monitor.

Data Factory har certifierats för:

| **[CSA STAR-certifiering](https://www.microsoft.com/trustcenter/compliance/csa-star-certification)** |
| :----------------------------------------------------------- |
| **[ISO 20000-1:2011](https://www.microsoft.com/trustcenter/Compliance/ISO-20000-1)** |
| **[ISO 22301:2012](https://www.microsoft.com/trustcenter/compliance/iso-22301)** |
| **[ISO 27001:2013](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001)** |
| **[ISO 27017:2015](https://www.microsoft.com/trustcenter/compliance/iso-iec-27017)** |
| **[ISO 27018:2014](https://www.microsoft.com/trustcenter/compliance/iso-iec-27018)** |
| **[ISO 9001:2015](https://www.microsoft.com/trustcenter/compliance/iso-9001)** |
| **[SOC 1, 2, 3](https://www.microsoft.com/trustcenter/compliance/soc)** |
| **[HIPAA BAA](https://www.microsoft.com/trustcenter/compliance/hipaa)** |

Om du är intresserad av Azure-efterlevnad och hur Azure skyddar sin egen infrastruktur besöker du [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). För den senaste listan över alla https://aka.ms/AzureComplianceAzure Compliance-erbjudanden check - .

I den här artikeln granskar vi säkerhetsöverväganden i följande två scenarier för dataflyttning: 

- **Molnscenario:** I det här scenariot är både din källa och din destination allmänt tillgängliga via Internet. Dessa inkluderar hanterade molnlagringstjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-tjänster som Salesforce och webbprotokoll som FTP och OData. Hitta en fullständig lista över datakällor som stöds i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybridscenario:** I det här fallet är antingen din källa eller din destination bakom en brandvägg eller i ett lokalt företagsnätverk. Eller datalagret finns i ett privat nätverk eller virtuellt nätverk (oftast källan) och är inte allmänt tillgängligt. Databasservrar som finns på virtuella datorer omfattas också av det här scenariot.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Molnscenarier

### <a name="securing-data-store-credentials"></a>Skydda autentiseringsuppgifter för datalager

- **Lagra krypterade autentiseringsuppgifter i ett hanterat Azure Data Factory-arkiv**. Data Factory hjälper till att skydda dina autentiseringsuppgifter för datalagring genom att kryptera dem med certifikat som hanteras av Microsoft. Dessa certifikat roteras vartannat år (vilket inkluderar förnyelse av certifikat och migrering av autentiseringsuppgifter). Mer information om Azure Storage-säkerhet finns i [översikt över Azure Storage-säkerhet](../security/fundamentals/storage-overview.md).
- **Lagra autentiseringsuppgifter i Azure Key Vault**. Du kan också lagra datalagrets autentiseringsuppgifter i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory hämtar autentiseringsuppgifterna under körningen av en aktivitet. Mer information finns [i Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Datakryptering under överföring
Om molndatalagret stöder HTTPS eller TLS sker alla dataöverföringar mellan dataöverföringstjänster i Data Factory och ett molndatalager via säker kanal HTTPS eller TLS .

> [!NOTE]
> Alla anslutningar till Azure SQL Database och Azure SQL Data Warehouse kräver kryptering (SSL/TLS) medan data överförs till och från databasen. När du skapar en pipeline med JSON lägger du till krypteringsegenskapen och ställer in den på **true** i anslutningssträngen. För Azure Storage kan du använda **HTTPS** i anslutningssträngen.

> [!NOTE]
> Så här aktiverar du kryptering under överföring när du flyttar data från Oracle följer du något av nedanstående alternativ:
> 1. I Oracle server, gå till Oracle Advanced Security (OAS) och konfigurera krypteringsinställningar, som stöder Triple-DES Encryption (3DES) och Advanced Encryption Standard (AES), se [här](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) för mer information. ADF förhandlar automatiskt om krypteringsmetoden för att använda den du konfigurerar i OAS när du upprättar anslutning till Oracle.
> 2. I ADF kan du lägga till EncryptionMethod=1 i anslutningssträngen (i den länkade tjänsten). Detta kommer att använda SSL/TLS som krypteringsmetod. Om du vill använda detta måste du inaktivera icke-SSL-krypteringsinställningar i OAS på Oracle-serversidan för att undvika krypteringskonflikt.

> [!NOTE]
> TLS-versionen som används är 1.2.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa data lagrar stöder kryptering av data i vila. Vi rekommenderar att du aktiverar datakrypteringsmekanismen för dessa datalager. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent datakryptering (TDE) i Azure SQL Data Warehouse hjälper till att skydda mot hotet om skadlig aktivitet genom att utföra kryptering i realtid och dekryptering av dina data i vila. Detta är transparent för klienten. Mer information finns [i Skydda en databas i SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database stöder också transparent datakryptering (TDE), vilket hjälper till att skydda mot hotet om skadlig aktivitet genom att utföra kryptering i realtid och dekryptering av data, utan att kräva ändringar i programmet. Detta är transparent för klienten. Mer information finns i [Transparent datakryptering för SQL Database och Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store tillhandahåller också kryptering för data som lagras i kontot. När det är aktiverat krypterar DataSjöarkivet automatiskt data innan data sparas och dekrypteras före hämtning, vilket gör dem genomskinliga för klienten som kommer åt data. Mer information finns [i Säkerhet i Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Lagring av Azure Blob-lagring och Azure-tabell
Azure Blob storage och Azure Table Storage support Storage Service Encryption (SSE), som automatiskt krypterar dina data innan de sparas till lagring och dekrypterar före hämtning. Mer information finns i [Azure Storage Service Encryption for Data at Rest](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klient- och serverkryptering av data i vila. Mer information finns i [Skydda data med kryptering](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift stöder klusterkryptering för data i vila. Mer information finns i [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Shield Platform-kryptering som tillåter kryptering av alla filer, bilagor och anpassade fält. Mer information finns [i Förstå webbserverns OAuth-autentiseringsflöde](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridscenarier
Hybridscenarier kräver att självvärdförd integreringskörning måste installeras i ett lokalt nätverk, i ett virtuellt nätverk (Azure) eller i ett virtuellt privat moln (Amazon). Den självvärderade integrationskörningen måste kunna komma åt de lokala datalagret. Mer information om självvärdbaserad integrationskörning finns i [Så här skapar och konfigurerar du självvärd för integrationskörning](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![självvärderade integrationskörningskanaler](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kommandokanalen tillåter kommunikation mellan dataförflyttningstjänster i Data Factory och självvärderade integrationskörning. Meddelandet innehåller information om aktiviteten. Datakanalen används för överföring av data mellan lokala datalager och molndatalager.    

### <a name="on-premises-data-store-credentials"></a>Autentiseringsuppgifter för lokalt datalager
Autentiseringsuppgifterna kan lagras i datafabriken eller [refereras av datafabriken](store-credentials-in-key-vault.md) under körningen från Azure Key Vault. Om du lagrar autentiseringsuppgifter i datafabriken lagras den alltid krypterad på den självvärderade integrationskörningen. 
 
- **Lagra autentiseringsuppgifter lokalt**. Om du direkt använder cmdleten **Set-AzDataFactoryV2LinkedService** med anslutningssträngarna och autentiseringsuppgifterna i JSON krypteras den länkade tjänsten och lagras på självvärdbaserad integrationskörning.  I det här fallet flödar autentiseringsuppgifterna via azure backend-tjänsten, som är extremt säker, till den självvärderade integrationsdatorn där den slutligen krypteras och lagras. Den självvärderade integrationskörningen använder Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) för att kryptera känslig data och autentiseringsuppgifter.

- **Lagra autentiseringsuppgifter i Azure Key Vault**. Du kan också lagra datalagrets autentiseringsuppgifter i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory hämtar autentiseringsuppgifterna under körningen av en aktivitet. Mer information finns [i Lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md).

- **Lagra autentiseringsuppgifter lokalt utan att flöda autentiseringsuppgifterna via Azure-backend till den självvärderade integrationskörningen**. Om du vill kryptera och lagra autentiseringsuppgifter lokalt på den självvärderade integrationskörningen utan att behöva flöda autentiseringsuppgifterna via datafabrikens backend följer du stegen i [Kryptera autentiseringsuppgifter för lokala datalager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Alla kopplingar stöder det här alternativet. Den självvärderade integrationskörningen använder Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) för att kryptera känslig data och autentiseringsuppgifter. 

   Använd **cmdleten New-AzDataFactoryV2LinkedServiceEncryptedCredential** för att kryptera länkade tjänstautentiseringsuppgifter och känslig information i den länkade tjänsten. Du kan sedan använda det JSON som returneras (med elementet **EncryptedCredential** i anslutningssträngen) för att skapa en länkad tjänst med hjälp av cmdleten **Set-AzDataFactoryV2LinkedService.**  


#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portar som används vid kryptering av länkad tjänst vid självvärd för integrationskörning
Som standard använder PowerShell port 8060 på datorn med självvärdförd integreringskörning för säker kommunikation. Om det behövs kan den här porten ändras.  

![HTTPS-port för gatewayen](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla dataöverföringar sker via säker kanal HTTPS och TLS över TCP för att förhindra man-in-the-middle-attacker under kommunikation med Azure-tjänster.

Du kan också använda [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Azure ExpressRoute](../expressroute/expressroute-introduction.md) för att ytterligare skydda kommunikationskanalen mellan ditt lokala nätverk och Azure.

Azure Virtual Network är en logisk representation av nätverket i molnet. Du kan ansluta ett lokalt nätverk till ditt virtuella nätverk genom att konfigurera IPSec VPN (plats-till-plats) eller ExpressRoute (privat peering).    

I följande tabell sammanfattas konfigurationsrekommendationerna för nätverks- och självvärderade integreringskörningar baserat på olika kombinationer av käll- och målplatser för hybriddataförflyttning.

| Källa      | Mål                              | Nätverkskonfiguration                    | Installation av Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokal | Virtuella datorer och molntjänster som distribueras i virtuella nätverk | IPSec VPN (punkt-till-plats eller plats-till-plats) | Den självvärderade integrationskörningen bör installeras på en virtuell Azure-dator i det virtuella nätverket.  |
| Lokal | Virtuella datorer och molntjänster som distribueras i virtuella nätverk | ExpressRoute (privat peering)           | Den självvärderade integrationskörningen bör installeras på en virtuell Azure-dator i det virtuella nätverket.  |
| Lokal | Azure-baserade tjänster som har en offentlig slutpunkt | ExpressRoute (Microsoft-peering)            | Den självvärderade integrationskörningen kan installeras lokalt eller på en virtuell Azure-dator. |

Följande avbildningar visar användningen av självvärdbaserad integrationskörning för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av ExpressRoute och IPSec VPN (med Azure Virtual Network):

**ExpressRoute**

![Använda ExpressRoute med gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN med gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-allow-list-setting-up-for-ip-addresses"></a><a name="firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway"></a>Brandväggskonfigurationer och tillåta listinställning för IP-adresser

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Brandväggskrav för lokalt/privat nätverk    
I ett företag körs en företagsbrandvägg på organisationens centrala router. Windows-brandväggen körs som en demon på den lokala datorn där den självvärderade integrationskörningen är installerad. 

I följande tabell finns utgående port- och domänkrav för företagsbrand brandväggar:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

> [!NOTE] 
> Du kan behöva hantera portar eller konfigurera tillåt-lista för domäner på företagets brandväggsnivå enligt kraven i respektive datakällor. Den här tabellen använder endast Azure SQL Database, Azure SQL Data Warehouse och Azure Data Lake Store som exempel.   

I följande tabell finns krav på inkommande port för Windows-brandväggen:

| Ingående portar | Beskrivning                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Krävs av PowerShell-krypterings-cmdlet enligt beskrivningen i [Kryptera autentiseringsuppgifter för lokala datalager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)och av autentiseringshanteraren-programmet för att på ett säkert sätt ange autentiseringsuppgifter för lokala datalager på den självvärderade integrationskörningen. |

![Krav på gatewayport](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-allow-list-setting-up-in-data-stores"></a>IP-konfigurationer och tillåta listinställning i datalager
Vissa datalager i molnet kräver också att du tillåter IP-adressen för den dator som ansluter till butiken. Kontrollera att IP-adressen för den självvärderade integrationskörningsdatorn tillåts eller konfigureras i brandväggen på rätt sätt.

Följande molndatalager kräver att du tillåter IP-adressen för den självvärderade integrationsdatorn. Vissa av dessa datalager kanske som standard inte kräver listan över tillåtande. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL-datalager](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Kan den självvärderade integrationskörningen delas mellan olika datafabriker?**

Ja. Mer information finns [här](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Vilka är portkraven för att den självvärderade integrationskörningen ska fungera?**

Den självvärderade integrationskörningen gör HTTP-baserade anslutningar för att komma åt internet. De utgående portarna 443 måste öppnas för den självvärderade integrationskörningen för att den här anslutningen ska kunna upprättas. Öppna inkommande port 8060 endast på datornivå (inte företagets brandväggsnivå) för autentiseringsuppgifter.Open inbound port 8060 only at the machine level (not the corporate firewall level) for credential manager application. Om Azure SQL Database eller Azure SQL Data Warehouse används som källa eller mål måste du även öppna port 1433. Mer information finns i [avsnittet Brandväggskonfigurationer och tillåta listinställning för IP-adresser.](#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway) 


## <a name="next-steps"></a>Nästa steg
Information om prestanda för Azure Data Factory Copy Activity finns i [Kopiera aktivitetsprestanda och justeringsguide](copy-activity-performance.md).

 
