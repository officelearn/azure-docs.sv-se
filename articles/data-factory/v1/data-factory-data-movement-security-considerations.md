---
title: Säkerhetsöverväganden för dataflyttning i Azure Data Factory
description: Läs mer om hur du skyddar datarörelser i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1f19d258531e5368238cba72c986aede3f4a64ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130829"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory – Säkerhetsaspekter för dataförflyttning

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [säkerhetsaspekter för dataflyttning för Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introduktion
I den här artikeln beskrivs grundläggande säkerhetsinfrastruktur som dataflyttningstjänster i Azure Data Factory använder för att skydda dina data. Azure Data Factory-hanteringsresurser bygger på Azure-säkerhetsinfrastruktur och använder alla möjliga säkerhetsåtgärder som erbjuds av Azure.

I en Data Factory-lösning skapar du en eller flera data[pipelines](data-factory-create-pipelines.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipelines finns i den region där datafabriken skapades. 

Även om Data Factory endast är tillgängligt i regioner i **västra USA,** **östra USA**och **Norra Europa** är dataflytttjänsten tillgänglig globalt i [flera regioner.](data-factory-data-movement-activities.md#global) Data Factory-tjänsten säkerställer att data inte lämnar ett geografiskt område/en geografisk region om du inte uttryckligen instruerar tjänsten att använda en alternativ region om dataflyttningstjänsten ännu inte har distribuerats till den regionen. 

Azure Data Factory själv lagrar inga data förutom länkade tjänstautentiseringsuppgifter för molndatalager, som krypteras med certifikat. Det låter dig skapa datadrivna arbetsflöden för att dirigera förflyttning av data mellan [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) och bearbetning av data med hjälp av [beräkningstjänster](data-factory-compute-linked-services.md) i andra regioner eller i en lokal miljö. Det låter dig också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med hjälp av både programmatiska och gränssnittsmekanismer.

Dataflyttning med Azure Data Factory har **certifierats** för:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STJÄRNA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Om du är intresserad av Azure-efterlevnad och hur Azure skyddar sin egen infrastruktur besöker du [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

I den här artikeln granskar vi säkerhetsöverväganden i följande två scenarier för dataflyttning: 

- **Molnscenario**- I det här scenariot är både din källa och destination allmänt tillgängliga via internet. Dessa inkluderar hanterade molnlagringstjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-tjänster som Salesforce och webbprotokoll som FTP och OData. Du hittar en fullständig lista över datakällor som stöds [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hybridscenario**– I det här fallet ligger antingen källan eller målet bakom en brandvägg eller i ett lokalt företagsnätverk eller så finns datalagret i ett privat nätverk/virtuellt nätverk (oftast källan) och är inte allmänt tillgängligt. Databasservrar som finns på virtuella datorer omfattas också av det här scenariot.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Molnscenarier
### <a name="securing-data-store-credentials"></a>Skydda autentiseringsuppgifter för datalager
Azure Data Factory skyddar dina autentiseringsuppgifter för datalagring genom **att kryptera** dem med hjälp av certifikat **som hanteras av Microsoft**. Dessa certifikat roteras **vartannat år** (vilket inkluderar förnyelse av certifikat och migrering av autentiseringsuppgifter). Dessa krypterade autentiseringsuppgifter lagras säkert i en **Azure Storage som hanteras av Azure Data Factory management services**. Mer information om Azure Storage-säkerhet finns i [Azure Storage Security Overview](../../security/fundamentals/storage-overview.md).

### <a name="data-encryption-in-transit"></a>Datakryptering under överföring
Om molndatalagret stöder HTTPS eller TLS är alla dataöverföringar mellan dataöverföringstjänster i Data Factory och ett molndatalager via säker kanal HTTPS eller TLS.

> [!NOTE]
> Alla anslutningar till **Azure SQL Database** och Azure SQL Data **Warehouse** kräver alltid kryptering (SSL/TLS) medan data överförs till och från databasen. När du skapar en pipeline med en JSON-redigerare lägger du till **krypteringsegenskapen** och ställer in den på **true** i **anslutningssträngen**. När du använder [kopieringsguiden](data-factory-azure-copy-wizard.md)anger guiden den här egenskapen som standard. För **Azure Storage**kan du använda **HTTPS** i anslutningssträngen.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa data lagrar stöder kryptering av data i vila. Vi föreslår att du aktiverar datakrypteringsmekanism för dessa datalager. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent datakryptering (TDE) i Azure SQL Data Warehouse hjälper till att skydda mot hotet om skadlig aktivitet genom att utföra kryptering i realtid och dekryptering av dina data i vila. Detta är transparent för klienten. Mer information finns [i Skydda en databas i SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database stöder också transparent datakryptering (TDE), vilket hjälper till att skydda mot hotet om skadlig aktivitet genom att utföra kryptering i realtid och dekryptering av data utan att kräva ändringar i programmet. Detta är transparent för klienten. Mer information finns i [Transparent datakryptering med Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store tillhandahåller också kryptering för data som lagras i kontot. När det är aktiverat krypterar DataSjöarkivet automatiskt data innan data sparas och dekrypteras före hämtning, vilket gör det transparent för klienten som använder data. Mer information finns [i Säkerhet i Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage och Azure Table Storage
Azure Blob Storage och Azure Table Storage stöder SSE (Storage Service Encryption), som automatiskt krypterar dina data innan de sparas i lagring och dekrypterar före hämtning. Mer information finns i [Azure Storage Service Encryption for Data at Rest](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klient- och serverkryptering av data i vila. Mer information finns i [Skydda data med kryptering](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). För närvarande stöder Data Factory inte Amazon S3 i ett virtuellt privat moln (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift stöder klusterkryptering för data i vila. Mer information finns i [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). För närvarande stöder Data Factory inte Amazon Redshift inuti en VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Shield Platform Encryption som tillåter kryptering av alla filer, bilagor, anpassade fält. Mer information finns [i Förstå webbserverns OAuth-autentiseringsflöde](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridscenarier (med datahanteringsgateway)
Hybridscenarier kräver att Data Management Gateway installeras i ett lokalt nätverk eller i ett virtuellt nätverk (Azure) eller ett virtuellt privat moln (Amazon). Gatewayen måste kunna komma åt de lokala datalager. Mer information om gatewayen finns i [Data Management Gateway](data-factory-data-management-gateway.md). 

![Kanaler för datahanteringsgateway](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kommandokanalen** tillåter kommunikation mellan dataflyttningstjänster i Data Factory och Data Management Gateway. Meddelandet innehåller information om aktiviteten. Datakanalen används för överföring av data mellan lokala datalager och molndatalager.    

### <a name="on-premises-data-store-credentials"></a>Autentiseringsuppgifter för lokalt datalager
Autentiseringsuppgifterna för dina lokala datalager lagras lokalt (inte i molnet). De kan ställas in på tre olika sätt. 

- Använda **oformaterad text** (mindre säker) via HTTPS från Azure Portal/ Copy Wizard. Autentiseringsuppgifterna skickas i oformaterad text till den lokala gatewayen.
- Använda **JavaScript-kryptografibibliotek från copy wizard**.
- Använda **appen för klickbaserade autentiseringsuppgifter**. Programmet klicka en gång körs på den lokala datorn som har åtkomst till gatewayen och anger autentiseringsuppgifter för datalagret. Det här alternativet och nästa är de säkraste alternativen. Appen Autentiseringsuppgifter använder som standard port 8050 på datorn med gateway för säker kommunikation.  
- Använd [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell cmdlet för att kryptera autentiseringsuppgifter. Cmdlet använder certifikatet som gatewayen är konfigurerad att använda för att kryptera autentiseringsuppgifterna. Du kan använda de krypterade autentiseringsuppgifterna som returneras av den här cmdleten och lägga till den i **elementet EncryptedCredential** i **anslutningenSträngning** i JSON-filen som du använder med cmdleten [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) eller i JSON-kodavsnittet i Data Factory Editor i portalen. Det här alternativet och klickprogrammet är de säkraste alternativen. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript-kryptering baserad kryptering baserad på kryptering
Du kan kryptera datalagringsuppgifter med [JavaScript-krypteringsbiblioteket](https://www.microsoft.com/download/details.aspx?id=52439) från [kopieringsguiden](data-factory-copy-wizard.md). När du väljer det här alternativet hämtar kopieringsguiden den offentliga nyckeln för gatewayen och använder den för att kryptera autentiseringsuppgifterna för datalager. Autentiseringsuppgifterna dekrypteras av gateway-datorn och skyddas av Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Webbläsare som stöds:** IE8, IE9, IE10, IE11, Microsoft Edge och senaste Firefox, Chrome, Opera, Safari webbläsare. 

#### <a name="click-once-credentials-manager-app"></a>Klicka en gång autentiseringshanterare app
Du kan starta den klickade autentiseringsuppgifterna från Azure Portal/Copy-guiden när du redigerar pipelines. Det här programmet säkerställer att autentiseringsuppgifter inte överförs i oformaterad text över kabeln. Som standard används porten **8050** på datorn med gateway för säker kommunikation. Om det behövs kan den här porten ändras.  
  
![HTTPS-port för gatewayen](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

För närvarande använder Data Management Gateway ett enda **certifikat**. Det här certifikatet skapas under gatewayinstallationen (gäller datahanteringsgateway som skapats efter november 2016 och version 2.4.xxxx.x eller senare). Du kan ersätta det här certifikatet med ditt eget SSL/TLS-certifikat. Det här certifikatet används av programmet för click-once-autentiseringsuppgifter för att på ett säkert sätt ansluta till gateway-datorn för att ange autentiseringsuppgifter för datalagring. Den lagrar autentiseringsuppgifter säkert lokalt med hjälp av Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) på datorn med gateway. 

> [!NOTE]
> Äldre gateways som installerades före november 2016 eller version 2.3.xxxx.x fortsätter att använda autentiseringsuppgifter krypterade och lagrade i molnet. Även om du uppgraderar gatewayen till den senaste versionen migreras inte autentiseringsuppgifterna till en lokal dator    
  
| Gateway-version (under skapandet) | Autentiseringsuppgifter lagrade | Kryptering/säkerhet för autentiseringsuppgifter | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | På moln | Krypterad med certifikat (skiljer sig från det som används av appen Autentiseringshanteraren) | 
| > = 2.4.xxxx.x | På plats | Säkrad via DPAPI | 
  

### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla dataöverföringar sker via säker kanal **HTTPS** och **TLS över TCP** för att förhindra man-in-the-middle-attacker under kommunikation med Azure-tjänster.
 
Du kan också använda [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Express Route](../../expressroute/expressroute-introduction.md) för att ytterligare skydda kommunikationskanalen mellan ditt lokala nätverk och Azure.

Virtuellt nätverk är en logisk representation av nätverket i molnet. Du kan ansluta ett lokalt nätverk till ditt virtuella Azure-nätverk (VNet) genom att konfigurera IPSec VPN (plats till plats) eller Express Route (Private Peering)     

I följande tabell sammanfattas konfigurationsrekommendationerna för nätverk och gateway baserat på olika kombinationer av käll- och målplatser för hybriddataförflyttning.

| Källa | Mål | Nätverkskonfiguration | Gateway-konfiguration |
| ------ | ----------- | --------------------- | ------------- | 
| Lokal | Virtuella datorer och molntjänster som distribueras i virtuella nätverk | IPSec VPN (punkt-till-plats eller plats-till-plats) | Gateway kan installeras antingen lokalt eller på en virtuell Azure-dator (VM) i VNet | 
| Lokal | Virtuella datorer och molntjänster som distribueras i virtuella nätverk | ExpressRoute (Privat peering) | Gateway kan installeras antingen lokalt eller på en Azure VM i VNet | 
| Lokal | Azure-baserade tjänster som har en offentlig slutpunkt | ExpressRoute (offentlig peering) | Gateway måste installeras lokalt | 

Följande avbildningar visar användningen av Data Management Gateway för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av Express-vägen och IPSec VPN (med virtuellt nätverk):

**Express rutt:**
 
![Använda Express Route med gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![IPSec VPN med gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Brandväggskonfigurationer och vitlistning av IP-adress för gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Brandväggskrav för lokalt/privat nätverk  
I ett företag körs en **företagsbrandvägg** på organisationens centrala router. Och **Windows-brandväggen** körs som en demon på den lokala datorn där gatewayen är installerad. 

I följande tabell finns **utgående port-** och domänkrav för **företagsbrandväggen**.

| Domännamn | Utgående portar | Beskrivning |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Krävs av gatewayen för att ansluta till datarörelsetjänster i Data Factory |
| `*.core.windows.net` | 443 | Används av gatewayen för att ansluta till Azure Storage-konto när du använder den [mellanslagna](data-factory-copy-activity-performance.md#staged-copy) kopieringsfunktionen. | 
| `*.frontend.clouddatahub.net` | 443 | Krävs av gatewayen för att ansluta till Azure Data Factory-tjänsten. | 
| `*.database.windows.net` | 1433   | (VALFRITT) som behövs när målet är Azure SQL Database/ Azure SQL Data Warehouse. Använd funktionen för stegvis kopia för att kopiera data till Azure SQL Database/Azure SQL Data Warehouse utan att öppna port 1433. | 
| `*.azuredatalakestore.net` | 443 | (VALFRITT) som behövs när din destination är Azure Data Lake Store | 

> [!NOTE] 
> Du kan behöva hantera portar/vitlistningsdomäner på företagets brandväggsnivå enligt kraven för respektive datakällor. Den här tabellen använder endast Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store som exempel.   

I följande tabell finns inkommande **portkrav** för **Windows-brandväggen**.

| Ingående portar | Beskrivning | 
| ------------- | ----------- | 
| 8050 (TCP) | Krävs av programmet för autentiseringsuppgifter för att på ett säkert sätt ange autentiseringsuppgifter för lokala datalager i gatewayen. | 

![Krav på gatewayport](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-konfigurationer/vitlistning i datalag
Vissa datalager i molnet kräver också vitlistning av IP-adressen för datorn som använder dem. Kontrollera att IP-adressen för gateway-datorn är vitlistad/konfigurerad i brandväggen på rätt sätt.

Följande molndatalager kräver vitlistning av IP-adressen för gateway-datorn. Vissa av dessa datalager kanske som standard inte kräver vitlistning av IP-adressen. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL-datalager](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Fråga:** Kan gatewayen delas mellan olika datafabriker?
**Svar:** Vi stöder inte den här funktionen ännu. Vi jobbar på det.

**Fråga:** Vilka är portkraven för att gatewayen ska fungera?
**Svar:** Gateway gör HTTP-baserade anslutningar för att öppna internet. De **utgående portarna 443 och 80** måste öppnas för gateway för att anslutningen ska kunna upprättas. Öppna **Inkommande port 8050** endast på datornivå (inte på företagets brandväggsnivå) för Programmet Autentiseringshanteraren. Om Azure SQL Database eller Azure SQL Data Warehouse används som källa/ mål måste du även öppna **1433-porten.** Mer information finns i [avsnittet Brandväggskonfigurationer och vitlistande IP-adresser.](#firewall-configurations-and-whitelisting-ip-address-of gateway) 

**Fråga:** Vad är certifikatkrav för Gateway?
**Svar:** Aktuell gateway kräver ett certifikat som används av programmet för autentiseringshanteraren för att korrekt ange autentiseringsuppgifter för datalagring. Det här certifikatet är ett självsignerat certifikat som skapats och konfigurerats av gateway-installationen. Du kan använda ditt eget TLS/SSL-certifikat i stället. Mer information finns i [programavsnittet för autentiseringshanteraren](#click-once-credentials-manager-app) när du är. 

## <a name="next-steps"></a>Nästa steg
Information om hur du prestanda för kopieringsaktivitet finns i [Kopiera aktivitetsprestanda och justeringsguide](data-factory-copy-activity-performance.md).

 
