---
title: Översikt över Azure kryptering | Microsoft Docs
description: Lär dig mer om olika alternativ för kryptering i Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 272cc843ab90eade06525f665d3cf2decf74a26f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444524"
---
# <a name="azure-encryption-overview"></a>Översikt över Azure-kryptering

Den här artikeln innehåller en översikt över hur kryptering används i Microsoft Azure. Den behandlar huvudområdena i kryptering, inklusive kryptering i vila, kryptering i flygning och nyckelhantering med Azure Key Vault. Varje avsnitt innehåller länkar till mer detaljerad information.

## <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data

Vilande data innehåller information som finns i permanent lagring på fysiska media i alla digitala format. Mediet kan innehålla filer på magnetiska eller optiska medier, arkiverade data och säkerhetskopiering av data. Microsoft Azure erbjuder en mängd olika lösningar för lagring att uppfylla olika behov, inklusive fil-, disk-, blob- och tabellagring. Microsoft tillhandahåller även kryptering för att skydda [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [Azure Cosmos DB](../cosmos-db/introduction.md), och Azure Data Lake.

Datakryptering i viloläge är tillgänglig för tjänster över programvara som en tjänst (SaaS), plattform som en tjänst (PaaS) och infrastruktur som en tjänst (IaaS) molnmodeller. Den här artikeln sammanfattar och innehåller resurser som hjälper dig att använda Azure krypteringsalternativen.

En mer detaljerad beskrivning av hur vilande data krypteras i Azure finns i [Azure Data kryptering vid vila](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Azure-kryptering modeller

Azure stöder olika kryptering modeller, inklusive kryptering för serversidan som använder service-hanterade nycklar, Kundhanterade nycklar i Key Vault eller Kundhanterade nycklar på kund-kontrollerade maskinvara. Med client side encryption kan du hantera och lagra nycklar på lokalt eller i en annan säker plats.

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Client side encryption utförs utanför Azure. Det innehåller:

- Data som krypterats av ett program som körs i kundens datacenter eller genom ett tjänstprogram.
- Data som redan är krypterad när den tas emot av Azure.

Med client side encryption leverantörer av molntjänster har inte åtkomst till krypteringsnycklarna och det går inte att dekryptera dessa data. Du kan ha fullständig kontroll över nycklarna.

### <a name="server-side-encryption"></a>Kryptering på serversidan

De tre modellerna server side encryption erbjuder olika nyckelhantering egenskaper, som du kan välja baserat på dina krav:

- **Service-hanterade nycklar**: Innehåller en kombination av kontroll och bekvämlighet med låg belastning.

- **Kundhanterade nycklar**: Ger dig kontroll över nycklar, inklusive stöd för Bring Your Own nycklar (BYOK), eller kan du generera nya.

- **Service-hanterade nycklar i kund-kontrollerade maskinvara**: Gör det möjligt för dig att hantera nycklar i centrallagret upphovsrättsskyddad utanför Microsoft kontroll. Denna variabel kallas värd Your Own Key (HYOK). Men konfigurationen är komplexa och de flesta Azure services stöder inte den här modellen.

### <a name="azure-disk-encryption"></a>Azure-diskkryptering

Du kan skydda Windows och Linux-datorer med hjälp av [Azure-diskkryptering](azure-security-disk-encryption.md), som använder [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) teknik- och Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) att skydda både operativsystemsdiskar och datadiskar med fullständig volymkryptering.

Krypteringsnycklar och hemligheter skyddas i din [Azure Key Vault-prenumeration](../key-vault/key-vault-whatis.md). Genom att använda Azure Backup-tjänsten kan du säkerhetskopiera och återställa krypterade virtuella datorer (VM) som använder nyckeln kryptering nyckel (KEK)-konfigurationen.

### <a name="azure-storage-service-encryption"></a>Kryptering av Azure-lagringstjänst

Vilande data i Azure Blob storage och Azure-filresurser kan vara krypterad på både serversidan och klientsidan.

[Azure Storage Service Encryption (SSE)](../storage/common/storage-service-encryption.md) kan automatiskt att kryptera data innan de lagras, och det automatiskt dekrypterar data när du hämtar den. Processen är helt transparent för användarna. Kryptering av lagringstjänst använder 256-bitars [Advanced Encryption Standard (AES) kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), vilket är en av de starkaste blockchiffer som är tillgängliga. AES hanterar transparent kryptering, dekryptering och nyckelhantering.

### <a name="client-side-encryption-of-azure-blobs"></a>Client side encryption för Azure-blobar

Du kan utföra på klientsidan kryptering av Azure BLOB-objekt på olika sätt.

Du kan använda Azure Storage-klientbiblioteket för .NET NuGet-paket för att kryptera data i ditt klientprogram innan de överförs till din Azure-lagring.

Om du vill lära dig mer om och ladda ned Azure Storage-klientbiblioteket för .NET NuGet-paket, se [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

När du använder client side encryption med Key Vault kan krypteras dina data med en enstaka symmetriska innehåll kryptering nyckel (CEK) som genereras av SDK för Azure Storage-klient. CEK är krypterat med en nyckel kryptering nyckel (KEK), vilket kan vara antingen en symmetrisk nyckel eller ett asymmetriskt nyckelpar. Du kan hantera den lokalt eller lagra den i Key Vault. Krypterade data överförs sedan till Azure Storage.

Om du vill veta mer om kryptering på klientsidan med Key Vault och kom igång med anvisningar Se [självstudien: Kryptera och dekryptera blobbar i Azure Storage med hjälp av Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Slutligen kan kan du också använda Azure Storage-klientbiblioteket för Java att utföra client side encryption innan du överför data till Azure Storage och för att dekryptera data när du laddar ned till klienten. Det här biblioteket stöder även integrering med [Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av nycklar i storage-konto.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Kryptering av data i vila med Azure SQL Database

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) är en allmän relationsdatabastjänst i Azure som stöder strukturer som relationsdata, JSON, rumsliga och XML. SQL Database stöder både server side encryption via funktionen Transparent datakryptering (TDE) och client side encryption via Always Encrypted-funktionen.

#### <a name="transparent-data-encryption"></a>Transparent datakryptering

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) används för att kryptera [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), och [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) datafiler i realtid, med en databas Datakrypteringsnyckeln (DEK) , som lagras i boot databaspost för tillgänglighet under återställningen.

TDE skyddar data och loggfiler med hjälp av AES och Triple Data Encryption Standard (3DES) krypteringsalgoritmer. Kryptering av databasfilen utförs på sidnivå. Sidorna i en krypterad databas krypteras innan de skrivs till disken och dekrypteras när de är läsas in i minnet. TDE är nu aktiverad som standard på nyligen skapade Azure SQL-databaser.

#### <a name="always-encrypted-feature"></a>Always Encrypted-funktionen

Med den [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funktionen i Azure SQL kan du kryptera data i klientprogram före lagring i Azure SQL Database. Du kan också aktivera delegering i den lokala databasadministration till tredje part och underhålla uppdelning mellan personer som äger och kan visa data och personer som hanterar den, men inte ska ha åtkomst till den.

#### <a name="cell-level-or-column-level-encryption"></a>Kryptering på cellnivå eller kolumnnivå

Med Azure SQL Database kan tillämpa du symmetrisk kryptering för en kolumn med data med hjälp av Transact-SQL. Den här metoden anropas [på cellnivå kryptering eller kolumnnivå kryptering (CLE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), eftersom du kan använda den för att kryptera vissa kolumner eller även vissa celler av data med olika krypteringsnycklar. Då får du mer detaljerade krypteringsfunktioner än TDE som krypterar data på sidor.

CLE har inbyggda funktioner som du kan använda för att kryptera data med hjälp av antingen symmetriskt eller asymmetriskt nycklar, den offentliga nyckeln för ett certifikat eller en lösenfras med hjälp av 3DES.

### <a name="cosmos-db-database-encryption"></a>Databaskryptering för cosmos DB

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) är Microsofts globalt distribuerad databas. Användardata som lagras i Cosmos DB i beständigt minne (SSD) är krypterad som standard. Det finns inga kontroller för att aktivera eller inaktivera. Kryptering i vila implementeras med hjälp av ett antal olika tekniker, inklusive säker nyckel lagringssystem, krypterade nätverk och kryptografiska API: er. Krypteringsnycklar hanteras av Microsoft och roteras per interna riktlinjer för Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Kryptering i vila i Data Lake

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) är en företagsomfattande lagringsplats för alla typer av data som samlas in i en enda plats innan några formellt definieras för krav eller schema. Data Lake Store stöder ”på som standard” transparent kryptering av vilande data, som har ställts in under genereringen av ditt konto. Azure Data Lake Store hanterar nycklarna åt dig som standard, men du har möjlighet att hantera dem själv.

Tre typer av nycklar som används i kryptera och dekryptera data: Huvudkrypteringsnyckeln (MEK), Data Datakrypteringsnyckeln (DEK) och blockera kryptering nyckel (BEK). Huvudkrypteringsnyckeln används för att kryptera DEK som lagras på permanenta media och BEK härleds från DEK och datablocket. Om du hanterar dina egna nycklar kan du rotera Huvudkrypteringsnyckeln.

## <a name="encryption-of-data-in-transit"></a>Kryptering av data under överföring

Azure erbjuder många metoder för att behålla data privata som flyttas från en plats till en annan.

### <a name="tlsssl-encryption-in-azure"></a>TLS/SSL-kryptering i Azure

Microsoft använder den [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokollet för att skydda data när den på resande fot mellan cloud services och kunder. Microsoft-datacenter att förhandla TLS-anslutning med klientdatorer som ansluter till Azure-tjänster. TLS ger stark autentisering, meddelandesekretess, och integritet (vilket möjliggör identifiering av meddelande manipulering avlyssning och förfalskning av meddelanden), samverkan, flexibla algoritmer och enkel distribution och användning.

[Förbättrar Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) skyddar anslutningar mellan kunders klientsystem och Microsofts molntjänster med unika nycklar. Anslutningar kan också använda RSA-baserade 2 048-bitars kryptering nyckellängder. Den här kombinationen gör det svårt för att skärningspunkt och komma åt data som överförs.

### <a name="azure-storage-transactions"></a>Azure Storage-transaktioner

När du interagerar med Azure Storage via Azure portal, sker alla transaktioner filer över HTTPS. Du kan också använda Storage REST API för att interagera med Azure Storage via HTTPS. Du kan framtvinga användningen av HTTPS när du anropar REST-API: er för åtkomst till objekt i storage-konton genom att aktivera säker överföring som krävs för lagringskontot.

Signaturer för delad åtkomst ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), som kan användas för att delegera åtkomst till Azure Storage-objekt, innehåller ett alternativ för att ange endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst. Den här metoden gör att vem som helst som skickar länkar med SAS-token använder rätt protokoll.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), vilket används för att komma åt Azure Files-resurser, har stöd för kryptering och den är tillgänglig i Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10. Det tillåter interregionala åtkomst och även komma åt på skrivbordet.

Client side encryption krypteras data innan den skickas till din Azure Storage-instans, så att den är krypterad när den skickas över nätverket.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-kryptering via Azure-nätverk 

Med hjälp av [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) i virtuella datorer som kör Windows Server 2012 eller senare kan du göra data transfers skydda genom att kryptera data under överföring via Azure-nätverk. Genom att kryptera data skyddar du mot manipulering och avlyssning attacker. Administratörer kan aktivera SMB-kryptering för hela servern eller bara vissa resurser.

När SMB-kryptering är aktiverat för en resurs eller en server, tillåts endast SMB 3.0-klienter att komma åt de krypterade resurserna.

## <a name="in-transit-encryption-in-vms"></a>Kryptering under överföring i virtuella datorer

Information som överförs till, från och mellan virtuella datorer som kör Windows krypteras på flera olika sätt beroende på typen av anslutningen.

### <a name="rdp-sessions"></a>RDP-sessioner

Du kan ansluta och logga in till en virtuell dator med hjälp av den [Remote Desktop Protocol (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) från en Windows-klientdator eller från en Mac-dator med en RDP-klient installerad. Information som överförs över nätverket i RDP-sessioner kan skyddas av TLS.

Du kan också använda Fjärrskrivbord för att ansluta till en Linux-VM i Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Säker åtkomst till virtuella Linux-datorer med SSH

Du kan använda för fjärrhantering, [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) för att ansluta till virtuella Linux-datorer som körs i Azure. SSH är en krypterad anslutningsprotokoll som tillåter säker inloggningar över oskyddade anslutningar. Det är standard anslutningsprotokoll för virtuella Linux-datorer i Azure. Med hjälp av SSH-nycklar för autentisering, eliminera behovet av lösenord för inloggning. SSH använder ett offentligt/privat nyckelpar (asymmetrisk kryptering) för autentisering.

## <a name="azure-vpn-encryption"></a>Azure VPN-kryptering

Du kan ansluta till Azure via ett virtuellt privat nätverk som skapar en säker tunnel för att skydda sekretessen för data som skickas över nätverket.

### <a name="azure-vpn-gateways"></a>Azure VPN gateway

Du kan använda en [Azure VPN-gatewayen](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) att skicka krypterad trafik mellan ditt virtuella nätverk och din lokala plats via en offentlig anslutning eller att skicka trafik mellan virtuella nätverk.

Plats-till-plats VPN-anslutningar används [IPsec](https://en.wikipedia.org/wiki/IPsec) för transportkryptering. Azure VPN-gatewayer använder en uppsättning standard förslag. Du kan konfigurera Azure VPN-gatewayer för att använda en anpassad IPsec/IKE-princip med specifika kryptografiska algoritmer och nyckellängder, i stället för att standardprincipen för Azure anger.

### <a name="point-to-site-vpns"></a>Punkt-till-plats-VPN

Punkt-till-plats-VPN kan enskilda klient datorer åtkomst till ett Azure-nätverk. [Den Secure Socket Tunneling Protocol (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) används för att skapa VPN-tunneln. Det kan passera brandväggar (tunneln visas som en HTTPS-anslutning). Du kan använda din egen interna offentlig nyckelinfrastruktur (PKI) rotcertifikatutfärdare (CA) för punkt-till-plats-anslutning.

Du kan konfigurera en punkt-till-plats VPN-anslutning till ett virtuellt nätverk med hjälp av Azure-portalen med certifikatautentisering eller PowerShell.

Mer information om punkt-till-plats VPN-anslutningar till virtuella Azure-nätverk finns:

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av certifieringsautentisering: Azure-portalen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med certifikatautentisering: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Plats-till-plats-VPN 

Du kan använda en plats-till-plats VPN-gatewayanslutning för att ansluta ditt lokala nätverk till ett Azure-nätverk via en IPsec/IKE (IKEv1 eller IKEv2) VPN-tunnel. Den här typen av anslutning kräver en lokal VPN-enhet som har en utåtriktade offentliga IP-adress tilldelas till den.

Du kan konfigurera en plats-till-plats VPN-anslutning till ett virtuellt nätverk med hjälp av Azure-portalen, PowerShell eller Azure CLI.

Mer information finns i:

[Skapa en plats-till-plats-anslutning i Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Skapa en plats-till-plats-anslutning i PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Skapa ett virtuellt nätverk med en plats-till-plats VPN-anslutning med hjälp av CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Kryptering i Data Lake under överföring

Data under överföring (även kallat data i rörelse) krypteras också alltid i Data Lake Store. Förutom att kryptera data före lagring på permanenta media skyddas alltid även data under överföring med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt.

Läs mer om kryptering av data under överföring i Data Lake i [kryptering av data i Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Nyckelhantering med Key Vault

Kryptering renderas oanvändbara utan rätt skydd och hantering av nycklar. Key Vault är Microsofts rekommenderade lösningen för att hantera och kontrollera åtkomst till krypteringsnycklarna som används av molntjänster. Behörighet att komma åt nycklar kan tilldelas till tjänster eller för användare via Azure Active Directory-konton.

Key Vault Frigör organisationer om behovet av att konfigurera, korrigera och underhålla maskinvarusäkerhetsmoduler (HSM) och programvara för nyckelhantering. När du använder Key Vault kan behåller du kontrollen. Microsoft ser aldrig nycklarna och program har inte direkt åtkomst till dem. Du kan också importera eller generera nycklar i HSM: er.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure-säkerhet](security-get-started-overview.md)
- [Översikt över Azure network security](security-network-overview.md)
- [Översikt över Azure database-säkerhet](azure-database-security-overview.md)
- [Säkerhetsöversikt för Azure-datorer](security-virtual-machines-overview.md)
- [Datakryptering i vila](azure-security-encryption-atrest.md)
- [Metodtips för datasäkerhet och kryptering](azure-security-data-encryption-best-practices.md)
