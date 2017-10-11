---
title: "Översikt över Azure kryptering | Microsoft Docs"
description: "Lär dig mer om olika krypteringsalternativen i Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="azure-encryption-overview"></a>Översikt över Azure kryptering

Den här artikeln innehåller en översikt över hur kryptering används i Microsoft Azure. Den omfattar huvudområden i kryptering, inklusive kryptering i vila, kryptering i svarta och hantering av nycklar med Key Vault. Varje avsnitt innehåller länkar till mer detaljerad information.

## <a name="encryption-of-data-at-rest"></a>Kryptering av vilande data

Vilande data innehåller information som finns i permanent lagringsutrymme på fysiska media digitala formatet. Detta inkluderar filer på magnetiska eller optical media arkiverade data och säkerhetskopiering av data. Microsoft Azure erbjuder en mängd olika data lagringslösningar för att uppfylla olika krav, inklusive fil-, disk-, blob- och tabellagring. Microsoft tillhandahåller också kryptering för att skydda [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [CosmosDB](../cosmos-db/introduction.md), och Azure Data Lake.

Datakryptering i viloläge är tillgänglig för tjänster över den Azure programvara som en-tjänsten (SaaS), plattform som en-tjänst (PaaS) och infrastruktur-som-en-tjänst (IaaS) molnet modeller. Det här dokumentet sammanfattar och resurser som hjälper dig att använda Azures krypteringsalternativen.

Mer detaljerad beskrivning av hur krypteras data i vila i Azure finns i dokumentet [Azure Data kryptering i vila](azure-security-encryption-atrest.md)

## <a name="azure-encryption-models"></a>Azure kryptering modeller

Azure stöder olika kryptering modeller, inklusive kryptering för serversidan via service-hanterade nycklar, med kundhanterad nycklar i Azure Key Vault, eller använder kundhanterad nycklar på kund-kontrollerade maskinvara. Kryptering på klientsidan kan du hantera och lagra nycklar på lokalt eller i en annan säker plats.

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Kryptering på klientsidan utförs utanför Azure. Kryptering på klientsidan innehåller:

- Data som krypterats av ett program som körs i kundens Datacenter eller av ett tjänstprogram
- Data som redan är krypterad när den tas emot av Azure.

Molntjänstleverantören har inte åtkomst till krypteringsnycklarna med kryptering på klientsidan och kan inte dekryptera dessa data. Du kan behålla fullständig kontroll över nycklarna.

### <a name="server-side-encryption"></a>Kryptering på serversidan

De tre kryptering för serversidan modellerna har olika nyckelhantering egenskaper som kan väljas per dina krav.

- **Service-hanterade nycklar** ge en kombination av kontrollen och bekvämlighet låg belastning

- **Kundhanterad nycklar** ger dig kontroll över nycklar, inklusive möjligheten att göra egna nycklar (BYOK) eller för att generera nya.

- **Service-hanterade nycklar i ccustomer controlledhardware** kan du hantera nycklar i egna databasen som ligger utanför Microsofts kontroll. Detta kallas värden din egen nyckel (HYOK). Dock konfiguration är komplex och mest Azure services stöder inte den här modellen.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Windows- och Linux virtuella datorer kan skyddas med [Azure Disk Encryption](azure-security-disk-encryption.md), som använder den [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) teknik- och Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) att skydda både operativsystemet diskar och datadiskar med fullständig volymkryptering.

Krypteringsnycklar och hemligheter skyddas i din [Azure Key Vault](../key-vault/key-vault-whatis.md) prenumeration. Du kan säkerhetskopiera och återställa krypterade virtuella datorer som är krypterade med KEK konfigurationen med Azure Backup-tjänsten.

### <a name="azure-storage-service-encryption"></a>Azure Storage service-kryptering

Vilande data i Azure storage (Blob och filen) vara krypterad på klientsidan och serversidan scenarier.

[Azure Storage Service-kryptering](../storage/storage-service-encryption.md) (SSE) kan automatiskt att kryptera data innan de lagras och dekrypterar dem automatiskt när du hämtar, vilket gör processen helt transparent användare. Lagringstjänstens kryptering använder 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), som är en av de starkaste blocket chiffer som är tillgängliga och hanterar kryptering, dekryptering och hantering av nycklar på ett transparent sätt.

### <a name="client-side-encryption-of-azure-blobs"></a>Kryptering på klientsidan av Azure-blobbar

Kryptering på klientsidan av Azure-blobbar kan utföras på olika sätt.

Du kan använda Azure Storage-klientbiblioteket för .NET-NuGet-paketet för att kryptera data i ditt klientprogram före överföringen till Azure Storage.

Om du vill lära dig mer om och ladda ned Azure Storage-klientbiblioteket för .NET-NuGet-paketet finns i dokumentet med titeln [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)

När du använder kryptering på klientsidan med Azure Key Vault krypteras data med en enstaka symmetriska innehåll kryptering nyckel (CEK) som genereras av Azure Storage-klient-SDK. CEK har krypterats med en nyckel kryptering nyckel (KEK) som kan vara en symmetrisk nyckel eller ett asymmetriskt nyckelpar. Du kan hantera lokalt eller lagra den i Azure Key Vault. Krypterade data överförs sedan till Azure Storage-tjänst.

Om du vill veta mer om kryptering på klientsidan med Azure Key Vault och kom igång med instruktioner finns i dokumentet med titeln [Självstudier: kryptera och dekryptera blobbar i Microsoft Azure Storage med hjälp av Azure Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md)

Slutligen kan kan du också använda Azure Storage-klientbibliotek för Java som utför kryptering på klientsidan innan du laddar upp data till Azure Storage och dekryptera data när hämtades till klienten. Det här biblioteket stöder även integrering med [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) storage-konto för hantering av nycklar.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Kryptering av data i vila med Azure SQL-databas

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) är en generell relationsdatabastjänst i Microsoft Azure som har stöd för strukturer som relationsdata, JSON, spatial, och XML. Azure SQL stöder både kryptering för serversidan via funktionen Transparent Data kryptering (TDE) och klientsidan kryptering via Always Encrypted-funktionen.

#### <a name="transparent-data-encryption"></a>Transparent datakryptering

[TDE Transparent datakryptering](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) används för att kryptera [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), och [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) datafiler i realtid med hjälp av en databaskrypteringsnyckel (DEK), som lagras i databasen startposten för tillgänglighet under återställningen.

TDE skyddar data och loggfiler med hjälp av AES och 3DES krypteringsalgoritmer. Kryptering av databasfilen utförs på sidnivå. sidorna i en krypterad databas måste krypteras innan de skrivs till disk- och dekrypteras när de har lästs in i minnet. TDE är nu aktiverad som standard för nya Azure SQL-databaser.

#### <a name="always-encrypted"></a>Alltid krypterad

Den [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funktionen i SQL Azure kan du kryptera data i klientprogram före lagring i Azure SQL Database och kan du aktivera delegering för administration av lokala databasen till tredje part och underhålla åtskillnad mellan dem som äger och visa data och de som hanterar den men inte ska ha åtkomst till den.

#### <a name="cellcolumn-level-encryption"></a>Kryptering på cellen/kolumn

Azure SQL-databas kan du använda symmetrisk kryptering till en kolumn med hjälp av Transact-SQL. Detta kallas [cell kryptering eller kryptering på kolumnen](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (r), eftersom du kan använda för att kryptera specifika kolumner eller med specifika dataceller med olika krypteringsnycklar. Det ger mer detaljerade krypteringsfunktioner än TDE som krypterar data i sidor.

Radera har inbyggda funktioner som du kan använda för att kryptera data med hjälp av antingen symmetriskt eller asymmetriskt nycklar, den offentliga nyckeln för ett certifikat eller med en lösenfras med hjälp av 3DES.

### <a name="cosmos-db-database-encryption"></a>Databaskryptering cosmos DB

[Azure Cosmos-DB](../cosmos-db/database-encryption-at-rest.md) är Microsofts globalt distribuerade och flera olika modeller databas. Data som lagras i Cosmos-DB i beständigt minne (solid-state-hårddiskar) som är krypterad som standard. Det finns inga kontroller för att aktivera eller inaktivera. Kryptering i vila implementeras med hjälp av ett antal säkerhetstekniker, inklusive system för säker lagring av nycklar, krypterade nätverk och kryptografiska API: er. Krypteringsnycklar hanteras av Microsoft och roteras per Microsofts interna riktlinjer.

### <a name="at-rest-encryption-in-azure-data-lake"></a>Kryptering i vila i Azure Data Lake

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) är en företagsomfattande lagringsplats för varje typ av data som samlas in i en enda plats innan en formell definition av krav eller schema. Har stöd för Azure Data Lake Store ”på som standard” transparent kryptering av data i vila, som ställs in under genereringen av ditt konto. Data Lake Store hanterar nycklarna du som standard, men du har möjlighet att hantera dem själv.

Tre typer av nycklar som används i kryptera och dekryptera data: kryptering huvudnyckel (MEK), Datakrypteringsnyckeln (DEK) och blockera kryptering nyckel (BEK). MEK används för att kryptera DEK som finns på beständiga medier, och BEK härleds från DEK och varje datablock. Du kan rotera MEK om du hanterar dina egna nycklar.

## <a name="encryption-of-data-in-transit"></a>Kryptering av data under överföring

Azure erbjuder många metoder för att behålla data privata som flyttas från en plats till en annan.

### <a name="tlsssl-encryption-in-azure"></a>TLS/SSL-kryptering i Azure

Microsoft använder den [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokollet för att skydda data när reser mellan molntjänster och kunder. Microsofts datacenter förhandla om en TLS-anslutning med klientdatorer som ansluter till Azure-tjänster. TLS innehåller stark autentisering, meddelandesekretess, och integritet (aktivera identifiering av meddelandet manipulation avlyssning och förfalskning), samverkan, flexibla algoritmer, enkel distribution och användning.

[Perfekt Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (Secrecy) är skyddar anslutningar mellan kunders klientsystem och Microsofts molntjänster med unika nycklar. Anslutningar använder även RSA-baserade 2 048-bitarskryptering nyckellängder. Den här kombinationen gör det svårt för att snappa upp och komma åt data som är under överföring.

### <a name="azure-storage-transactions"></a>Azure Storage-transaktioner

När du kommunicerar med Azure Storage via Azure portal för alla transaktioner äga rum via HTTPS. Du kan också använda Storage REST API för att interagera med Azure Storage via HTTPS. Du kan tillämpa HTTPS används vid anrop av REST-API för åtkomst till objekt i storage-konton genom att aktivera säker överföring krävs för lagringskontot.

Signaturer för delad åtkomst ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), som kan användas för att delegera åtkomst till Azure Storage-objekt innehåller ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst. Detta säkerställer att vem som helst skicka länkar med SAS-token använder rätt protokoll.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) används för att komma åt Azure-filresurser stöder kryptering och är tillgängliga i Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10, cross-region åtkomst och även komma åt på skrivbordet.

Klientsidans kryptering krypteras data innan den skickas till Azure Storage, så att den är krypterad när de skickas via nätverket.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-kryptering över virtuella Azure-nätverk 

[SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) i virtuella Azure-datorer som kör Windows Server 2012 och senare kan du göra data överföringar säker genom att kryptera data under överföring via Azure virtuella nätverk, som skyddar mot manipulation och avlyssning attacker. Administratörer kan aktivera SMB-kryptering för hela servern eller bara vissa resurser.

När SMB-kryptering är aktiverat för en resurs eller en server tillåts endast SMB-3-klienter åtkomst till krypterade resurser.

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Kryptering under överföring i virtuella Azure-datorer

Krypteras data under överföringen till, från och mellan virtuella Azure-datorer med Windows på många olika sätt beroende på typ av anslutningen.

### <a name="rdp-sessions"></a>RDP-sessioner

Du kan ansluta och logga in på en virtuell dator i Azure med hjälp av den [Remote Desktop Protocol](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP) från en Windows-klientdator eller från en Mac med RDP-klienten installerad. Du kan skydda data under överföringen via nätverket i RDP-sessioner av TLS.

Du kan också använda Fjärrskrivbord för att ansluta till en Linux VM i Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Säker åtkomst till Linux virtuella datorer med SSH

Du kan använda [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) för att ansluta till Linux virtuella datorer som körs i Azure för fjärrhantering. SSH är en krypterad anslutningsprotokoll som tillåter säker inloggning via oskyddat anslutningar. Det är standardprotokollet för anslutning för virtuella Linux-datorer finns i Azure. Med hjälp av SSH-nycklar för autentisering, eliminerar behovet för lösenord att logga in. SSH använder en offentlig/privat nyckelpar (asymmetrisk kryptering) för autentisering.

## <a name="azure-vpn-encryption"></a>Azure VPN-kryptering

Du kan ansluta till Azure via ett virtuellt privat nätverk som skapar en säker tunnel för att skydda sekretessen för data som skickas över nätverket.

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

[Azure VPN-gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) kan användas för att skicka krypterad trafik mellan det virtuella nätverket och den lokala platsen i en offentlig anslutning, eller att skicka trafik mellan virtuella nätverk.

Plats-till-plats VPN använder [IPsec](https://en.wikipedia.org/wiki/IPsec) för transport-kryptering. Azure VPN-gatewayer Använd standard förslag. Du kan konfigurera Azure VPN-gatewayer för att använda en anpassad IPsec/IKE-princip med särskilda kryptografiska algoritmer och viktiga styrkor i stället anger standardprincipen för Azure.

### <a name="point-to-site-vpn"></a>Punkt-till-plats VPN

Punkt-till-plats-VPN kan enskilda klienten datorer åtkomst till ett Azure Virtual Network. [Secure Socket Tunneling Protocol](https://technet.microsoft.com/library/2007.06.cableguy.aspx) (SSTP) används för att skapa VPN-tunnel och kan bläddra brandväggar (tunneln visas som en HTTPS-anslutning). Du kan använda din egen interna PKI rotcertifikatutfärdaren för punkt-till-plats-anslutning.

Du kan konfigurera en punkt-till-plats VPN-anslutning till ett virtuellt nätverk med Azure-portalen med certifikatautentisering eller PowerShell.

Mer information om punkt-till-plats VPN-anslutningar till virtuella Azure-nätverk finns: [konfigurerar en punkt-till-plats-anslutning till ett VNet med certifieringsautentisering: Azure-portalen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) och

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk som använder certifikatautentisering: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>Plats-till-plats VPN 

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress.

Du kan konfigurera en plats-till-plats VPN-anslutning till ett virtuellt nätverk med Azure-portalen, PowerShell eller Azure-kommandoradsgränssnittet (CLI).

Läs igenom det här för mer information:

[Skapa en plats-till-plats-anslutning i Azure-portalen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Skapa en plats-till-plats-anslutning](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Skapa ett virtuellt nätverk med en plats-till-plats VPN-anslutning med hjälp av CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Kryptering under överföring i Azure Data Lake

Data under överföring (även kallat data i rörelse) krypteras också alltid i Data Lake Store. Förutom att kryptera data före lagring till permanenta media skyddas alltid även data under överföring eller i rörelse med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt.

Mer information om kryptering av data under överföring i Azure Data Lake finns i dokumentet [kryptering av data i Azure Data Lake Store.](../data-lake-store/data-lake-store-encryption.md)

## <a name="key-management-with-azure-key-vault"></a>Hantering av nycklar med Azure Key Vault

Utan tillräckligt skydd och hantering av nycklar återges kryptering oanvändbar. Azure Key Vault är Microsofts rekommenderade lösningen för att hantera och kontrollera åtkomst till krypteringsnycklarna som används av molntjänster. Behörighet att komma åt nycklar kan tilldelas till tjänster eller användare via Azure Active Directory-konton.

Azure Key Vault besparar organisationer behovet av att konfigurera, korrigera och underhålla Maskinvarusäkerhetsmoduler (HSM) och programvara för hantering av nycklar. Med Azure Key Vault Microsoft ser aldrig dina nycklar och program har inte direkt åtkomst till dem. du behåller kontrollen. Du kan också importera eller generera nycklar i HSM.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure-säkerhet](security-get-started-overview.md)
- [Översikt över säkerheten i Azure-nätverk](security-network-overview.md)
- [Översikt över säkerheten i Azure-databas](azure-database-security-overview.md)
- [Översikt över säkerheten i virtuella Azure-datorer](security-virtual-machines-overview.md)
- [Datakryptering i vila](azure-security-encryption-atrest.md)
- [Metodtips för datasäkerhet och kryptering](azure-security-data-encryption-best-practices.md)
