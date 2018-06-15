---
title: Översikt över Azure kryptering | Microsoft Docs
description: Lär dig mer om olika krypteringsalternativen i Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 00c8b30b5351b7a6e4388b186fab70e3a3357ef4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366315"
---
# <a name="azure-encryption-overview"></a>Översikt över Azure kryptering

Den här artikeln innehåller en översikt över hur kryptering används i Microsoft Azure. Den omfattar huvudområden i kryptering, inklusive kryptering i vila, kryptering i svarta och hantering av nycklar med Azure Key Vault. Varje avsnitt innehåller länkar till mer detaljerad information.

## <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data

Vilande data innehåller information som finns i permanent lagringsutrymme på fysiska media digitala formatet. Mediet kan innehålla filer på magnetiska eller optical media, arkiverade data och säkerhetskopiering av data. Microsoft Azure erbjuder en mängd olika data lagringslösningar för att uppfylla olika krav, inklusive fil-, disk-, blob- och tabellagring. Microsoft tillhandahåller också kryptering för att skydda [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [Azure Cosmos DB](../cosmos-db/introduction.md), och Azure Data Lake.

Datakryptering i viloläge är tillgänglig för tjänster över programvara som en tjänst (SaaS), plattform som en tjänst (PaaS) och en infrastruktur som en tjänst (IaaS) molnet modeller. Den här artikeln sammanfattar och resurser som hjälper dig att använda Azure krypteringsalternativen.

En mer detaljerad beskrivning av hur krypteras data i vila i Azure finns [Azure Data kryptering i vila](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Azure kryptering modeller

Azure stöder olika kryptering modeller, inklusive kryptering på serversidan som använder service-hanterade nycklar, kundhanterad nycklar i Nyckelvalvet eller kundhanterad nycklar på kund-kontrollerade maskinvara. Med kryptering på klientsidan, kan du hantera och lagra nycklar på lokalt eller i en annan säker plats.

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Kryptering på klientsidan utförs utanför Azure. Det innehåller:

- Data som krypterats av ett program som körs i kundens datacenter eller av ett tjänstprogram.
- Data som redan är krypterad när den tas emot av Azure.

Med kryptering på klientsidan, leverantörer av molntjänster har inte åtkomst till krypteringsnycklarna och det går inte att dekryptera dessa data. Du kan behålla fullständig kontroll över nycklarna.

### <a name="server-side-encryption"></a>Kryptering på serversidan

De tre kryptering på serversidan modellerna har olika nyckelhantering egenskaper som du kan välja enligt dina behov:

- **Service-hanterade nycklar**: innehåller en kombination av kontrollen och bekvämlighet med låg belastning.

- **Kundhanterad nycklar**: ger du kontroll över nycklar, inklusive stöd för ta med din egen nycklar (BYOK), eller kan du generera nya.

- **Service-hanterade nycklar i kund-kontrollerade maskinvara**: gör att du kan hantera nycklar i egna databasen, utanför Microsoft-kontrollen. Denna variabel kallas värden din egen nyckel (HYOK). Dock konfiguration är komplex och mest Azure services stöder inte den här modellen.

### <a name="azure-disk-encryption"></a>Azure disk encryption

Du kan skydda Windows- och Linux virtuella datorer med hjälp av [Azure disk encryption](azure-security-disk-encryption.md), som använder [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) teknik- och Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) att skydda både operativsystemet diskar och datadiskar med fullständig volymkryptering.

Krypteringsnycklar och hemligheter skyddas i din [Azure Key Vault prenumeration](../key-vault/key-vault-whatis.md). Du kan säkerhetskopiera och återställa krypterade virtuella datorer (VM som använder nyckeln kryptering nyckel (KEK) konfiguration) med hjälp av Azure Backup-tjänsten.

### <a name="azure-storage-service-encryption"></a>Kryptering av Azure-lagringstjänst

Vilande data i Azure Blob storage och Azure-filresurser kan vara krypterad på klientsidan och serversidan scenarier.

[Azure Storage Service kryptering (SSE)](../storage/common/storage-service-encryption.md) automatiskt kryptera data innan den lagras och det automatiskt dekrypterar data när du hämtar den. Processen är helt transparent för användarna. Lagringstjänstens kryptering använder 256-bitars [Advanced Encryption Standard (AES) kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), som är en av de starkaste block chiffer som är tillgängliga. AES hanterar transparent kryptering, dekryptering och hantering av nycklar.

### <a name="client-side-encryption-of-azure-blobs"></a>Kryptering på klientsidan av Azure-blobbar

Du kan utföra på klientsidan kryptering av Azure BLOB-objekt på olika sätt.

Du kan använda Azure Storage-klientbiblioteket för .NET-NuGet-paketet för att kryptera data i ditt klientprogram före överföringen till ditt Azure storage.

Om du vill lära dig mer om och ladda ned Azure Storage-klientbiblioteket för .NET-NuGet-paketet finns [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

När du använder kryptering på klientsidan med Key Vault krypteras data med en enstaka symmetriska innehåll kryptering nyckel (CEK) som genereras av Azure Storage-klient-SDK. CEK har krypterats med en nyckel kryptering nyckel (KEK) som kan vara en symmetrisk nyckel eller ett asymmetriskt nyckelpar. Du kan hantera lokalt eller lagra den i Nyckelvalvet. Krypterade data överförs sedan till Azure Storage.

Om du vill veta mer om kryptering på klientsidan med Key Vault och kom igång med instruktioner, se [Självstudier: kryptera och dekryptera blobbar i Azure Storage med hjälp av Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Slutligen kan kan du också använda Azure Storage-klientbibliotek för Java som utför kryptering på klientsidan innan du laddar upp data till Azure Storage och dekryptera data när du laddar ned till klienten. Det här biblioteket stöder även integrering med [Key Vault](https://azure.microsoft.com/services/key-vault/) storage-konto för hantering av nycklar.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Kryptering av data i vila med Azure SQL Database

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) är en generell relationsdatabastjänst i Azure som har stöd för strukturer som relationsdata, JSON, spatial, och XML. SQL-databasen stöder både kryptering för serversidan via funktionen Transparent Data kryptering (TDE) och klientsidan kryptering via Always Encrypted-funktionen.

#### <a name="transparent-data-encryption"></a>Transparent datakryptering

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) används för att kryptera [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), och [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) datafiler i realtid, med hjälp av en databas Datakrypteringsnyckeln (DEK) , som lagras i databasen startposten för tillgänglighet under återställningen.

TDE skyddar data och loggfiler med hjälp av AES och trippel datakrypteringsstandard (3DES) krypteringsalgoritmer. Kryptering av databasfilen utförs på sidnivån. Sidorna i en krypterad databas måste krypteras innan de skrivs till disk- och dekrypteras när de har lästs in i minnet. TDE är nu aktiverad som standard för nya Azure SQL-databaser.

#### <a name="always-encrypted-feature"></a>Always Encrypted-funktionen

Med den [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funktionen i SQL Azure kan du kryptera data i klientprogram före lagras i Azure SQL Database. Du kan också aktivera delegering för administration av lokala databasen till tredje part och underhålla åtskillnad mellan dem som äger och visa data och de som hanterar den men inte ska ha åtkomst till den.

#### <a name="cell-level-or-column-level-encryption"></a>Cell- eller kolumn nivå kryptering

Med Azure SQL Database, kan du använda symmetrisk kryptering till en kolumn med data med hjälp av Transact-SQL. Den här metoden anropas [cellnivå kryptering eller kolumnnivå kryptering (r)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), eftersom du kan använda för att kryptera specifika kolumner eller med specifika dataceller med olika krypteringsnycklar. Då får du mer detaljerade krypteringsfunktioner än TDE som krypterar data i sidor.

Radera har inbyggda funktioner som du kan använda för att kryptera data med hjälp av antingen symmetriskt eller asymmetriskt nycklar, den offentliga nyckeln för ett certifikat eller en lösenfras med hjälp av 3DES.

### <a name="cosmos-db-database-encryption"></a>Databaskryptering cosmos DB

[Azure Cosmos-DB](../cosmos-db/database-encryption-at-rest.md) är Microsofts globalt distribuerade och flera olika modeller databas. Informationen som lagras i Cosmos-DB i beständigt minne (solid-state-hårddiskar) är krypterad som standard. Det finns inga kontroller för att aktivera eller inaktivera. Kryptering i vila implementeras med hjälp av ett antal säkerhetstekniker, inklusive system för säker lagring av nycklar, krypterade nätverk och kryptografiska API: er. Krypteringsnycklar hanteras av Microsoft och roteras per Microsoft interna riktlinjer.

### <a name="at-rest-encryption-in-data-lake"></a>Kryptering i vila i Data Lake

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) är en företagsomfattande lagringsplats för varje typ av data som samlas in i en enda plats innan en formell definition av krav eller schema. Data Lake Store stöder ”på som standard” transparent kryptering av data i vila, som ställs in under genereringen av ditt konto. Azure Data Lake Store hanterar nycklarna du som standard, men du har möjlighet att hantera dem själv.

Tre typer av nycklar som används i kryptera och dekryptera data: kryptering huvudnyckel (MEK), Datakrypteringsnyckeln (DEK) och blockera kryptering nyckel (BEK). MEK används för att kryptera DEK som finns på beständiga medier, och BEK härleds från DEK och varje datablock. Du kan rotera MEK om du hanterar dina egna nycklar.

## <a name="encryption-of-data-in-transit"></a>Kryptering av data under överföring

Azure erbjuder många metoder för att behålla data privata som flyttas från en plats till en annan.

### <a name="tlsssl-encryption-in-azure"></a>TLS/SSL-kryptering i Azure

Microsoft använder den [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokollet för att skydda data när reser mellan molntjänster och kunder. Microsoft-datacenter förhandla om en TLS-anslutning med klientdatorer som ansluter till Azure-tjänster. TLS innehåller stark autentisering, meddelandesekretess, och integritet (aktivera identifiering av meddelandet manipulation avlyssning och förfalskning), samverkan, flexibla algoritmer och enkel distribution och användning.

[Perfekt Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (Secrecy) skyddar anslutningar mellan kunders klientsystem och Microsoft-molntjänster med unika nycklar. Anslutningar använder även RSA-baserade 2 048-bitarskryptering nyckellängder. Den här kombinationen gör det svårt för att snappa upp och komma åt data som överförs.

### <a name="azure-storage-transactions"></a>Azure Storage-transaktioner

När du kommunicerar med Azure Storage via Azure portal för alla transaktioner äga rum via HTTPS. Du kan också använda Storage REST API för att interagera med Azure Storage via HTTPS. Du kan framtvinga användning av HTTPS när du anropa REST-API: er för åtkomst till objekt i storage-konton genom att aktivera säker överföring som krävs för lagringskontot.

Signaturer för delad åtkomst ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), som kan användas för att delegera åtkomst till Azure Storage-objekt innehåller ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst. Den här metoden gör att vem som helst som skickar länkar med SAS-token använder rätt protokoll.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), som används för att få åtkomst till Azure Files resurser, har stöd för kryptering, och den är tillgänglig i Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10. Det ger mellan region åtkomst och även komma åt på skrivbordet.

Klientsidans kryptering krypteras data innan den skickas till din Azure Storage-instans, så att den är krypterad när de skickas via nätverket.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-kryptering över virtuella Azure-nätverk 

Med hjälp av [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) i virtuella datorer som kör Windows Server 2012 eller senare, kan du göra data överföringar säker genom att kryptera data under överföring via Azure-nätverk. Genom att kryptera data skyddar du mot manipulering och avlyssning attacker. Administratörer kan aktivera SMB-kryptering för hela servern eller bara vissa resurser.

När SMB-kryptering är aktiverat för en resurs eller en server tillåts endast SMB 3.0-klienter åtkomst till krypterade resurser.

## <a name="in-transit-encryption-in-vms"></a>Kryptering för virtuella datorer under överföring

Krypteras data under överföringen till, från och mellan virtuella datorer som kör Windows på många olika sätt beroende på typ av anslutningen.

### <a name="rdp-sessions"></a>RDP-sessioner

Du kan ansluta och logga in på en virtuell dator med hjälp av den [Remote Desktop Protocol (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) från en Windows-klientdator eller från en Mac med RDP-klienten installerad. Du kan skydda data under överföringen via nätverket i RDP-sessioner av TLS.

Du kan också använda Fjärrskrivbord för att ansluta till en Linux VM i Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Säker åtkomst till Linux virtuella datorer med SSH

Du kan använda för fjärrhantering, [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) för att ansluta till Linux virtuella datorer som körs i Azure. SSH är en krypterad anslutningsprotokoll som tillåter säker inloggningar över oskyddade anslutningar. Det är standardprotokollet för anslutning för virtuella Linux-datorer finns i Azure. Med hjälp av SSH-nycklar för autentisering, eliminerar behovet för lösenord för inloggning. SSH använder en offentlig/privat nyckelpar (asymmetrisk kryptering) för autentisering.

## <a name="azure-vpn-encryption"></a>Azure VPN-kryptering

Du kan ansluta till Azure via ett virtuellt privat nätverk som skapar en säker tunnel för att skydda sekretessen för data som skickas över nätverket.

### <a name="azure-vpn-gateways"></a>Azure VPN-gatewayer

Du kan använda en [Azure VPN-gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) att skicka krypterad trafik mellan det virtuella nätverket och den lokala platsen i en offentlig anslutning, eller att skicka trafik mellan virtuella nätverk.

Plats-till-plats VPN-anslutningar används [IPsec](https://en.wikipedia.org/wiki/IPsec) för transport-kryptering. Azure VPN-gatewayer Använd standard förslag. Du kan konfigurera Azure VPN-gatewayer för att använda en anpassad IPsec/IKE-princip med särskilda kryptografiska algoritmer och viktiga styrkor i stället anger standardprincipen för Azure.

### <a name="point-to-site-vpns"></a>Punkt-till-plats VPN-anslutningar

Punkt-till-plats-VPN kan enskilda klienten datorer åtkomst till Azure-nätverk. [Den Secure Socket Tunneling Protocol (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) används för att skapa VPN-tunnel. Det kan gå igenom brandväggar (tunneln visas som en HTTPS-anslutning). Du kan använda din egen interna infrastruktur för offentliga nycklar (PKI) rotcertifikatutfärdare (CA) för punkt-till-plats-anslutning.

Du kan konfigurera en punkt-till-plats VPN-anslutning till ett virtuellt nätverk med hjälp av Azure-portalen med certifikatautentisering eller PowerShell.

Mer information om punkt-till-plats VPN-anslutningar till virtuella Azure-nätverk finns:

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av certifikatutfärdare autentisering: Azure-portalen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av autentisering med datorcertifikat: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Plats-till-plats VPN-anslutningar 

Du kan använda en plats-till-plats VPN-anslutning för gateway för att ansluta din lokala nätverk till Azure-nätverk via en IPsec/IKE (IKEv1 eller IKEv2) VPN-tunnel. Den här typen av anslutning kräver en lokal VPN-enhet som har en externa offentliga IP-adressen till den.

Du kan konfigurera en plats-till-plats VPN-anslutning till ett virtuellt nätverk med hjälp av Azure-portalen, PowerShell eller Azure CLI.

Mer information finns i:

[Skapa en plats-till-plats-anslutning i Azure-portalen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Skapa en plats-till-plats-anslutning i PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Skapa ett virtuellt nätverk med en plats-till-plats VPN-anslutning med hjälp av CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Kryptering för Data Lake under överföring

Data under överföring (även kallat data i rörelse) krypteras också alltid i Data Lake Store. Förutom att kryptera data innan lagras i beständiga media skyddas alltid data under överföringen med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt.

Mer information om kryptering av data under överföring i Data Lake finns [kryptering av data i Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Hantering av nycklar med Key Vault

Utan tillräckligt skydd och hantering av nycklar återges kryptering oanvändbar. Key Vault är Microsofts rekommenderade lösningen för att hantera och kontrollera åtkomst till krypteringsnycklarna som används av molntjänster. Behörighet att komma åt nycklar kan tilldelas till tjänster eller användare via Azure Active Directory-konton.

Key Vault besparar organisationer behovet av att konfigurera, korrigera och underhålla maskinvarusäkerhetsmoduler (HSM) och programvara för hantering av nycklar. När du använder Key Vault kan behålla kontrollen. Microsoft ser aldrig dina nycklar och program har inte direkt åtkomst till dem. Du kan också importera eller generera nycklar i HSM.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure-säkerhet](security-get-started-overview.md)
- [Översikt över säkerheten i Azure-nätverk](security-network-overview.md)
- [Översikt över säkerheten i Azure-databas](azure-database-security-overview.md)
- [Översikt över säkerheten i virtuella Azure-datorer](security-virtual-machines-overview.md)
- [Datakryptering i vila](azure-security-encryption-atrest.md)
- [Metodtips för datasäkerhet och kryptering](azure-security-data-encryption-best-practices.md)
