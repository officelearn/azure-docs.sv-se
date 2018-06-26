---
title: Säkerhetsaspekter i Azure Data Factory | Microsoft Docs
description: Beskriver grundläggande infrastruktur med flytt datatjänster i Azure Data Factory för att skydda dina data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: 5d9061e12ac9fe0b9d858690897e582acab5169e
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754581"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Säkerhetsaspekter vid flytt av data i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-data-movement-security-considerations.md)
> * [Version 2 – förhandsversion](data-movement-security-considerations.md)

Den här artikeln beskriver grundläggande infrastruktur med flytt datatjänster i Azure Data Factory för att skydda dina data. Data Factory hanteringsresurser bygger på Azure säkerhetsinfrastruktur och Använd alla möjliga skyddsåtgärder som Azure erbjuder.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Data movement säkerhetsaspekter för Data Factory version 1](v1/data-factory-data-movement-security-considerations.md).

I en Data Factory-lösning skapar du en eller flera data[pipelines](concepts-pipelines-activities.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipelines finns i den region där datafabriken har skapats. 

Även om Data Factory finns bara i några regioner, av data movement service är [tillgängligt globalt](concepts-integration-runtime.md#integration-runtime-location) för att säkerställa efterlevnad av data, effektivitet och minskade nätverket utgång kostnader. 

Azure Data Factory lagrar inte alla data utom länkade tjänsten autentiseringsuppgifter för molnet datalager som krypteras med hjälp av certifikat. Med Data Factory du skapa datadrivna arbetsflöden för att dirigera flödet av data mellan [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats), och bearbetning av data med hjälp av [compute services](compute-linked-services.md) i andra regioner eller i en lokala miljö. Du kan också övervaka och hantera arbetsflöden med hjälp av SDK: er och Azure-Monitor.

Flytt av data med hjälp av Data Factory har certifierats för:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STJÄRNA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Om du är intresserad av Azure efterlevnad och hur Azure skyddar sin egen infrastruktur, finns det [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

Vi går igenom säkerhetsaspekter i följande scenarier för flytt av två data i den här artikeln: 

- **Scenariot**: I det här scenariot både käll- och ditt mål är offentligt tillgänglig via internet. Dessa inkluderar hanterad storage-tjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift SaaS-tjänster, till exempel Salesforce och webbprotokoll, till exempel FTP och OData. Hitta en fullständig lista över datakällor som stöds i [datalager och format stöds](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybridscenario**: I det här scenariot källan eller målet är bakom en brandvägg eller ett företagsnätverk lokalt. Eller, datalagret är i ett privat nätverk eller virtuella nätverk (oftast källan) och inte är allmänt tillgänglig. Database-servrar som är värd för virtuella datorer också omfattas av det här scenariot.

## <a name="cloud-scenarios"></a>Scenarier för

### <a name="securing-data-store-credentials"></a>Att säkra autentiseringsuppgifterna för datalager

- **Lagra krypterade autentiseringsuppgifter i en hanterad Azure Data Factory-store**. Data Factory skyddar autentiseringsuppgifterna för ditt datalager genom att kryptera dem med certifikat som hanteras av Microsoft. Dessa certifikat roteras vartannat år (som inkluderar certifikatförnyelse och migreringen av autentiseringsuppgifter). Krypterade autentiseringsuppgifter lagras på ett säkert sätt i ett Azure storage-konto som hanteras av Azure Data Factory hanteringstjänster. Mer information om säkerhet i Azure Storage finns [översikt över säkerheten i Azure Storage](../security/security-storage-overview.md).
- **Lagra autentiseringsuppgifter i Azure Key Vault**. Du kan också lagra data store autentiseringsuppgifter i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory hämtar referenserna under körningen av en aktivitet. Mer information finns i [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Datakryptering under överföring
Om datalagret molnet stöder HTTPS- eller TLS, alla data som överförs mellan flytt datatjänster i Data Factory och ett datalager i molnet är via en säker kanal HTTPS- eller TLS.

> [!NOTE]
> Alla anslutningar till Azure SQL Database och Azure SQL Data Warehouse Kräv kryptering (SSL/TLS) när data är i överföringen till och från databasen. När du håller på att redigera en pipeline med hjälp av JSON, lägga till egenskapen kryptering och ange det till **SANT** i anslutningssträngen. Du kan använda för Azure Storage, **HTTPS** i anslutningssträngen.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa data lagras stöd för kryptering av data i vila. Vi rekommenderar att du aktiverar data krypteringsmekanism för dessa databaser. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent Data kryptering (TDE) i Azure SQL Data Warehouse skyddar mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av data i vila. Det här beteendet är transparent för klienten. Mer information finns i [skydda en databas i SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database stöder också transparent datakryptering (TDE), som skyddar mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av data, utan ändringar i programmet. Det här beteendet är transparent för klienten. Mer information finns i [Transparent datakryptering för SQL-databasen och datalagret](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store ger också kryptering för data som lagras på kontot. När aktiverat, krypteras data innan beständighet automatiskt i Data Lake Store och dekrypterar före hämtning, att det är transparent för klienten som har åtkomst till data. Mer information finns i [säkerhet i Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob storage och Azure Table storage
Azure Blob storage och Azure Table storage stöder Storage Service kryptering (SSE), som automatiskt krypterar dina data innan beständighet till lagring och dekrypterar före hämtning. Mer information finns i [Azure Storage Service-kryptering för Data i vila](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klienten och servern kryptering av vilande data. Mer information finns i [skydda Data med hjälp av kryptering](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift stöder kluster kryptering för data i vila. Mer information finns i [Amazon Redshift databaskryptering](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Shield plattform kryptering som har stöd för kryptering av alla filer, bilagor och anpassade fält. Mer information finns i [förstå flöda till autentisering för Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridmoln
Hybridscenarier kräver själva värdbaserade integration runtime installeras i ett lokalt nätverk i ett virtuellt nätverk (Azure) eller i ett virtuellt privat moln (Amazon). Automatisk värdbaserade integration runtime måste kunna få åtkomst till lokala datalager. Läs mer om automatisk värdbaserade integration runtime [egenvärdbaserat integrering runtime om hur du skapar och konfigurerar](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![automatisk värdbaserade runtime-integrationskanaler](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kommandokanalen tillåter kommunikation mellan flytt datatjänster i Data Factory och automatisk värdbaserade integration körning. Kommunikationen innehåller information relaterad till aktivitet. Datakanalen används för att överföra data mellan lokala datalager och molnet dataarkiv.    

### <a name="on-premises-data-store-credentials"></a>Lokala autentiseringsuppgifterna för datalager
Autentiseringsuppgifterna för ditt lokala datalager alltid krypteras och lagras. De kan vara antingen lagras lokalt på själva värdbaserade integration runtime datorn eller lagras i Azure Data Factory hanteras storage (precis som molnet store autentiseringsuppgifter). 

- **Lagra autentiseringsuppgifter lokalt**. Om du vill kryptera och lagra autentiseringsuppgifter lokalt på själva värdbaserade integration runtime, följer du stegen i [kryptera autentiseringsuppgifterna för lokalt datalager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Alla kopplingar stöder det här alternativet. Automatisk värdbaserade integration runtime använder Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) att kryptera känsliga data och autentiseringsuppgifter information. 

   Använd den **ny AzureRmDataFactoryV2LinkedServiceEncryptedCredential** för att kryptera autentiseringsuppgifter för länkad tjänst och känslig information i den länkade tjänsten. Du kan sedan använda JSON returnerade (med den **EncryptedCredential** element i anslutningssträngen) skapa en länkad tjänst med hjälp av den **Set AzureRmDataFactoryV2LinkedService** cmdlet.  

- **Lagra i Azure Data Factory hanteras lagring**. Om du använder direkt i **Set AzureRmDataFactoryV2LinkedService** med anslutningen strängar och autentiseringsuppgifter infogad i JSON, den länkade tjänsten krypteras och lagras i Azure Data Factory hanteras lagring. Känslig information krypteras fortfarande av certifikat och Microsoft hanterar dessa certifikat.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portar som används när du krypterar länkad tjänst på själva värdbaserade integration runtime
Som standard använder PowerShell port 8050 på datorn med automatisk värdbaserade integration runtime för säker kommunikation. Om det behövs kan du ändra den här porten.  

![HTTPS-port för gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla dataöverföringar är via en säker kanal HTTPS och TLS via TCP för att förhindra att man-in-the-middle under kommunikationen med Azure-tjänster.

Du kan också använda [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Azure ExpressRoute](../expressroute/expressroute-introduction.md) att ytterligare skydda kommunikationskanalen mellan ditt lokala nätverk och Azure.

Azure-nätverk är en logisk representation av ditt nätverk i molnet. Du kan ansluta till ett lokalt nätverk till det virtuella nätverket genom att skapa IPSec-VPN (plats-till-plats) eller ExpressRoute (privat peering).    

I följande tabell sammanfattas nätverket och automatisk värdbaserade integration runtime rekommendationer baserat på olika kombinationer av käll- och platser för hybrid dataflyttning.

| Källa      | Mål                              | Nätverkskonfiguration                    | Installation av Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokal | Virtuella datorer och molntjänster som är distribuerad i virtuella nätverk | IPSec-VPN (punkt-till-plats eller plats-till-plats) | Själva värdbaserade integration körning kan installeras antingen lokalt eller på en Azure-dator i ett virtuellt nätverk. |
| Lokal | Virtuella datorer och molntjänster som är distribuerad i virtuella nätverk | ExpressRoute (privat peering)           | Själva värdbaserade integration körning kan installeras antingen lokalt eller på en Azure-dator i ett virtuellt nätverk. |
| Lokal | Azure-baserade tjänster som har en offentlig slutpunkt | ExpressRoute (offentlig peering)            | Automatisk värdbaserade integration runtime måste vara installerad lokalt. |

Följande bilder visar användningen av själva värdbaserade integration runtime för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av ExpressRoute- och IPSec-VPN (med Azure Virtual Network):

**ExpressRoute**

![Använda ExpressRoute med gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN med gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Brandväggskonfigurationer och vitlistning av IP-adresser

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Krav för brandväggen för för lokala/privat nätverk  
I ett företag körs en företagsbrandvägg på den centrala routern för organisationen. Windows-brandväggen körs som en daemon på den lokala datorn där själva värdbaserade integration runtime är installerat. 

Följande tabell innehåller kraven för utgående port och domän för företagsbrandväggar:

| Domännamn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av körningsmiljön själva värdbaserade integration att ansluta till data movement tjänster i Data Factory. |
| `*.core.windows.net`          | 443            | Används av själva värdbaserade integration körningen för att ansluta till Azure storage-konto när du använder den [mellanlagrad kopiera](copy-activity-performance.md#staged-copy) funktion. |
| `*.frontend.clouddatahub.net` | 443            | Krävs av själva värdbaserade integration körningen för att ansluta till Data Factory-tjänsten. |
| `*.database.windows.net`      | 1433           | (Valfritt) Krävs när du kopierar från eller till Azure SQL Database eller Azure SQL Data Warehouse. Använda kopieringsfunktionen mellanlagrade för att kopiera data till Azure SQL Database eller Azure SQL Data Warehouse utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Valfritt) Krävs när du kopierar från eller till Azure Data Lake Store. |

> [!NOTE] 
> Du kan behöva hantera portar eller vitlistning domäner på företagets brandvägg nivå som krävs av respektive datakällor. Den här tabellen använder bara Azure SQL Database, Azure SQL Data Warehouse och Azure Data Lake Store som exempel.   

Följande tabell innehåller kraven för inkommande portar för Windows-brandväggen:

| Ingående portar | Beskrivning                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Krävs av PowerShell-cmdlet för kryptering, enligt beskrivningen i [kryptera autentiseringsuppgifterna för lokalt datalager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), och av autentiseringsuppgifter manager programmet för att ange autentiseringsuppgifter för lokala data lagras på ett säkert sätt på själva värdbaserade integration körningsmiljön. |

![Krav för gateway-port](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>IP-konfigurationer och vitlistning i datalager
Vissa data lagras i molnet kräver också att du listan över godkända IP-adressen för den datorn åtkomst till store. Kontrollera att IP-adressen för automatisk värdbaserade integration runtime-datorn är godkända eller konfigurerad i brandväggen.

Följande molntjänster datalager kräver att du listan över godkända IP-adressen för automatisk värdbaserade integration runtime-datorn. Vissa av dessa datalager som standard kan inte kräva vitlistning. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Kan själva värdbaserade integration runtime delas mellan olika datafabriker?**

Vi stöder inte den här funktionen ännu. Vi arbetar aktivt på den.

**Vilka är portkraven på för automatisk värdbaserade integration runtime ska fungera?**

Automatisk värdbaserade integration runtime gör HTTP-baserade anslutningar till internet. Utgående portarna 443 och 80 måste öppnas för automatisk värdbaserade integration runtime att upprätta anslutningen. Öppna inkommande port 8050 endast på datornivå (inte företagsbrandvägg nivå) för autentiseringsuppgifter manager-programmet. Om Azure SQL Database eller Azure SQL Data Warehouse används som källan eller målet, måste du öppna port 1433 samt. Mer information finns i [konfigurationer och vitlistning av IP-adresser i brandväggen](#firewall-configurations-and-whitelisting-ip-address-of-gateway) avsnitt. 


## <a name="next-steps"></a>Nästa steg
Information om Azure Data Factory-Kopieringsaktiviteten prestanda finns [prestandajustering guide och Kopieringsaktivitet prestanda](copy-activity-performance.md).

 
