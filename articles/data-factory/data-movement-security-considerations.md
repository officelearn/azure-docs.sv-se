---
title: "Säkerhetsaspekter i Azure Data Factory | Microsoft Docs"
description: "Beskriver grundläggande infrastruktur som flytt datatjänster i Azure Data Factory använder för att skydda dina data."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2017
ms.author: abnarain
ms.openlocfilehash: a69f3770184d94c481c1b78f23efa9e9c4fb31fa
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory - säkerhetsaspekter för dataflyttning
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-data-movement-security-considerations.md)
> * [Version 2 – förhandsversion](data-movement-security-considerations.md)

Den här artikeln beskriver grundläggande säkerhetsinfrastruktur flytt datatjänster i Azure Data Factory använder för att skydda dina data. Azure Data Factory-hanteringsresurser bygger på Azure säkerhetsinfrastruktur och Använd alla möjliga skyddsåtgärder som Azure erbjuder.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [data movement säkerhetsaspekter för Data Factory version 1](v1/data-factory-data-movement-security-considerations.md).

I en Data Factory-lösning skapar du en eller flera data[pipelines](concepts-pipelines-activities.md). En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Dessa pipelines finns i den region där datafabriken har skapats. 

Även om Data Factory finns bara på **östra USA**, **östra USA 2**, och **Västeuropa** regioner (version 2 preview) av data movement service är tillgänglig [globalt i flera regioner](concepts-integration-runtime.md#azure-ir). Om en av data movement service inte har distribuerats till den regionen som Data Factory-tjänsten säkerställer att data inte lämna ett geografiskt område / region om du uttryckligen instruera tjänsten för att använda en annan region. 

Azure Data Factory själva lagrar inte alla data utom länkade tjänsten autentiseringsuppgifter för molnet datalager som krypteras med certifikat. Du kan använda den för att skapa datadrivna arbetsflöden som samordnar flödet av data mellan [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) och bearbetning av data med hjälp av [beräkningstjänster](compute-linked-services.md) i andra regioner eller i en lokal miljö. Du kan också övervaka och hantera arbetsflöden med hjälp av SDK: er och Azure-Monitor.

Flytt av data med hjälp av Azure Data Factory har **certifierade** för:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STJÄRNA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Om du är intresserad av Azure efterlevnad och hur Azure skyddar sin egen infrastruktur, finns det [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx). 

Vi går igenom säkerhetsaspekter i följande scenarier för flytt av två data i den här artikeln: 

- **Scenariot**– i det här scenariot både käll- och mål som är offentligt tillgänglig via internet. Dessa inkluderar hanterade lagring molntjänster som Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift SaaS-tjänster, till exempel Salesforce och webbprotokoll, till exempel FTP och OData. Du hittar en fullständig lista över datakällor som stöds [här](copy-activity-overview.md#supported-data-stores-and-formats).
- **Hybridscenario**– i det här scenariot källan eller målet finns bakom en brandvägg eller i ett företagsnätverk lokalt eller data store är i ett privat nätverk eller virtuella nätverk (oftast källan) och inte är allmänt tillgänglig. Database-servrar som är värd för virtuella datorer också omfattas av det här scenariot.

## <a name="cloud-scenarios"></a>Scenarier för
###<a name="securing-data-store-credentials"></a>Att säkra autentiseringsuppgifterna för datalager
- Lagra krypterade autentiseringsuppgifter i Azure Data Factory hanterad store.

   Azure Data Factory skyddar autentiseringsuppgifterna för ditt datalager av **kryptera** dem med hjälp av **certifikat ska hanteras av Microsoft**. Dessa certifikat roteras varje **två år** (som inkluderar förnyelse av certifikat och migrering av autentiseringsuppgifter). Dessa krypterade autentiseringsuppgifter lagras på ett säkert sätt i en **Azure Storage hanteras av Azure Data Factory hanteringstjänster**. Mer information om säkerhet i Azure Storage [översikt över säkerheten i Azure Storage](../security/security-storage-overview.md).
- Spara autentiseringsuppgifter i Azure Key Vault 

   Nu kan du välja att spara data store autentiseringsuppgifter i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), därefter låta Azure Data Factory för att hämta vid körning av en aktivitet. Mer information finns i [Store autentiseringsuppgifter i Azure Key Vault](store-credentials-in-key-vault.md).

   > [!NOTE]
   > För närvarande endast [Dynamics connector](connector-dynamics-crm-office-365.md) stöder den här funktionen. 

### <a name="data-encryption-in-transit"></a>Datakryptering under överföring
Om datalagret molnet stöder HTTPS- eller TLS, alla data som överförs mellan flytt datatjänster i Data Factory och ett datalager i molnet är via en säker kanal HTTPS- eller TLS.

> [!NOTE]
> Alla anslutningar till **Azure SQL Database** och **Azure SQL Data Warehouse** alltid Kräv kryptering (SSL/TLS) när data är i överföringen till och från databasen. När du redigerar en pipeline med JSON, lägger du till den **kryptering** egenskap och ange det till **SANT** i den **anslutningssträngen**. För **Azure Storage**, kan du använda **HTTPS** i anslutningssträngen.

### <a name="data-encryption-at-rest"></a>Datakryptering i vila
Vissa data lagras stöd för kryptering av data i vila. Vi rekommenderar att du aktiverar data krypteringsmekanism för dessa databaser. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Transparent Data kryptering (TDE) i Azure SQL Data Warehouse hjälper med att skydda mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av data i vila. Det här beteendet är transparent för klienten. Mer information finns i [skydda en databas i SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database stöder också transparent datakryptering (TDE), vilket hjälper dig med att skydda mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av data utan att ändra till programmet. Det här beteendet är transparent för klienten. Mer information finns i [Transparent datakryptering med Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store ger också kryptering för data som lagras på kontot. När aktiverat, krypteras data innan beständighet automatiskt i Data Lake store och dekrypterar före hämtning, att det är transparent för klienten att komma åt data. Mer information finns i [säkerhet i Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage och Azure-tabellagring
Azure Blob Storage och Azure Table storage stöder Storage Service kryptering (SSE), som automatiskt krypterar dina data innan beständighet till lagring och dekrypterar före hämtning. Mer information finns i [Azure Storage Service-kryptering för Data i vila](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 stöder både klienten och servern kryptering av vilande data. Mer information finns i [skydda Data med hjälp av kryptering](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Data Factory stöder för närvarande inte Amazon S3 inuti en virtuell privat moln (Virtual PC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift stöder kluster kryptering för data i vila. Mer information finns i [Amazon Redshift databaskryptering](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Data Factory stöder för närvarande inte Amazon Redshift inuti en Virtual PC. 

#### <a name="salesforce"></a>Salesforce
Salesforce stöder Shield plattform kryptering som har stöd för kryptering av filer, bifogade filer, anpassade fält. Mer information finns i [förstå flöda till autentisering för Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-self-hosted-integration-runtime"></a>Hybridmoln (med automatisk värdbaserade integration runtime)
Hybridscenarier kräver själva värdbaserade integration runtime installeras i ett lokalt nätverk eller i ett virtuellt nätverk (Azure) eller ett virtuellt privat moln (Amazon). Automatisk värdbaserade integration runtime måste kunna få åtkomst till lokala datalager. Läs mer om automatisk värdbaserade integration runtime [egenvärdbaserat integrering runtime](create-self-hosted-integration-runtime.md). 

![automatisk värdbaserade runtime-integrationskanaler](media/data-movement-security-considerations/data-management-gateway-channels.png)

Den **kommandokanal** tillåter kommunikation mellan flytt datatjänster i Data Factory och automatisk värdbaserade integration körning. Kommunikationen innehåller information relaterad till aktivitet. Datakanalen används för att överföra data mellan lokala datalager och molnet dataarkiv.    

### <a name="on-premises-data-store-credentials"></a>Lokala autentiseringsuppgifterna för datalager
Autentiseringsuppgifterna för ditt lokala datalager alltid krypteras och lagras. Det kan antingen lagras lokalt på själva värdbaserade integration runtime datorn eller i Azure Data Factory hanterade lagring (precis som molnet store autentiseringsuppgifter). 

1. Du kan välja att **lagra autentiseringsuppgifter lokalt**. Om du vill kryptera och lagra autentiseringsuppgifter lokalt på själva värdbaserade integration runtime, följer du stegen i [kryptering av autentiseringsuppgifter på själva värdbaserade integration runtime](encrypt-credentials-self-hosted-integration-runtime.md). Alla kopplingar stöder det här alternativet. Automatisk värdbaserade integration runtime använder Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) att kryptera känsliga data / autentiseringsuppgifter information. 

   Använd **ny AzureRmDataFactoryV2LinkedServiceEncryptedCredential** för att kryptera autentiseringsuppgifterna för länkad tjänst / kryptera känslig information i den länkade tjänsten. Du kan sedan använda JSON returnerade (med **EncryptedCredential** element i den **connectionString**) att skapa en länkad tjänst av **Set-AzureRmDataFactoryV2LinkedSevrice**cmdlet.  

2. Om du inte använder **ny AzureRmDataFactoryV2LinkedServiceEncryptedCredential** som beskrivs i steget ovan och Använd i stället direkt **Set AzureRmDataFactoryV2LinkedSevrice** med anslutningen strängar – autentiseringsuppgifter infogad i JSON och sedan på den länkade tjänsten kommer att **krypterade och lagras i Azure Data Factory hanteras lagring**. Känslig information krypteras fortfarande av certifikat och dessa certifikat hanteras av Microsoft.



#### <a name="ports-used-during-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portar som används vid kryptera länkad tjänst på själva värdbaserade integration runtime
Som standard använder PowerShell porten **8050** på datorn med automatisk värdbaserade integration runtime för säker kommunikation. Om det behövs kan du ändra den här porten.  

![HTTPS-port för gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Kryptering under överföring
Alla dataöverföringar är via en säker kanal **HTTPS** och **TLS via TCP** att förhindra att man-in-the-middle-attacker under kommunikationen med Azure-tjänster.

Du kan också använda [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) eller [Express Route](../expressroute/expressroute-introduction.md) att ytterligare skydda kommunikationskanalen mellan ditt lokala nätverk och Azure.

Virtuellt nätverk är en logisk representation av ditt nätverk i molnet. Du kan ansluta ett lokalt nätverk till Azure virtuella nätverk (VNet) genom att skapa IPSec-VPN (plats-till-plats) eller Express Route (privat Peering)     

I följande tabell sammanfattas nätverket och automatisk värdbaserade integration runtime rekommendationer baserat på olika kombinationer av käll- och platser för hybrid dataflyttning.

| Källa      | Mål                              | Nätverkskonfiguration                    | Integration runtime installationen                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokal | Virtuella datorer och molntjänster som är distribuerad i virtuella nätverk | IPSec-VPN (punkt-till-plats eller plats-till-plats) | Själva värdbaserade integration körning kan installeras antingen lokalt eller på en virtuell Azure dator (VM) i virtuella nätverk |
| Lokal | Virtuella datorer och molntjänster som är distribuerad i virtuella nätverk | ExpressRoute (privat Peering)           | Själva värdbaserade integration körning kan installeras antingen lokalt eller på en Azure VM i VNet |
| Lokal | Azure-baserade tjänster som har en offentlig slutpunkt | ExpressRoute (offentlig Peering)            | Automatisk värdbaserade integration runtime måste vara installerad lokalt |

Följande bilder visar användningen av själva värdbaserade integration runtime för att flytta data mellan en lokal databas och Azure-tjänster med hjälp av expressroute- och IPSec-VPN (med virtuella nätverk):

**Expressroute:**

![Använda Expressroute med gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec-VPN:**

![IPSec VPN med gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-self-hosted-integration-runtime"></a>Brandväggskonfigurationer och vitlistning av IP-adress (automatisk värdbaserade integration runtime)

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Krav för brandväggen för för lokala/privat nätverk  
Företag kan en **företagsbrandvägg** körs på den centrala routern för organisationen. Och, **Windows-brandväggen** körs som en daemon på den lokala datorn där själva värdbaserade integration runtime är installerad. 

Följande tabell innehåller **utgående port** och domänen för den **företagsbrandvägg**.

| Domännamn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443, 80        | Krävs av själva värdbaserade integration körningsmiljön att ansluta till data movement tjänster i Data Factory |
| `*.core.windows.net`          | 443            | Används av själva värdbaserade integration körningen för att ansluta till Azure Storage-konto när du använder den [mellanlagrad kopiera](copy-activity-performance.md#staged-copy) funktion. |
| `*.frontend.clouddatahub.net` | 443            | Krävs av själva värdbaserade integration körningen för att ansluta till Azure Data Factory-tjänsten. |
| `*.database.windows.net`      | 1433           | (Valfritt) krävs när målet är Azure SQL Database / Azure SQL Data Warehouse. Använda kopieringsfunktionen mellanlagrade för att kopiera data till Azure SQL Database-/ Azure SQL Data Warehouse utan att öppna port 1433. |
| `*.azuredatalakestore.net`    | 443            | (Valfritt) krävs när målet är Azure Data Lake store |

> [!NOTE] 
> Du kan behöva hantera portar / vitlistning domäner på företagets brandvägg nivå som krävs av respektive datakällor. Den här tabellen använder bara Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store som exempel.   

Följande tabell innehåller **inkommande port** kraven för den **Windows-brandväggen**.

| Ingående portar | Beskrivning                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Krävs för kryptering i PowerShell-cmdlet som beskrivs i [kryptering av autentiseringsuppgifter på själva värdbaserade integration runtime](encrypt-credentials-self-hosted-integration-runtime.md)/ credential manager-program för att ange autentiseringsuppgifter för lokala data lagras på ett säkert sätt på själva värdbaserade Integration runtime. |

![Krav för gateway-port](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurationswhitelisting-in-data-store"></a>IP-konfigurationer eller avvisa i datalagret
Vitlistning av IP-adressen för den datorn åtkomst till dem kräver dessutom att vissa data lagras i molnet. Se till att IP-adressen för automatisk värdbaserade integration runtime-datorn är godkända / konfigurerad i brandväggen.

Följande molntjänster datalager kräver vitlistning av IP-adressen för automatisk värdbaserade integration runtime-datorn. Vissa av dessa datalager som standard kan inte kräva vitlistning av IP-adressen. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Fråga:** kan själva värdbaserade integration runtime delas mellan olika datafabriker?
**Svar:** vi stöder inte den här funktionen ännu. Vi arbetar aktivt på den.

**Fråga:** vilka är portkraven på för automatisk värdbaserade integration runtime ska fungera?
**Svar:** själva värdbaserade integration runtime gör HTTP-baserade anslutningar för att öppna internet. Den **utgående portarna 443 och 80** måste vara öppna för automatisk värdbaserade integration runtime att upprätta anslutningen. Öppna **inkommande Port 8050** endast på datornivå (inte på företagets brandvägg nivån) för Autentiseringshanteraren program. Om Azure SQL Database eller Azure SQL Data Warehouse kan användas som källa / mål och sedan måste du öppna **1433** samt port. Mer information finns i [konfigurationer och vitlistning av IP-adresser i brandväggen](#firewall-configurations-and-whitelisting-ip-address-of gateway) avsnitt. 


## <a name="next-steps"></a>Nästa steg
Information om prestanda för kopieringsaktiviteten finns [kopiera aktivitet prestanda och prestandajustering guiden](copy-activity-performance.md).

 
