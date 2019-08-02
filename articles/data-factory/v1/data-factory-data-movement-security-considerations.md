---
title: Säkerhets överväganden för data förflyttning i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att skydda data flyttning i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b425db761375c705d3c810002234a937bac46d78
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610164"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory-säkerhets överväganden för data förflyttning

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [säkerhets aspekter för data flyttning för Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introduktion
Den här artikeln beskriver grundläggande säkerhets infrastruktur som tjänster för data flyttning i Azure Data Factory använder för att skydda dina data. Azure Data Factory hanterings resurser bygger på Azures säkerhets infrastruktur och använder alla möjliga säkerhets åtgärder som erbjuds av Azure.

I en Data Factory-lösning skapar du en eller flera data[pipelines](data-factory-create-pipelines.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipeliner finns i den region där data fabriken skapades. 

Även om Data Factory är endast tillgängligt i regionerna **västra USA**, **östra USA**och **norra Europa** , är tjänsten för data flyttning tillgänglig [globalt i flera regioner](data-factory-data-movement-activities.md#global). Data Factory tjänsten säkerställer att data inte lämnar något geografiskt område/region, om du inte uttryckligen instruerar tjänsten att använda en alternativ region om data flyttnings tjänsten ännu inte har distribuerats till den regionen. 

Azure Data Factory lagrar inga data utom länkade autentiseringsuppgifter för tjänsten för moln data lager som är krypterade med certifikat. Du kan använda den för att skapa datadrivna arbetsflöden som samordnar flödet av data mellan [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) och bearbetning av data med hjälp av [beräkningstjänster](data-factory-compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder.

Data förflyttning med Azure Data Factory har **certifierats** för:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Om du är intresse rad av Azure-kompatibilitet och hur Azure skyddar sin egen infrastruktur kan du gå till [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

I den här artikeln granskar vi säkerhets aspekter i följande två scenarier för data förflyttning: 

- **Moln scenario**– i det här scenariot är både källa och mål offentligt tillgängliga via Internet. Dessa omfattar hanterade moln lagrings tjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-tjänster som Salesforce och webb protokoll som FTP och OData. Du hittar en fullständig lista över data källor som stöds [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hybrid scenario**– i det här scenariot är antingen källan eller målet bakom en brand vägg eller i ett lokalt företags nätverk eller så är data lagret i ett privat nätverk/virtuellt nätverk (oftast källan) och är inte offentligt tillgänglig. Databas servrar som är värdar för virtuella datorer omfattas också av det här scenariot.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Moln scenarier
### <a name="securing-data-store-credentials"></a>Skydda autentiseringsuppgifter för data lager
Azure Data Factory skyddar dina data lagrings uppgifter genom att **kryptera** dem med hjälp av **certifikat som hanteras av Microsoft**. Dessa certifikat roteras vartannat **år** (vilket omfattar förnyelse av certifikat och migrering av autentiseringsuppgifter). Dessa krypterade autentiseringsuppgifter lagras säkert i en **Azure Storage som hanteras av Azure Data Factory hanterings tjänster**. Mer information om Azure Storage säkerhet finns [Azure Storage säkerhets översikt](../../security/fundamentals/storage-overview.md).

### <a name="data-encryption-in-transit"></a>Data kryptering under överföring
Om moln data lagringen stöder HTTPS eller TLS, är alla data överföringar mellan tjänster för data förflyttning i Data Factory och ett moln data lager via säker kanal HTTPS eller TLS.

> [!NOTE]
> Alla anslutningar till **Azure SQL Database** och **Azure SQL Data Warehouse** alltid kräva kryptering (SSL/TLS) medan data överförs till och från databasen. När du redigerar en pipeline med hjälp av en JSON-redigerare, lägger du till egenskapen **kryptering** och anger den som **sann** i **anslutnings strängen**. När du använder [guiden Kopiera](data-factory-azure-copy-wizard.md), ställer guiden in den här egenskapen som standard. För **Azure Storage**kan du använda **https** i anslutnings strängen.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa data lager stöder kryptering av data i vila. Vi rekommenderar att du aktiverar data krypterings mekanismen för dessa data lager. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent datakryptering (TDE) i Azure SQL Data Warehouse hjälper till med att skydda mot hot mot skadlig aktivitet genom att utföra kryptering och dekryptering i real tid av dina data i vila. Det här beteendet är transparent för klienten. Mer information finns [i skydda en databas i SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database stöder även transparent data kryptering (TDE), som hjälper till att skydda mot hot från skadlig aktivitet genom att utföra kryptering och dekryptering i real tid av data utan att kräva ändringar i programmet. Det här beteendet är transparent för klienten. Mer information finns i [Transparent datakryptering med Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store tillhandahåller även kryptering för data som lagras i kontot. När den är aktive rad krypterar Data Lake Store automatiskt data innan de behålls och dekrypteras innan de kan hämtas, vilket gör det transparent för klienten som har åtkomst till data. Mer information finns [i säkerhet i Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage och Azure Table Storage
Azure Blob Storage och Azure Table Storage stöder Kryptering för lagringstjänst (SSE), som automatiskt krypterar dina data innan de sparas och dekrypteras innan de kan hämtas. Mer information finns i [Azure Storage tjänst kryptering för vilande data](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klient-och server kryptering av data i vila. Mer information finns i [skydda data med hjälp av kryptering](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). För närvarande stöder Data Factory inte Amazon S3 i ett virtuellt privat moln (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon RedShift har stöd för kluster kryptering för vilande data. Mer information finns i [Amazon RedShift Database](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html)Encryption. För närvarande stöder Data Factory inte Amazon-RedShift i en VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Avskärmnings plattforms kryptering som tillåter kryptering av alla filer, bilagor, anpassade fält. Mer information finns i [Förstå Web Server OAuth authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybrid scenarier (med Data Management Gateway)
Hybrid scenarier kräver Data Management Gateway installeras i ett lokalt nätverk eller i ett virtuellt nätverk (Azure) eller ett virtuellt privat moln (Amazon). Gatewayen måste kunna komma åt lokala data lager. Mer information om gatewayen finns [Data Management Gateway](data-factory-data-management-gateway.md). 

![Data Management Gateway kanaler](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kommando kanalen** tillåter kommunikation mellan data flyttnings tjänster i Data Factory och data Management Gateway. Kommunikationen innehåller information som rör aktiviteten. Data kanalen används för att överföra data mellan lokala data lager och moln data lager.    

### <a name="on-premises-data-store-credentials"></a>Autentiseringsuppgifter för lokal data lager
Autentiseringsuppgifterna för dina lokala data lager lagras lokalt (inte i molnet). De kan ställas in på tre olika sätt. 

- Använda **enkel text** (mindre säkert) via https från Azure Portal/guiden Kopiera. Autentiseringsuppgifterna skickas med oformaterad text till den lokala gatewayen.
- Med hjälp av **Java Script Cryptography Library från guiden Kopiera**.
- Använda **appen för att klicka på en gång baserat på autentiseringsuppgifter Manager**. Klicknings programmet körs på den lokala datorn som har åtkomst till gatewayen och anger autentiseringsuppgifter för data lagret. Det här alternativet och nästa är de säkraste alternativen. Credential Manager-appen som standard använder port 8050 på datorn med Gateway för säker kommunikation.  
- Använd [New-AzDataFactoryEncryptValue PowerShell-](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) cmdlet för att kryptera autentiseringsuppgifter. Cmdlet: en använder det certifikat som gatewayen är konfigurerad att använda för att kryptera autentiseringsuppgifterna. Du kan använda de krypterade autentiseringsuppgifterna som returnerades av denna cmdlet och lägga till den i **EncryptedCredential** -elementet för **ConnectionString** i JSON-filen som du använder med cmdleten [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) eller i JSON-kodfragmentet i Data Factory redigeraren i portalen. Det här alternativet och programmet Klicka på när är de säkraste alternativen. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript-baserad kryptering med kryptering
Du kan kryptera data lagrings uppgifterna med hjälp av [Java Script Cryptography Library](https://www.microsoft.com/download/details.aspx?id=52439) från [guiden Kopiera](data-factory-copy-wizard.md). När du väljer det här alternativet hämtar kopierings guiden den offentliga nyckeln till gatewayen och använder den för att kryptera autentiseringsuppgifterna för data arkivet. Autentiseringsuppgifterna dekrypteras av gateway-datorn och skyddas av Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Webbläsare som stöds:** IE8, IE9, IE10, IE11, Microsoft Edge och de senaste Firefox-, Chrome-, Opera-och Safari-webbläsarna. 

#### <a name="click-once-credentials-manager-app"></a>Klicka på en gång som hanterar appen för autentiseringsuppgifter
Du kan starta appen klicknings-based Credential Manager från Azure Portal/guiden Kopiera när du redigerar pipeliner. Det här programmet säkerställer att autentiseringsuppgifterna inte överförs i klartext via kabeln. Som standard använder den port **8050** på datorn med Gateway för säker kommunikation. Vid behov kan den här porten ändras.  
  
![HTTPS-port för gatewayen](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

För närvarande använder Data Management Gateway ett enda **certifikat**. Det här certifikatet skapas under Gateway-installationen (gäller Data Management Gateway som skapats efter 2016 november och version 2.4. xxxx. x eller senare). Du kan ersätta det här certifikatet med ditt eget SSL/TLS-certifikat. Det här certifikatet används av programmet klickning-Once Credential Manager för att på ett säkert sätt ansluta till gateway-datorn för att ange autentiseringsuppgifter för data lager. Den lagrar autentiseringsuppgifter för data lager på ett säkert sätt lokalt genom att använda Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) på datorn med Gateway. 

> [!NOTE]
> Äldre gatewayer som installerades före november 2016 eller version 2.3. xxxx. x fortsätter att använda autentiseringsuppgifterna som är krypterade och lagrade i molnet. Även om du uppgraderar gatewayen till den senaste versionen migreras inte autentiseringsuppgifterna till en lokal dator    
  
| Gateway-version (vid skapande) | Lagrade autentiseringsuppgifter | Kryptering/säkerhet för autentiseringsuppgifter | 
| --------------------------------- | ------------------ | --------- |  
| < = 2,3. xxxx. x | I molnet | Krypterad med certifikat (skiljer sig från den som används av Autentiseringshanteraren) | 
| > = 2.4. xxxx. x | Lokalt | Säkra via DPAPI | 
  

### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla data överföringar sker via säker kanal- **https** och **TLS över TCP** för att förhindra man-in-the-middle-attacker under kommunikation med Azure-tjänster.
 
Du kan också använda [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Express Route](../../expressroute/expressroute-introduction.md) för att ytterligare skydda kommunikations kanalen mellan ditt lokala nätverk och Azure.

Virtual Network är en logisk representation av ditt nätverk i molnet. Du kan ansluta ett lokalt nätverk till ditt virtuella Azure-nätverk (VNet) genom att konfigurera IPSec VPN (plats-till-plats) eller Express Route (privat peering)     

I följande tabell sammanfattas rekommendationerna för nätverks-och gateway-konfiguration baserat på olika kombinationer av käll-och mål platser för Hybrid data förflyttning.

| Source | Mål | Nätverkskonfiguration | Gateway-konfiguration |
| ------ | ----------- | --------------------- | ------------- | 
| Lokalt | Virtuella datorer och moln tjänster som distribueras i virtuella nätverk | IPSec VPN (punkt-till-plats eller plats-till-plats) | Gateway kan installeras antingen lokalt eller på en virtuell Azure-dator (VM) i VNet | 
| Lokalt | Virtuella datorer och moln tjänster som distribueras i virtuella nätverk | ExpressRoute (privat peering) | Gateway kan installeras antingen lokalt eller på en virtuell Azure-dator i VNet | 
| Lokalt | Azure-baserade tjänster som har en offentlig slut punkt | ExpressRoute (offentlig peering) | Gatewayen måste installeras lokalt | 

I följande bilder visas användningen av Data Management Gateway för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av Express Route och IPSec VPN (med Virtual Network):

**Express väg:**
 
![Använd Express Route med Gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN med Gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Brand Väggs konfigurationer och vit listning IP-adress för gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Brand Väggs krav för lokalt/privat nätverk  
I ett företag körs en **företags brand vägg** på den centrala routern i organisationen. Och **Windows-brandväggen** körs som en daemon på den lokala dator där gatewayen är installerad. 

Följande tabell innehåller **utgående port** -och domän krav för **företags brand väggen**.

| Domännamn | Utgående portar | Beskrivning |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Krävs av gatewayen för att ansluta till data flytt tjänster i Data Factory |
| `*.core.windows.net` | 443 | Används av gatewayen för att ansluta till Azure Storage-kontot när du använder funktionen för mellanlagrad [kopiering](data-factory-copy-activity-performance.md#staged-copy) . | 
| `*.frontend.clouddatahub.net` | 443 | Krävs av gatewayen för att ansluta till Azure Data Factorys tjänsten. | 
| `*.database.windows.net` | 1433   | (Valfritt) krävs när målet är Azure SQL Database/Azure SQL Data Warehouse. Använd funktionen för mellanlagrad kopiering för att kopiera data till Azure SQL Database/Azure SQL Data Warehouse utan att öppna port 1433. | 
| `*.azuredatalakestore.net` | 443 | (Valfritt) krävs när målet är Azure Data Lake Store | 

> [!NOTE] 
> Du kan behöva hantera portar/vit listning-domäner på företags brand Väggs nivån som krävs av respektive data källor. I den här tabellen används endast Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store som exempel.   

Följande tabell innehåller krav på **inkommande portar** för **Windows-brandväggen**.

| Ingående portar | Beskrivning | 
| ------------- | ----------- | 
| 8050 (TCP) | Krävs av Credential Manager-programmet för att på ett säkert sätt ange autentiseringsuppgifter för lokala data lager på gatewayen. | 

![Krav för Gateway-port](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-konfigurationer/vit listning i data lager
Vissa data lager i molnet kräver också vit listning IP-adress för den dator som har åtkomst till dem. Se till att IP-adressen för gateway-datorn är vit listas/konfigurerad i brand väggen på lämpligt sätt.

Följande moln data lager kräver vit listning av IP-adressen för gateway-datorn. Vissa av dessa data lager kräver som standard inte vit listning av IP-adressen. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Ifrågasätta** Kan gatewayen delas mellan olika data fabriker?
**Svarsfilen** Vi har inte stöd för den här funktionen ännu. Vi jobbar på det.

**Ifrågasätta** Vilka är port kraven för gatewayen att fungera?
**Svarsfilen** Gatewayen gör HTTP-baserade anslutningar till öppna Internet. De **utgående portarna 443 och 80** måste vara öppna för att gatewayen ska kunna ansluta. Öppna endast **inkommande Port 8050** på dator nivå (inte på företags brand Väggs nivå) för Autentiseringshanteraren-program. Om Azure SQL Database eller Azure SQL Data Warehouse används som källa/mål, måste du även öppna **1433** -porten. Mer information finns i avsnittet om [brand Väggs konfiguration och vit listning IP-adresser](#firewall-configurations-and-whitelisting-ip-address-of gateway) . 

**Ifrågasätta** Vad är certifikat krav för gateway?
**Svarsfilen** Den aktuella gatewayen kräver ett certifikat som används av Autentiseringshanteraren för att på ett säkert sätt ange autentiseringsuppgifter för data lagret. Det här certifikatet är ett självsignerat certifikat som skapas och konfigureras av Gateway-installationen. Du kan använda ditt eget TLS/SSL-certifikat i stället. Mer information finns i avsnittet om att [Klicka-en gång till en program referens hanterare](#click-once-credentials-manager-app) . 

## <a name="next-steps"></a>Nästa steg
Information om prestanda för kopierings aktivitet finns i [guiden Kopiera aktivitets prestanda och justering](data-factory-copy-activity-performance.md).

 
