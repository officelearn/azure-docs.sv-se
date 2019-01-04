---
title: Säkerhetsöverväganden för dataförflyttning i Azure Data Factory | Microsoft Docs
description: Lär dig mer om hur du skyddar dataförflyttning i Azure Data Factory.
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
ms.openlocfilehash: 197762255a1a693821b8416227b4abf52755eb31
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015754"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - säkerhetsöverväganden för dataförflyttning

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [säkerhetsöverväganden vid dataflytt för Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introduktion
Den här artikeln beskriver grundläggande säkerhetsinfrastruktur dataförflyttning i Azure Data Factory använder för att skydda dina data. Azure Data Factory-hanteringsresurser bygger på Azure säkerhetsinfrastruktur och använda alla möjliga skyddsåtgärder som erbjuds av Azure.

I en Data Factory-lösning skapar du en eller flera data[pipelines](data-factory-create-pipelines.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipelines finns i den region där datafabriken har skapats. 

Även om Data Factory bara finns i **västra USA**, **USA, östra**, och **Nordeuropa** regioner, av data movement service är tillgänglig [globalt i flera regioner](data-factory-data-movement-activities.md#global). Data Factory-tjänsten säkerställer att data inte lämnar ett geografiskt område / region, såvida inte du uttryckligen instruera tjänsten att använda en annan region om av data movement service inte har distribuerats till den regionen. 

Azure Data Factory själva lagrar inte alla data utom länkade autentiseringsuppgifter för molndatalager som krypteras med hjälp av certifikat. Du kan använda den för att skapa datadrivna arbetsflöden som samordnar flödet av data mellan [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) och bearbetning av data med hjälp av [beräkningstjänster](data-factory-compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också [övervaka och hantera arbetsflöden](data-factory-monitor-manage-pipelines.md) med både program- och användargränssnittsmetoder.

Dataförflyttning med Azure Data Factory har **certifierade** för:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Om du är intresserad av efterlevnad i Azure och hur Azure skyddar sin egen infrastruktur kan du gå till den [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

I den här artikeln ska granska vi säkerhetsaspekter i följande två data movement scenarier: 

- **Scenariot med molnet**– i det här scenariot både käll- och mål som är tillgängliga för allmänheten via internet. Dessa inkluderar hanterad lagring molntjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, SaaS-tjänster, till exempel Salesforce och webbprotokoll som FTP- och OData. Du hittar en fullständig lista över datakällor som stöds [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Scenario med hybridanvändning**– i det här scenariot källan eller målet finns bakom en brandvägg eller i ett lokalt företagsnätverk eller data store är i ett privat nätverk per virtuell nätverks-(oftast källan) och inte är allmänt tillgänglig. Database-servrar som körs på virtuella datorer omfattas också det här scenariot.

## <a name="cloud-scenarios"></a>Scenarier för molnet
### <a name="securing-data-store-credentials"></a>Att säkra autentiseringsuppgifter för datalagring
Azure Data Factory skyddar dina autentiseringsuppgifter för datalagring av **kryptera** dem med hjälp av **certifikat som hanteras av Microsoft**. Dessa certifikat roteras varje **två år** (som inkluderar förnyelse av certifikat och migreringen av autentiseringsuppgifter). Dessa krypterade autentiseringsuppgifter lagras på ett säkert sätt i en **Azure Storage hanteras av Azure Data Factory hanteringstjänster**. Mer information om Azure Storage-säkerhet finns [Säkerhetsöversikt för Azure Storage](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Datakryptering under överföring
Om molndatalagret stöder HTTPS- eller TLS, alla data som överförs mellan dataförflyttning i Data Factory och ett molndatalager är via säker kanal HTTPS- eller TLS.

> [!NOTE]
> Alla anslutningar till **Azure SQL Database** och **Azure SQL Data Warehouse** alltid filkryptering (SSL/TLS) medan datakommunikationen till och från databasen. När du redigerar en pipeline med en JSON-redigerare, lägger du till den **kryptering** egenskapen och ge den värdet **SANT** i den **anslutningssträngen**. När du använder den [Kopieringsguiden](data-factory-azure-copy-wizard.md), guiden anger den här egenskapen som standard. För **Azure Storage**, du kan använda **HTTPS** i anslutningssträngen.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa datalager stöd för kryptering av vilande data. Vi rekommenderar att du aktiverar mekanism för kryptering av data för dessa datalager. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent datakryptering (TDE) i Azure SQL Data Warehouse hjälper med att skydda mot skadlig aktivitet genom att utföra i realtid kryptering och dekryptering av dina data i vila. Det här beteendet är transparent för klienten. Mer information finns i [skydda en databas i SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database har även stöd för transparent datakryptering (TDE), som hjälper dig med att skydda mot skadlig aktivitet genom att utföra i realtid kryptering och dekryptering av data utan att kräva ändringar i programmet. Det här beteendet är transparent för klienten. Mer information finns i [Transparent datakryptering med Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store innehåller också kryptering för data som lagras i kontot. När aktiverat krypterar data före beständig lagring automatiskt i Data Lake store och dekrypterar innan hämtning, vilket gör det transparent till klienten åtkomst till data. Mer information finns i [säkerhet i Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage och Azure Table Storage
Azure Blob Storage och Azure Table storage stöder Storage Service Encryption (SSE), som automatiskt krypterar dina data före beständig lagring och dekrypterar före hämtning. Mer information finns i [Azure Storage Service Encryption för vilande Data](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klienten och servern kryptering av vilande data. Mer information finns i [skyddar Data med hjälp av kryptering](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Data Factory stöder för närvarande inte Amazon S3 i ett virtuellt privat moln (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift stöder kluster kryptering för vilande data. Mer information finns i [databaskryptering för Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Data Factory stöder för närvarande inte Amazon Redshift inuti en VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Shield plattform kryptering som har stöd för kryptering av alla filer, bifogade filer eller anpassade fält. Mer information finns i [förstå flöda till autentisering för Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Hybridscenarier (med Data Management Gateway)
Hybridscenarier kräver Data Management Gateway installeras i ett lokalt nätverk eller i ett virtuellt nätverk (Azure) eller ett virtuellt privat moln (Amazon). Gatewayen måste kunna komma åt lokala datalager. Mer information om gateway finns i [Data Management Gateway](data-factory-data-management-gateway.md). 

![Data Management Gateway kanaler](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

Den **kommandokanal** tillåter kommunikation mellan dataförflyttning i Data Factory och Gateway för datahantering. Kommunikationen innehåller information relaterad till aktivitet. Datakanalen används för att överföra data mellan lokala datalager och molndatalager.    

### <a name="on-premises-data-store-credentials"></a>Lokala autentiseringsuppgifter för datalagring
Autentiseringsuppgifterna för dina lokala datalager lagras lokalt (inte i molnet). De kan ställas in på tre olika sätt. 

- Med hjälp av **textformat** (mindre säkert) via HTTPS från Azure Portal / Kopieringsguiden. Autentiseringsuppgifterna skickas i klartext till en lokal gateway.
- Med hjälp av **kryptering med JavaScript-biblioteket från guiden Kopiera**.
- Med hjälp av **klickar du på-när baserat autentiseringsuppgifter manager app**. Klicka-när programmet körs på den lokala datorn som har åtkomst till gatewayen och anger autentiseringsuppgifter för datalagret. Det här alternativet och nästa kommando är de säkraste alternativen. Appen credential manager använder som standard port 8050 på datorn med gateway för säker kommunikation.  
- Använd [New AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) PowerShell-cmdlet för att kryptera autentiseringsuppgifterna. Cmdlet: en använder certifikatet som gatewayen är konfigurerad för att använda för att kryptera autentiseringsuppgifterna. Du kan använda de krypterade autentiseringsuppgifterna som returneras av denna cmdlet och lägger till den i **EncryptedCredential** elementet i den **connectionString** i JSON-filen som du använder med den [ New-AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) cmdlet eller i JSON-kodfragmentet i Data Factory Editor i portalen. Det här alternativet och klicka på-när programmet är de säkraste alternativen. 

#### <a name="javascript-cryptography-library-based-encryption"></a>JavaScript kryptografi biblioteksbaserad kryptering
Du kan kryptera autentiseringsuppgifter för datalagring med [kryptering med JavaScript-biblioteket](https://www.microsoft.com/download/details.aspx?id=52439) från den [Kopieringsguiden](data-factory-copy-wizard.md). När du väljer det här alternativet kan Kopieringsguiden hämtar den offentliga nyckeln för gateway och används för att kryptera autentiseringsuppgifter för datalagring. Autentiseringsuppgifterna dekrypteras av gateway-datorn och skyddas av Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Webbläsare som stöds:** IE8, IE9, IE10, IE11, Microsoft Edge och senaste Firefox, Chrome, Opera, Safari-webbläsare. 

#### <a name="click-once-credentials-manager-app"></a>Klicka på-en gång autentiseringsuppgifter manager-appen
Du kan starta Klicka-när baserat credential manager-appen från Azure portal/kopiera guiden när du redigerar pipelines. Det här programmet säkerställer att autentiseringsuppgifter inte överförs i klartext över nätverket. Som standard använder den porten **8050** på datorn med gateway för säker kommunikation. Om det behövs kan du ändra den här porten.  
  
![HTTPS-port för gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

För närvarande Data Management Gateway använder en enda **certifikat**. Det här certifikatet skapas under gatewayinstallationen av (gäller för Data Management Gateway som skapats efter November 2016 och version 2.4.xxxx.x eller senare). Du kan ersätta det här certifikatet med dina egna SSL/TLS-certifikat. Det här certifikatet används genom att klicka på-en gång credential manager-program för säker anslutning till gateway-datorn för att ange autentiseringsuppgifter för datalagring. Den lagrar data store autentiseringsuppgifter på ett säkert sätt på plats med hjälp av Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) på datorn med gatewayen. 

> [!NOTE]
> Äldre gatewayer som har installerats före November 2016 eller av version 2.3.xxxx.x fortsätta att använda autentiseringsuppgifter krypteras och lagras i molnet. Även om du uppgraderar en gateway till den senaste versionen migreras autentiseringsuppgifterna inte till en lokal dator    
  
| Gateway-versionen (under skapande) | Autentiseringsuppgifter lagras | Kryptering av autentiseringsuppgifter / säkerhet | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | I molnet | Krypterat med certifikat (skiljer sig från den som används av Credential manager app) | 
| > = 2.4.xxxx.x | Lokalt | Skyddad via DPAPI | 
  

### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla dataöverföringar är via säker kanal **HTTPS** och **TLS via TCP** att förhindra att man-in-the-middle-attacker under kommunikation med Azure-tjänster.
 
Du kan också använda [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Express Route](../../expressroute/expressroute-introduction.md) att ytterligare skydda kommunikationskanalen mellan ditt lokala nätverk och Azure.

Virtuellt nätverk är en logisk representation av ditt nätverk i molnet. Du kan ansluta ett lokalt nätverk till Azure-nätverk (VNet) genom att konfigurera IPSec-VPN (plats-till-plats) eller Express Route (privat Peering)     

I följande tabell sammanfattas de nätverk och gateway-konfigurationsrekommendationer baserat på olika kombinationer av käll- och målplatserna flytt av hybriddata.

| Källa | Mål | Nätverkskonfiguration | Installationsprogram för gateway |
| ------ | ----------- | --------------------- | ------------- | 
| Lokal | Virtuella datorer och molntjänster som distribuerats i virtuella nätverk | IPSec-VPN (punkt-till-plats eller plats-till-plats) | Det kan vara installeras antingen lokalt eller på en virtuell Azure-dator (VM) i ett virtuellt nätverk | 
| Lokal | Virtuella datorer och molntjänster som distribuerats i virtuella nätverk | ExpressRoute (privat Peering) | Det kan vara installerad antingen lokalt eller på en virtuell Azure-dator i ett virtuellt nätverk | 
| Lokal | Azure-baserade tjänster som har en offentlig slutpunkt | ExpressRoute (offentlig Peering) | Gatewayen måste vara installerade på plats | 

Följande bilder visar användningen av Data Management Gateway för att flytta data mellan en lokal databas och Azure-tjänster med expressroute- och IPSec-VPN (med virtuellt nätverk):

**Expressroute:**
 
![Använda Express Route med gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec-VPN:**

![IPSec VPN med gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Brandväggskonfigurationer och listan över tillåtna IP-adressen för gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Brandväggskrav för om-plats/privat nätverk  
Företag kan en **företagets brandvägg** körs på den centrala routern för organisationen. Och, **Windows-brandväggen** körs som en daemon på den lokala datorn där gatewayen är installerad. 

Följande tabell innehåller **utgående port** och domänen för den **företagets brandvägg**.

| Domännamn | Utgående portar | Beskrivning |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Krävs av gateway för att ansluta till dataförflyttning i Data Factory |
| `*.core.windows.net` | 443 | Används av gatewayen för att ansluta till Azure Storage-konto när du använder den [mellanlagrad kopiering](data-factory-copy-activity-performance.md#staged-copy) funktionen. | 
| `*.frontend.clouddatahub.net` | 443 | Krävs av gateway för att ansluta till Azure Data Factory-tjänsten. | 
| `*.database.windows.net` | 1433   | (Valfritt) behövs när målet är Azure SQL-databas / Azure SQL Data Warehouse. Använd funktionen mellanlagrad kopiering för att kopiera data till Azure SQL Database-/ Azure SQL Data Warehouse utan att öppna port 1433. | 
| `*.azuredatalakestore.net` | 443 | (Valfritt) behövs när målet är Azure Data Lake store | 

> [!NOTE] 
> Du kan behöva hantera portar / lista över tillåtna domäner på företagets brandvägg nivå som krävs av respektive datakällor. Den här tabellen använder bara Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store som exempel.   

Följande tabell innehåller **inkommande port** kraven för den **windows-brandväggen**.

| Ingående portar | Beskrivning | 
| ------------- | ----------- | 
| 8050 (TCP) | Krävs av Autentiseringshanteraren att ange autentiseringsuppgifter för lokala datalager på ett säkert sätt på gatewayen. | 

![Krav för gateway-port](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>IP-konfigurationer / listan över tillåtna program i data store
Vissa datalager i molnet kräver också vitlistning av IP-adressen för den datorn åtkomst till dem. Se till att IP-adressen för gateway-datorn är godkänd / korrekt konfigurerad i brandväggen.

För följande datalager i molnet kräver vitlistning av IP-adressen för gateway-datorn. Vissa av dessa datalager, som standard kan inte kräva vitlistning av IP-adress. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Fråga:** Gatewayen kan delas mellan olika datafabriker?
**Svar:** Vi stöder inte den här funktionen ännu. Vi arbetar på den.

**Fråga:** Vilka är portkraven för att gatewayen ska fungera?
**Svar:** Gateway gör HTTP-baserade anslutningar till öppet internet. Den **utgående portarna 443 och 80** måste vara tillgänglig för gateway att upprätta anslutningen. Öppna **inkommande Port 8050** endast på datornivå (inte på nivån för företagets brandvägg) för programmet Autentiseringshanteraren. Om Azure SQL Database eller Azure SQL Data Warehouse används som källa / mål och sedan måste du öppna **1433** samt port. Mer information finns i [konfigurationer och listan över tillåtna IP-adresser i brandväggen](#firewall-configurations-and-whitelisting-ip-address-of gateway) avsnittet. 

**Fråga:** Vilka är certifikatkraven för Gateway?
**Svar:** Aktuell gateway kräver ett certifikat som används av Autentiseringshanteraren för att på ett säkert sätt ställa in autentiseringsuppgifter för datalagring. Det här certifikatet är ett självsignerat certifikat skapas och konfigureras av gateway-installationen. Du kan använda din egen TLS / SSL-certifikat i stället. Mer information finns i [klickar du på-credential manager-program en gång](#click-once-credentials-manager-app) avsnittet. 

## <a name="next-steps"></a>Nästa steg
Läs om hur prestanda för kopieringsaktiviteten [kopiera aktivitet prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md).

 
