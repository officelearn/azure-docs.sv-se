---
title: Säkerhetsöverväganden i Azure Data Factory | Microsoft Docs
description: Beskriver grundläggande säkerhetsinfrastruktur som dataförflyttning i Azure Data Factory använder för att skydda dina data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: d684ec56c7dfcc28d1057d0b20905db49bce9723
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55498081"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Säkerhetsöverväganden för dataförflyttning i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [Version 1](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuell version](data-movement-security-considerations.md)

Den här artikeln beskriver grundläggande säkerhetsinfrastruktur som dataförflyttning i Azure Data Factory använder för att skydda dina data. Data Factory-hanteringsresurser bygger på Azure säkerhetsinfrastruktur och använda alla möjliga skyddsåtgärder som erbjuds av Azure.

I en Data Factory-lösning skapar du en eller flera data[pipelines](concepts-pipelines-activities.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipelines finns i den region där datafabriken har skapats. 

Även om Data Factory är endast tillgänglig i några regioner, av data movement service är [tillgänglig globalt](concepts-integration-runtime.md#integration-runtime-location) för att säkerställa dataefterlevnad, effektivitet och minskade nätverk egress kostnader. 

Azure Data Factory lagrar inte alla data utom länkade autentiseringsuppgifter för molndatalager som krypteras med hjälp av certifikat. Med Data Factory kan du skapa datadrivna arbetsflöden som samordnar flödet av data mellan [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats), och bearbetning av data med hjälp av [Beräkningstjänster](compute-linked-services.md) i andra regioner eller i en lokala miljö. Du kan också övervaka och hantera arbetsflöden med hjälp av SDK: er och Azure Monitor.

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

Om du är intresserad av efterlevnad i Azure och hur Azure skyddar sin egen infrastruktur kan du gå till den [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). För den senaste listan över alla Azure-erbjudanden kompatibilitetskontrollen - http://aka.ms/AzureCompliance.

I den här artikeln ska granska vi säkerhetsaspekter i följande två data movement scenarier: 

- **Scenariot med molnet**: I det här scenariot är både källan och målet tillgängliga för allmänheten via internet. Dessa inkluderar hanterade molnlagringstjänster, till exempel Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-tjänster, till exempel Salesforce och webbprotokoll som FTP- och OData. En fullständig lista över datakällor som stöds i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scenario med hybridanvändning**: I det här scenariot är antingen källan eller målet bakom en brandvägg eller i ett lokalt företagsnätverk. Eller så datalagret är i ett privat nätverk eller virtuella nätverk (oftast källan) och inte är allmänt tillgänglig. Database-servrar som körs på virtuella datorer omfattas också det här scenariot.

## <a name="cloud-scenarios"></a>Scenarier för molnet

### <a name="securing-data-store-credentials"></a>Att säkra autentiseringsuppgifter för datalagring

- **Store krypterade autentiseringsuppgifter i en hanterad Azure Data Factory-store**. Data Factory hjälper dig att skydda dina data store autentiseringsuppgifter genom att kryptera dem med certifikat som hanteras av Microsoft. Dessa certifikat roteras vartannat år (som innehåller förnyelse av certifikat och migreringen av autentiseringsuppgifter). Krypterade autentiseringsuppgifter lagras på ett säkert sätt i ett Azure storage-konto som hanteras av Azure Data Factory hanteringstjänster. Mer information om Azure Storage-säkerhet finns i [Säkerhetsöversikt för Azure Storage](../security/security-storage-overview.md).
- **Store autentiseringsuppgifter i Azure Key Vault**. Du kan också lagra data store autentiseringsuppgifter i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory hämtar autentiseringsuppgifterna vid körningen av en aktivitet. Mer information finns i [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Datakryptering under överföring
Om molndatalagret stöder HTTPS- eller TLS, alla data som överförs mellan dataförflyttning i Data Factory och ett molndatalager är via säker kanal HTTPS- eller TLS.

> [!NOTE]
> Alla anslutningar till Azure SQL Database och Azure SQL Data Warehouse kräver kryptering (SSL/TLS) datakommunikationen till och från databasen. När du är på att redigera en pipeline med hjälp av JSON, lägga till egenskapen och ge den värdet **SANT** i anslutningssträngen. Du kan använda för Azure Storage, **HTTPS** i anslutningssträngen.

> [!NOTE]
> Aktivera kryptering under överföring när du flyttar data från Oracle gör du något av de nedan alternativ:
> 1. I Oracle-server, gå till Oracle avancerad säkerhet (OAS) och konfigurera krypteringsinställningar, vilken har stöd för kryptering av trippel-DES (3DES) och Advanced Encryption Standard (AES), se [här](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) mer information. ADF förhandlar automatiskt krypteringsmetod för att använda den som du konfigurerar i OAS när anslutning till Oracle.
> 2. I ADF, kan du lägga till EncryptionMethod = 1 i anslutningssträngen (i den länkade tjänsten). Detta använder SSL/TLS som krypteringsmetod. För att använda detta, måste du inaktivera icke-SSL krypteringsinställningar i OAS på serversidan Oracle för att undvika konflikt för kryptering.

> [!NOTE]
> TLS-version som används är 1.2.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa datalager stöd för kryptering av vilande data. Vi rekommenderar att du aktiverar krypteringsalgoritm som data för dessa datalager. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent datakryptering (TDE) i Azure SQL Data Warehouse kan du skydda mot skadlig aktivitet genom att utföra i realtid kryptering och dekryptering av dina data i vila. Det här beteendet är transparent för klienten. Mer information finns i [skydda en databas i SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database har även stöd för transparent datakryptering (TDE), som hjälper dig att skydda mot skadlig programvara genom att utföra i realtid kryptering och dekryptering av data, utan ändringar i programmet. Det här beteendet är transparent för klienten. Mer information finns i [Transparent datakryptering för SQL Database och Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store innehåller också kryptering för data som lagras i kontot. När aktiverat krypterar data före beständig lagring automatiskt i Data Lake Store och dekrypterar innan hämtning, vilket gör det transparent till klienten som har åtkomst till data. Mer information finns i [säkerhet i Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob storage och Azure Table storage
Azure Blob storage och Azure Table storage stöder Storage Service Encryption (SSE), som automatiskt krypterar dina data före beständig lagring och dekrypterar före hämtning. Mer information finns i [Azure Storage Service Encryption för vilande Data](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klienten och servern kryptering av vilande data. Mer information finns i [skyddar Data med hjälp av kryptering](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift stöder kluster kryptering för vilande data. Mer information finns i [databaskryptering för Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Shield plattform kryptering som har stöd för kryptering av alla filer, bilagor och anpassade fält. Mer information finns i [förstå flöda till autentisering för Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybridscenarier
Hybridscenarier kräver lokal integration runtime som ska installeras i ett lokalt nätverk i ett virtuellt nätverk (Azure), eller i ett virtuellt privat moln (Amazon). Den lokala integreringskörningen måste kunna komma åt lokala datalager. Läs mer om lokal integration runtime [hur du skapar och konfigurerar integration runtime med egen värd](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![lokal integration runtime-kanaler](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kommandokanalen möjliggör kommunikation mellan dataförflyttning i Data Factory och lokal integration runtime. Kommunikationen innehåller information relaterad till aktivitet. Datakanalen används för att överföra data mellan lokala datalager och molndatalager.    

### <a name="on-premises-data-store-credentials"></a>Lokala autentiseringsuppgifter för datalagring
Autentiseringsuppgifterna för dina lokala datalager alltid krypteras och lagras. De kan vara antingen lagras lokalt på den lokala installation av integration runtime-datorn eller lagras i Azure Data Factory hanteras storage (precis som cloud store autentiseringsuppgifter). 

- **Store autentiseringsuppgifter lokalt**. Om du vill kryptera och lagra autentiseringsuppgifter lokalt på den lokala integreringskörningen, följer du stegen i [kryptera autentiseringsuppgifterna för den lokala datalager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Alla anslutningar stöder det här alternativet. Lokal integration runtime använder Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) att kryptera känsliga data och autentiseringsuppgifter information. 

   Använd den **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** cmdlet för att kryptera autentiseringsuppgifterna för den länkade tjänsten och känslig information i den länkade tjänsten. Du kan sedan använda den JSON som returneras (med den **EncryptedCredential** element i anslutningssträngen) skapa en länkad tjänst med hjälp av den **Set-AzureRmDataFactoryV2LinkedService** cmdlet.  

- **Store i Azure Data Factory hanteras storage**. Om du använder direkt i **Set-AzureRmDataFactoryV2LinkedService** cmdlet med anslutningen anslutningssträngar och autentiseringsuppgifter infogade i JSON, den länkade tjänsten krypteras och lagras i Azure Data Factory hanterad lagring. Känslig information som krypteras fortfarande av certifikat och Microsoft hanterar dessa certifikat.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portar som används när du krypterar länkad tjänst på lokal integration runtime
Som standard använder PowerShell port 8050 på datorn med lokal integration runtime för säker kommunikation. Om det behövs kan du ändra den här porten.  

![HTTPS-port för gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla dataöverföringar är via säker kanal HTTPS och TLS via TCP för att förhindra att man-in-the-middle vid kommunikation med Azure-tjänster.

Du kan också använda [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Azure ExpressRoute](../expressroute/expressroute-introduction.md) att ytterligare skydda kommunikationskanalen mellan ditt lokala nätverk och Azure.

Azure-nätverk är en logisk representation av ditt nätverk i molnet. Du kan ansluta ett lokalt nätverk till det virtuella nätverket genom att konfigurera IPSec-VPN (plats-till-plats) eller ExpressRoute (privat peering).    

I följande tabell sammanfattas i nätverket och lokal integration runtime-konfigurationsrekommendationer baserat på olika kombinationer av käll- och platser för flytt av hybriddata.

| Källa      | Mål                              | Nätverkskonfiguration                    | Installation av Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokal | Virtuella datorer och molntjänster som distribuerats i virtuella nätverk | IPSec-VPN (punkt-till-plats eller plats-till-plats) | Lokal integration runtime kan installeras antingen lokalt eller på en Azure-dator i ett virtuellt nätverk. |
| Lokal | Virtuella datorer och molntjänster som distribuerats i virtuella nätverk | ExpressRoute (privat peering)           | Lokal integration runtime kan installeras antingen lokalt eller på en Azure-dator i ett virtuellt nätverk. |
| Lokal | Azure-baserade tjänster som har en offentlig slutpunkt | ExpressRoute (offentlig peering)            | Den lokala integreringskörningen måste vara installerade på plats. |

Följande bilder visar användning av lokal integration runtime för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av ExpressRoute- och IPSec-VPN (med Azure Virtual Network):

**ExpressRoute**

![Använda ExpressRoute med gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec-VPN**

![IPSec VPN med gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Brandväggskonfigurationer och listan över tillåtna IP-adresser

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Brandväggskrav för om-plats/privat nätverk  
Företag kan en företagsbrandvägg som körs på den centrala routern organisationens. Windows-brandväggen körs som en daemon på den lokala datorn där lokal integration runtime har installerats. 

Följande tabell innehåller kraven för utgående port och domän för företagsbrandväggar:

| Domännamn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av lokal integration runtime kan ansluta till dataförflyttning i Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Krävs av lokal integration runtime för att ansluta till Data Factory-tjänsten. |
| `download.microsoft.com`    | 443            | Kräver en lokal integration runtime för att ladda ned uppdateringarna. Om du har inaktiverat automatisk uppdatering sedan du kan hoppa över detta. |
| `*.core.windows.net`          | 443            | Används av lokal integration runtime för att ansluta till Azure storage-kontot när du använder den [mellanlagrad kopiering](copy-activity-performance.md#staged-copy) funktionen. |
| `*.database.windows.net`      | 1433           | (Valfritt) Krävs när du kopierar från eller till Azure SQL Database eller Azure SQL Data Warehouse. Använd funktionen mellanlagrad kopiering för att kopiera data till Azure SQL Database eller Azure SQL Data Warehouse utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Valfritt) Krävs när du kopierar från eller till Azure Data Lake Store. |

> [!NOTE] 
> Du kan behöva hantera portar eller lista över tillåtna domäner på företagets brandvägg nivå som krävs av respektive datakällor. Den här tabellen använder bara Azure SQL Database, Azure SQL Data Warehouse och Azure Data Lake Store som exempel.   

Följande tabell innehåller kraven för inkommande port för Windows-brandväggen:

| Ingående portar | Beskrivning                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Krävs för kryptering PowerShell-cmdleten enligt beskrivningen i [kryptera autentiseringsuppgifterna för den lokala datalager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md), och av Autentiseringshanteraren att ange autentiseringsuppgifter för lokala datalager på ett säkert sätt på den lokala integreringskörningen. |

![Krav för gateway-port](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>IP-konfigurationer och listan över tillåtna program i datalager
Vissa datalager i molnet kräver också att du godkänner IP-adressen för den datorn åtkomst till arkivet. Kontrollera att IP-adressen för den lokala installation av integration runtime-datorn är godkänd eller korrekt konfigurerad i brandväggen.

För följande datalager i molnet kräver att du godkänner IP-adressen för den lokala installation av integration runtime-datorn. Några av dessa datalager, som standard kan inte kräva listan över tillåtna program. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Kan den lokala integreringskörningen delas mellan olika datafabriker?**

Vi stöder inte den här funktionen ännu. Vi arbetar på den.

**Vilka är portkraven för den lokala integreringskörningen ska fungera?**

Lokal integration runtime gör HTTP-baserade anslutningar för att ansluta till internet. Utgående portarna 443 måste vara tillgänglig för den lokala integreringskörningen att upprätta anslutningen. Öppna inkommande port 8050 endast på datornivå (inte företagets brandvägg nivå) för Autentiseringshanteraren. Om Azure SQL Database eller Azure SQL Data Warehouse används som källan eller målet, måste du öppna port 1433 samt. Mer information finns i den [konfigurationer och listan över tillåtna IP-adresser i brandväggen](#firewall-configurations-and-whitelisting-ip-address-of-gateway) avsnittet. 


## <a name="next-steps"></a>Nästa steg
Information om prestanda för Azure Data Factory Kopieringsaktivitet finns i [Kopieringsaktiviteten prestanda- och Justeringsguiden](copy-activity-performance.md).

 
