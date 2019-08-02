---
title: Säkerhets aspekter i Azure Data Factory | Microsoft Docs
description: Beskriver grundläggande säkerhets infrastruktur som tjänster för data flyttning i Azure Data Factory använder för att skydda dina data.
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
ms.openlocfilehash: c42e70efc8543e1d255690070ffb51b865e1754f
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608591"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Säkerhets överväganden för data förflyttning i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
>
> * [Version 1](v1/data-factory-data-movement-security-considerations.md)
> * [Aktuell version](data-movement-security-considerations.md)

Den här artikeln beskriver grundläggande säkerhets infrastruktur som tjänster för data flyttning i Azure Data Factory använder för att skydda dina data. Data Factory hanterings resurser bygger på Azures säkerhets infrastruktur och använder alla möjliga säkerhets åtgärder som erbjuds av Azure.

I en Data Factory-lösning skapar du en eller flera data[pipelines](concepts-pipelines-activities.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipeliner finns i den region där data fabriken skapades. 

Även om Data Factory bara är tillgängligt i några regioner, är tjänsten för data flyttning [tillgänglig globalt](concepts-integration-runtime.md#integration-runtime-location) för att säkerställa att data efterlevs, effektivitets och minskade kostnader för utgående nätverks trafik. 

Azure Data Factory lagrar inga data utom länkade autentiseringsuppgifter för tjänsten för moln data lager som krypteras med hjälp av certifikat. Med Data Factory skapar du data drivna arbets flöden för att dirigera data flödet mellan [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats)och bearbetning av data med hjälp av [beräknings tjänster](compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också övervaka och hantera arbets flöden med hjälp av SDK: er och Azure Monitor.

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

Om du är intresse rad av Azure-kompatibilitet och hur Azure skyddar sin egen infrastruktur kan du gå till [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). För den senaste listan över alla Azure Compliance- https://aka.ms/AzureCompliance erbjudanden kontrollerar du.

I den här artikeln granskar vi säkerhets aspekter i följande två scenarier för data förflyttning: 

- **Moln scenario**: I det här scenariot är både din källa och ditt mål offentligt tillgängliga via Internet. Dessa omfattar hanterade moln lagrings tjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-tjänster som Salesforce och webb protokoll som FTP och OData. Hitta en fullständig lista över data källor som stöds i [data lager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybrid scenario**: I det här scenariot finns antingen din källa eller ditt mål bakom en brand vägg eller i ett lokalt företags nätverk. Eller så är data lagret i ett privat nätverk eller virtuellt nätverk (oftast källan) och är inte offentligt tillgänglig. Databas servrar som är värdar för virtuella datorer omfattas också av det här scenariot.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Moln scenarier

### <a name="securing-data-store-credentials"></a>Skydda autentiseringsuppgifter för data lager

- **Lagra krypterade autentiseringsuppgifter i en Azure Data Factory hanterad butik**. Data Factory skyddar dina data lagrings uppgifter genom att kryptera dem med certifikat som hanteras av Microsoft. Dessa certifikat roteras varannan år (vilket omfattar certifikat förnyelse och migrering av autentiseringsuppgifter). De krypterade autentiseringsuppgifterna lagras på ett säkert sätt i ett Azure Storage-konto som hanteras av Azure Data Factory Management Services. Mer information om Azure Storage säkerhet finns i [Azure Storage säkerhets översikt](../security/fundamentals/storage-overview.md).
- **Lagra autentiseringsuppgifter i Azure Key Vault**. Du kan också lagra data lagrets autentiseringsuppgifter i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Data Factory hämtar autentiseringsuppgifterna under körningen av en aktivitet. Mer information finns i [lagra autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Data kryptering under överföring
Om moln data lagringen stöder HTTPS eller TLS, är alla data överföringar mellan tjänster för data förflyttning i Data Factory och ett moln data lager via säker kanal HTTPS eller TLS.

> [!NOTE]
> Alla anslutningar till Azure SQL Database och Azure SQL Data Warehouse Kräv kryptering (SSL/TLS) medan data överförs till och från databasen. När du redigerar en pipeline med hjälp av JSON lägger du till egenskapen kryptering och anger den till **True** i anslutnings strängen. För Azure Storage kan du använda **https** i anslutnings strängen.

> [!NOTE]
> Om du vill aktivera kryptering i överföring samtidigt som du flyttar data från Oracle följer du något av alternativen nedan:
> 1. I Oracle-server går du till Oracle Advanced Security (OAS) och konfigurerar krypterings inställningarna, som stöder 3DES (Triple-DES Encryption) och Advanced Encryption Standard (AES). mer information finns [här](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759) . ADF förhandlar automatiskt om krypterings metoden för att använda den som du konfigurerar i OAS när du upprättar en anslutning till Oracle.
> 2. I ADF kan du lägga till EncryptionMethod = 1 i anslutnings strängen (i den länkade tjänsten). Detta använder SSL/TLS som krypterings metod. Om du vill använda detta måste du inaktivera krypterings inställningar som inte är SSL i OAS på Oracle-servern för att undvika krypterings konflikt.

> [!NOTE]
> Den TLS-version som används är 1,2.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa data lager stöder kryptering av data i vila. Vi rekommenderar att du aktiverar data krypterings mekanismen för dessa data lager. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent datakryptering (TDE) i Azure SQL Data Warehouse skyddar mot hot mot skadlig aktivitet genom att utföra kryptering och dekryptering i real tid av dina data i vila. Det här beteendet är transparent för klienten. Mer information finns [i skydda en databas i SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database stöder även transparent data kryptering (TDE), som hjälper till att skydda mot hot från skadlig aktivitet genom att utföra kryptering och dekryptering i real tid av data, utan att kräva ändringar i programmet. Det här beteendet är transparent för klienten. Mer information finns i [transparent data kryptering för SQL Database och informations lager](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store innehåller även kryptering för data som lagras i kontot. När den är aktive rad krypteras Data Lake Store automatiskt data innan de behålls och dekrypteras innan de kan hämtas, vilket gör det transparent för klienten som har åtkomst till data. Mer information finns [i säkerhet i Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage och Azure Table Storage
Azure Blob Storage och Azure Table Storage stöder Kryptering för lagringstjänst (SSE), som automatiskt krypterar dina data innan de sparas och dekrypteras innan de kan hämtas. Mer information finns i [Azure Storage tjänst kryptering för vilande data](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klient-och server kryptering av data i vila. Mer information finns i [skydda data med hjälp av kryptering](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon RedShift har stöd för kluster kryptering för vilande data. Mer information finns i [Amazon RedShift Database](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)Encryption. 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Avskärmnings plattforms kryptering som tillåter kryptering av alla filer, bilagor och anpassade fält. Mer information finns i [Förstå Web Server OAuth authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Hybrid scenarier
Hybrid scenarier kräver att integration runtime med egen värd installeras i ett lokalt nätverk, i ett virtuellt nätverk (Azure) eller i ett virtuellt privat moln (Amazon). Integration runtime med egen värd måste kunna komma åt lokala data lager. Mer information om integration runtime med egen värd finns i [så här skapar och konfigurerar du integration runtime med egen värd](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![kanaler för integration runtime med egen värd](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kommando kanalen tillåter kommunikation mellan data flyttnings tjänster i Data Factory och integration runtime med egen värd. Kommunikationen innehåller information som rör aktiviteten. Data kanalen används för att överföra data mellan lokala data lager och moln data lager.    

### <a name="on-premises-data-store-credentials"></a>Autentiseringsuppgifter för lokal data lager
Autentiseringsuppgifterna för dina lokala data lager krypteras och lagras alltid. De kan antingen lagras lokalt på den lokala datorn för integration runtime eller lagras i Azure Data Factory hanterade lagrings enheter (precis som autentiseringsuppgifter för moln arkivet). 

- **Lagra autentiseringsuppgifter lokalt**. Om du vill kryptera och lagra autentiseringsuppgifter lokalt på integration runtime med egen värd följer du stegen i [kryptera autentiseringsuppgifter för lokala data lager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md). Alla anslutningar har stöd för det här alternativet. Den egna värdbaserade integrerings körningen använder Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) för att kryptera känsliga data och autentiseringsinformation. 

   Använd cmdleten **New-AzDataFactoryV2LinkedServiceEncryptedCredential** för att kryptera länkade tjänst referenser och känslig information i den länkade tjänsten. Du kan sedan använda den JSON som returnerades (med **EncryptedCredential** -elementet i anslutnings strängen) för att skapa en länkad tjänst genom att använda cmdleten **set-AzDataFactoryV2LinkedService** .  

- **Lagra i Azure Data Factory hanterad lagring**. Om du direkt använder cmdleten **set-AzDataFactoryV2LinkedService** med anslutnings strängarna och AUTENTISERINGSUPPGIFTERNA i JSON, krypteras den länkade tjänsten och lagras i Azure Data Factory hanterad lagring. Känslig information krypteras fortfarande av certifikat och Microsoft hanterar dessa certifikat.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portar som används vid kryptering av länkad tjänst i integration runtime med egen värd
Som standard använder PowerShell port 8060 på datorn med integration runtime med egen värd för säker kommunikation. Vid behov kan den här porten ändras.  

![HTTPS-port för gatewayen](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla data överföringar sker via säker kanal-HTTPS och TLS över TCP för att förhindra man-in-the-middle-attacker under kommunikation med Azure-tjänster.

Du kan också använda [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Azure ExpressRoute](../expressroute/expressroute-introduction.md) för att ytterligare skydda kommunikations kanalen mellan ditt lokala nätverk och Azure.

Azure Virtual Network är en logisk representation av ditt nätverk i molnet. Du kan ansluta ett lokalt nätverk till ditt virtuella nätverk genom att konfigurera IPSec VPN (plats-till-plats) eller ExpressRoute (privat peering).    

I följande tabell sammanfattas konfigurations rekommendationerna för nätverk och lokal integrerings körning baserat på olika kombinationer av käll-och mål platser för Hybrid data förflyttning.

| Source      | Mål                              | Nätverkskonfiguration                    | Installation av Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokalt | Virtuella datorer och moln tjänster som distribueras i virtuella nätverk | IPSec VPN (punkt-till-plats eller plats-till-plats) | Integration runtime med egen värd bör installeras på en virtuell Azure-dator i det virtuella nätverket.  |
| Lokalt | Virtuella datorer och moln tjänster som distribueras i virtuella nätverk | ExpressRoute (privat peering)           | Integration runtime med egen värd bör installeras på en virtuell Azure-dator i det virtuella nätverket.  |
| Lokalt | Azure-baserade tjänster som har en offentlig slut punkt | ExpressRoute (Microsoft-peering)            | Integration runtime med egen värd kan installeras lokalt eller på en virtuell Azure-dator. |

I följande avbildningar visas användningen av integration runtime med egen värd för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av ExpressRoute och IPSec VPN (med Azure Virtual Network):

**ExpressRoute**

![Använda ExpressRoute med Gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN**

![IPSec VPN med Gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Brand Väggs konfigurationer och vit listning IP-adresser

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Brand Väggs krav för lokalt/privat nätverk  
I ett företag körs en företags brand vägg på den centrala routern i organisationen. Windows-brandväggen körs som en daemon på den lokala datorn där den lokala integrerings körningen är installerad. 

Följande tabell innehåller utgående port-och domän krav för företags brand väggar:

| Domännamn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av integration runtime med egen värd för att ansluta till data flytt tjänster i Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. |
| `download.microsoft.com`    | 443            | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. Om du har inaktiverat automatisk uppdatering kan du hoppa över detta. |
| `*.core.windows.net`          | 443            | Används av integration runtime med egen värd för att ansluta till Azure Storage-kontot när du använder funktionen för mellanlagrad [kopiering](copy-activity-performance.md#staged-copy) . |
| `*.database.windows.net`      | 1433           | Valfritt Krävs när du kopierar från eller till Azure SQL Database eller Azure SQL Data Warehouse. Använd funktionen för mellanlagrad kopiering för att kopiera data till Azure SQL Database eller Azure SQL Data Warehouse utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Valfritt Krävs när du kopierar från eller till Azure Data Lake Store. |

> [!NOTE] 
> Du kanske måste hantera portar eller vit listning-domäner på företags brand Väggs nivån som krävs av respektive data källa. I den här tabellen används endast Azure SQL Database, Azure SQL Data Warehouse och Azure Data Lake Store som exempel.   

Följande tabell innehåller krav på inkommande portar för Windows-brand väggen:

| Ingående portar | Beskrivning                              |
| ------------- | ---------------------------------------- |
| 8060 (TCP)    | Krävs av PowerShell-krypterings-cmdleten enligt beskrivningen i [kryptera autentiseringsuppgifter för lokala data lager i Azure Data Factory](encrypt-credentials-self-hosted-integration-runtime.md)och av Credential Manager-programmet för att på ett säkert sätt ange autentiseringsuppgifter för lokala data lager på egen värd integration Runtime. |

![Krav för Gateway-port](media/data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>IP-konfigurationer och vit listning i data lager
Vissa data lager i molnet kräver också att du vitlista IP-adressen för datorn som ansluter till butiken. Kontrol lera att IP-adressen för den egen värdbaserade integration runtime-datorn är vit listas eller konfigurerad i brand väggen på lämpligt sätt.

Följande moln data lager kräver att du vitlista IP-adressen för den egen värdbaserade integration runtime-datorn. Vissa av dessa data lager kan som standard inte kräva vit listning. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Kan den egen värdbaserade integrerings körningen delas mellan olika data fabriker?**

Ja. Mer information finns [här](https://azure.microsoft.com/blog/sharing-a-self-hosted-integration-runtime-infrastructure-with-multiple-data-factories/).

**Vilka är port kraven för integration runtime med egen värd att fungera?**

Den egen värdbaserade integrerings körningen gör HTTP-baserade anslutningar till Internet. De utgående portarna 443 måste öppnas för integration runtime med egen värd för att ansluta. Öppna endast inkommande port 8060 på dator nivå (inte företags brand Väggs nivå) för Autentiseringshanteraren-program. Om Azure SQL Database eller Azure SQL Data Warehouse används som källa eller mål, behöver du även öppna port 1433. Mer information finns i avsnittet om [brand Väggs konfiguration och vit listning IP-adresser](#firewall-configurations-and-whitelisting-ip-address-of-gateway) . 


## <a name="next-steps"></a>Nästa steg
Information om hur du Azure Data Factory kopiera aktivitets prestanda finns i avsnittet [Kopiera aktivitets prestanda och justering](copy-activity-performance.md).

 
