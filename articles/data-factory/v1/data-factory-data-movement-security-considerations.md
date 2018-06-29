---
title: Säkerhetsaspekter vid flytt av data i Azure Data Factory | Microsoft Docs
description: Lär dig mer om hur du skyddar flytt av data i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 222558a6596c676034e52812d3b2dd0c77e1466b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046909"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - säkerhetsaspekter för dataflyttning

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [data movement säkerhetsaspekter för Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introduktion
Den här artikeln beskriver grundläggande säkerhetsinfrastruktur flytt datatjänster i Azure Data Factory använder för att skydda dina data. Azure Data Factory-hanteringsresurser bygger på Azure säkerhetsinfrastruktur och Använd alla möjliga skyddsåtgärder som Azure erbjuder.

I en Data Factory-lösning skapar du en eller flera data[pipelines](data-factory-create-pipelines.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipelines finns i den region där datafabriken har skapats. 

Även om Data Factory finns bara på **västra USA**, **östra USA**, och **Nordeuropa** regioner, av data movement service är tillgänglig [globalt i flera regioner](data-factory-data-movement-activities.md#global). Data Factory-tjänsten garanterar att data inte lämna ett geografiskt område / region om du uttryckligen instruera tjänsten för att använda en annan region om av data movement service ännu inte har distribuerats till den regionen. 

Azure Data Factory själva lagrar inte alla data utom länkade tjänsten autentiseringsuppgifter för molnet datalager som krypteras med certifikat. Du kan använda den för att skapa datadrivna arbetsflöden som samordnar flödet av data mellan [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) och bearbetning av data med hjälp av [beräkningstjänster](data-factory-compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder.

Flytt av data med hjälp av Azure Data Factory har **certifierade** för:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STJÄRNA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Om du är intresserad av Azure efterlevnad och hur Azure skyddar sin egen infrastruktur, finns det [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

Vi går igenom säkerhetsaspekter i följande scenarier för flytt av två data i den här artikeln: 

- **Scenariot**– i det här scenariot både käll- och mål som är offentligt tillgänglig via internet. Dessa inkluderar hanterade lagring molntjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift SaaS-tjänster, till exempel Salesforce och webbprotokoll, till exempel FTP och OData. Du hittar en fullständig lista över datakällor som stöds [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Hybridscenario**– i det här scenariot källan eller målet finns bakom en brandvägg eller i ett företagsnätverk lokalt eller data store är i ett privat nätverk eller virtuella nätverk (oftast källan) och inte är allmänt tillgänglig. Database-servrar som är värd för virtuella datorer också omfattas av det här scenariot.

## <a name="cloud-scenarios"></a>Scenarier för
### <a name="securing-data-store-credentials"></a>Att säkra autentiseringsuppgifterna för datalager
Azure Data Factory skyddar autentiseringsuppgifterna för ditt datalager av **kryptera** dem med hjälp av **certifikat ska hanteras av Microsoft**. Dessa certifikat roteras varje **två år** (som inkluderar förnyelse av certifikat och migrering av autentiseringsuppgifter). Dessa krypterade autentiseringsuppgifter lagras på ett säkert sätt i en **Azure Storage hanteras av Azure Data Factory hanteringstjänster**. Mer information om säkerhet i Azure Storage [översikt över säkerheten i Azure Storage](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Datakryptering under överföring
Om datalagret molnet stöder HTTPS- eller TLS, alla data som överförs mellan flytt datatjänster i Data Factory och ett datalager i molnet är via en säker kanal HTTPS- eller TLS.

> [!NOTE]
> Alla anslutningar till **Azure SQL Database** och **Azure SQL Data Warehouse** alltid Kräv kryptering (SSL/TLS) när data är i överföringen till och från databasen. När du redigerar en pipeline som använder en JSON-redigerare, lägger du till den **kryptering** egenskap och ange det till **SANT** i den **anslutningssträngen**. När du använder den [guiden Kopiera](data-factory-azure-copy-wizard.md), guiden anges den här egenskapen som standard. För **Azure Storage**, kan du använda **HTTPS** i anslutningssträngen.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa data lagras stöd för kryptering av data i vila. Vi rekommenderar att du aktiverar data krypteringsmekanism för dessa databaser. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent Data kryptering (TDE) i Azure SQL Data Warehouse hjälper med att skydda mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av data i vila. Det här beteendet är transparent för klienten. Mer information finns i [skydda en databas i SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database stöder också transparent datakryptering (TDE), vilket hjälper dig med att skydda mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av data utan att ändra till programmet. Det här beteendet är transparent för klienten. Mer information finns i [Transparent datakryptering med Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store ger också kryptering för data som lagras på kontot. När aktiverat, krypteras data innan beständighet automatiskt i Data Lake store och dekrypterar före hämtning, att det är transparent för klienten att komma åt data. Mer information finns i [säkerhet i Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage och Azure-tabellagring
Azure Blob Storage och Azure Table storage stöder Storage Service kryptering (SSE), som automatiskt krypterar dina data innan beständighet till lagring och dekrypterar före hämtning. Mer information finns i [Azure Storage Service-kryptering för Data i vila](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klienten och servern kryptering av vilande data. Mer information finns i [skydda Data med hjälp av kryptering](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Data Factory stöder för närvarande inte Amazon S3 inuti en virtuell privat moln (Virtual PC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift stöder kluster kryptering för data i vila. Mer information finns i [Amazon Redshift databaskryptering](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Data Factory stöder för närvarande inte Amazon Redshift inuti en Virtual PC. 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Shield plattform kryptering som har stöd för kryptering av filer, bifogade filer, anpassade fält. Mer information finns i [förstå flöda till autentisering för Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridmoln (med Data Management Gateway)
Hybridscenarier kräver Data Management Gateway installeras i ett lokalt nätverk eller i ett virtuellt nätverk (Azure) eller ett virtuellt privat moln (Amazon). Gatewayen måste kunna få åtkomst till lokala datalager. Mer information om gateway finns [Data Management Gateway](data-factory-data-management-gateway.md). 

![Data Management Gateway kanaler](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

Den **kommandokanal** tillåter kommunikation mellan flytt datatjänster i Data Factory och Data Management Gateway. Kommunikationen innehåller information relaterad till aktivitet. Datakanalen används för att överföra data mellan lokala datalager och molnet dataarkiv.    

### <a name="on-premises-data-store-credentials"></a>Lokala autentiseringsuppgifterna för datalager
Autentiseringsuppgifterna för ditt lokala datalager lagras lokalt (inte i molnet). De kan ställas in i tre olika sätt. 

- Med hjälp av **oformaterad text** (mindre säkert) via HTTPS från Azure-portalen / guiden Kopiera. Autentiseringsuppgifterna skickas i klartext till lokala gateway.
- Med hjälp av **kryptering med JavaScript-bibliotek från guiden Kopiera**.
- Med hjälp av **klickar du på-när baserat autentiseringsuppgifter manager app**. Klicka på-när programmet körs på den lokala datorn som har åtkomst till gatewayen och anger autentiseringsuppgifter för datalagret. Det här alternativet och nästa visas de säkraste alternativ. Appen autentiseringsuppgifter manager använder som standard port 8050 på datorn med gateway för säker kommunikation.  
- Använd [ny AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) PowerShell-cmdlet för att kryptera autentiseringsuppgifterna. Cmdlet använder certifikatet som gatewayen har konfigurerats för att använda för att kryptera autentiseringsuppgifterna. Du kan använda de krypterade autentiseringsuppgifter som returneras av denna cmdlet och lägger till den i **EncryptedCredential** element i den **connectionString** i JSON-filen som du använder med den [ Nya AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) cmdlet eller i JSON-kodstycket i den Data Factory-redigeraren i portalen. Det här alternativet och klicka på-när programmet är de säkraste alternativen. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript kryptografi biblioteket-baserad kryptering
Du kan kryptera autentiseringsuppgifterna för datalager med [kryptering med JavaScript-bibliotek](https://www.microsoft.com/download/details.aspx?id=52439) från den [guiden Kopiera](data-factory-copy-wizard.md). När du väljer det här alternativet kan guiden Kopiera hämtar den offentliga nyckeln för gateway och används för att kryptera autentiseringsuppgifterna för datalager. Autentiseringsuppgifterna dekrypteras av gateway-datorn och skyddas av Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Webbläsare som stöds:** IE8, IE9, IE10, IE11, Microsoft Edge och senaste Firefox, Chrome, Opera Safari webbläsare. 

#### <a name="click-once-credentials-manager-app"></a>Klicka på – en gång autentiseringsuppgifter manager-appen
Du kan starta Klicka-när baserat autentiseringsuppgifter manager-appen från Azure portal/kopiera guiden när du redigerar pipelines. Det här programmet säkerställer att autentiseringsuppgifter inte överförs i klartext via kabel. Som standard används porten **8050** på datorn med gateway för säker kommunikation. Om det behövs kan du ändra den här porten.  
  
![HTTPS-port för gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Data Management Gateway används för närvarande, en enda **certifikat**. Det här certifikatet skapas under gatewayinstallationen av (gäller för Data Management Gateway som skapats efter November 2016 och version 2.4.xxxx.x eller senare). Du kan ersätta det här certifikatet med din egen SSL/TLS-certifikat. Det här certifikatet används genom att klicka på-autentiseringsuppgifter en gång manager-programmet för säker anslutning till gateway-datorn för att ange autentiseringsuppgifterna för datalager. Data sparas autentiseringsuppgifterna för datalager på ett säkert sätt lokalt med hjälp av Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) på datorn med gateway. 

> [!NOTE]
> Äldre gateways som har installerats före November 2016 eller version 2.3.xxxx.x fortsätta att använda autentiseringsuppgifter krypteras och lagras i molnet. Även om du uppgraderar gateway till den senaste versionen har autentiseringsuppgifterna inte migrerats till en lokal dator    
  
| Gateway-versionen (under generering) | Autentiseringsuppgifterna lagrades | Autentiseringsuppgifter kryptering / säkerhet | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | I molnet | Krypteras med certifikat (som skiljer sig från den som används av autentiseringsuppgifter manager app) | 
| > = 2.4.xxxx.x | Lokalt | Skyddad via DPAPI | 
  

### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla dataöverföringar är via en säker kanal **HTTPS** och **TLS via TCP** att förhindra att man-in-the-middle-attacker under kommunikationen med Azure-tjänster.
 
Du kan också använda [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Express Route](../../expressroute/expressroute-introduction.md) att ytterligare skydda kommunikationskanalen mellan ditt lokala nätverk och Azure.

Virtuellt nätverk är en logisk representation av ditt nätverk i molnet. Du kan ansluta ett lokalt nätverk till Azure virtuella nätverk (VNet) genom att skapa IPSec-VPN (plats-till-plats) eller Express Route (privat Peering)     

I följande tabell sammanfattas nätverks- och gateway-konfigurationsrekommendationer baserat på olika kombinationer av käll- och målplatserna för hybrid dataförflyttning.

| Källa | Mål | Nätverkskonfiguration | Installationsprogram för gateway |
| ------ | ----------- | --------------------- | ------------- | 
| Lokal | Virtuella datorer och molntjänster som är distribuerad i virtuella nätverk | IPSec-VPN (punkt-till-plats eller plats-till-plats) | Gateway kan installeras antingen lokalt eller på en virtuell Azure dator (VM) i virtuella nätverk | 
| Lokal | Virtuella datorer och molntjänster som är distribuerad i virtuella nätverk | ExpressRoute (privat Peering) | Gateway kan installeras antingen lokalt eller på en Azure VM i VNet | 
| Lokal | Azure-baserade tjänster som har en offentlig slutpunkt | ExpressRoute (offentlig Peering) | Gateway måste vara installerad lokalt | 

Följande bilder visar användningen av Data Management Gateway för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av expressroute- och IPSec-VPN (med virtuella nätverk):

**Expressroute:**
 
![Använda Expressroute med gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec-VPN:**

![IPSec VPN med gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Brandväggskonfigurationer och vitlistning av IP-adressen för gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Krav för brandväggen för för lokala/privat nätverk  
Företag kan en **företagsbrandvägg** körs på den centrala routern för organisationen. Och, **Windows-brandväggen** körs som en daemon på den lokala datorn där gatewayen har installerats. 

Följande tabell innehåller **utgående port** och domänen för den **företagsbrandvägg**.

| Domännamn | Utgående portar | Beskrivning |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Krävs av gateway för att ansluta till data movement tjänster i Data Factory |
| `*.core.windows.net` | 443 | Används av gateway för att ansluta till Azure Storage-konto när du använder den [mellanlagrad kopiera](data-factory-copy-activity-performance.md#staged-copy) funktion. | 
| `*.frontend.clouddatahub.net` | 443 | Krävs av gateway för att ansluta till Azure Data Factory-tjänsten. | 
| `*.database.windows.net` | 1433   | (Valfritt) krävs när målet är Azure SQL Database / Azure SQL Data Warehouse. Använda kopieringsfunktionen mellanlagrade för att kopiera data till Azure SQL Database-/ Azure SQL Data Warehouse utan att öppna port 1433. | 
| `*.azuredatalakestore.net` | 443 | (Valfritt) krävs när målet är Azure Data Lake store | 

> [!NOTE] 
> Du kan behöva hantera portar / vitlistning domäner på företagets brandvägg nivå som krävs av respektive datakällor. Den här tabellen använder bara Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store som exempel.   

Följande tabell innehåller **inkommande port** kraven för den **windows-brandväggen**.

| Ingående portar | Beskrivning | 
| ------------- | ----------- | 
| 8050 (TCP) | Krävs av autentiseringsuppgifter manager-program att ange autentiseringsuppgifter för lokala data lagras på ett säkert sätt på gateway. | 

![Krav för gateway-port](media\data-factory-data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-konfigurationer / vitlistning i data store
Vitlistning av IP-adressen för den datorn åtkomst till dem kräver dessutom att vissa data lagras i molnet. Se till att IP-adressen för gateway-datorn är godkända / konfigurerad i brandväggen.

Följande molntjänster datalager kräver vitlistning av IP-adressen för gateway-datorn. Vissa av dessa datalager som standard kan inte kräva vitlistning av IP-adressen. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Fråga:** kan gatewayen som delas mellan olika datafabriker?
**Svar:** vi stöder inte den här funktionen ännu. Vi arbetar aktivt på den.

**Fråga:** vilka är portkraven på för gateway att fungera?
**Svar:** Gateway gör HTTP-baserade anslutningar för att öppna internet. Den **utgående portarna 443 och 80** måste vara öppna för gateway att upprätta anslutningen. Öppna **inkommande Port 8050** endast på datornivå (inte på företagets brandvägg nivån) för Autentiseringshanteraren program. Om Azure SQL Database eller Azure SQL Data Warehouse kan användas som källa / mål och sedan måste du öppna **1433** samt port. Mer information finns i [konfigurationer och vitlistning av IP-adresser i brandväggen](#firewall-configurations-and-whitelisting-ip-address-of gateway) avsnitt. 

**Fråga:** vilka är certifikatkraven för Gateway?
**Svar:** aktuella gatewayen kräver ett certifikat som används av autentiseringsuppgifter manager-program för att ange autentiseringsuppgifterna för datalager på ett säkert sätt. Det här certifikatet är ett självsignerat certifikat som skapas och konfigureras av gateway-installationen. Du kan använda din egen TLS / SSL-certifikat i stället. Mer information finns i [klickar du på-manager-program på en gång autentiseringsuppgifter](#click-once-credentials-manager-app) avsnitt. 

## <a name="next-steps"></a>Nästa steg
Information om prestanda för kopieringsaktiviteten finns [kopiera aktivitet prestanda och prestandajustering guiden](data-factory-copy-activity-performance.md).

 
