---
title: Översikt över Azure-kryptering | Microsoft-dokument
description: Lär dig mer om olika krypteringsalternativ i Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 5d8fd578c5539c83e37a232d8425ad8bdf22129b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125055"
---
# <a name="azure-encryption-overview"></a>Översikt över Azure-kryptering

Den här artikeln innehåller en översikt över hur kryptering används i Microsoft Azure. Den täcker de viktigaste krypteringsområdena, inklusive kryptering i vila, kryptering under flygning och nyckelhantering med Azure Key Vault. Varje avsnitt innehåller länkar till mer detaljerad information.

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila

Data i vila innehåller information som finns i beständig lagring på fysiska medier, i alla digitala format. Mediet kan innehålla filer på magnetiska eller optiska medier, arkiverade data och säkerhetskopiering av data. Microsoft Azure erbjuder en mängd olika datalagringslösningar för att uppfylla olika behov, inklusive fil-, disk-, blob- och tabelllagring. Microsoft tillhandahåller också kryptering för att skydda [Azure SQL Database,](../../sql-database/sql-database-technical-overview.md) [Azure Cosmos DB](../../data-factory/introduction.md)och Azure Data Lake.

Datakryptering i vila är tillgänglig för tjänster i programvaran som en tjänst (SaaS), plattform som en tjänst (PaaS) och infrastruktur som en tjänst (IaaS) molnmodeller. Den här artikeln sammanfattar och tillhandahåller resurser som hjälper dig att använda Azure-krypteringsalternativen.

En mer detaljerad diskussion om hur data i vila krypteras i Azure finns i [Azure Data Encryption-at-Rest](encryption-atrest.md).

## <a name="azure-encryption-models"></a>Azure-krypteringsmodeller

Azure stöder olika krypteringsmodeller, inklusive kryptering på serversidan som använder tjänsthanterade nycklar, kundhanterade nycklar i Key Vault eller kundhanterade nycklar på kundstyrd maskinvara. Med kryptering på klientsidan kan du hantera och lagra nycklar lokalt eller på en annan säker plats.

### <a name="client-side-encryption"></a>Kryptering av klientsidan

Kryptering på klientsidan utförs utanför Azure. Den innehåller:

- Data som krypteras av ett program som körs i kundens datacenter eller av ett tjänstprogram.
- Data som redan är krypterade när de tas emot av Azure.

Med kryptering på klientsidan har molntjänstleverantörer inte åtkomst till krypteringsnycklarna och kan inte dekryptera dessa data. Du behåller fullständig kontroll över nycklarna.

### <a name="server-side-encryption"></a>Kryptering på serversidan

De tre krypteringsmodellerna på serversidan erbjuder olika viktiga hanteringsegenskaper, som du kan välja enligt dina krav:

- **Tjänsthanterade nycklar:** Ger en kombination av kontroll och bekvämlighet med låga omkostnader.

- **Kundhanterade nycklar:** Ger dig kontroll över nycklarna, inklusive Ta med egna nycklar (BYOK) stöd, eller låter dig generera nya.

- **Tjänsthanterade nycklar i kundstyrd maskinvara**: Gör att du kan hantera nycklar i din egen databas, utanför Microsofts kontroll. Denna egenskap kallas Host Your Own Key (HYOK). Konfigurationen är dock komplex och de flesta Azure-tjänster stöder inte den här modellen.

### <a name="azure-disk-encryption"></a>Kryptering av Azure-disk

Du kan skydda virtuella Windows- och Linux-datorer med hjälp av [Azure-diskkryptering](/azure/security/fundamentals/azure-disk-encryption-vms-vmss), som använder [Windows BitLocker-teknik](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) och Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) för att skydda både operativsystemdiskar och datadiskar med fullständig volymkryptering.

Krypteringsnycklar och hemligheter skyddas i din [Azure Key Vault-prenumeration](../../key-vault/key-vault-overview.md). Med hjälp av Azure Backup-tjänsten kan du säkerhetskopiera och återställa krypterade virtuella datorer (VMs) som använder KEK-konfiguration (Key Encryption Key Key).

### <a name="azure-storage-service-encryption"></a>Kryptering av Azure Storage-tjänsten

Data i vila i Azure Blob storage och Azure-filresurser kan krypteras i scenarier på både server- och klientsidan.

[Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) kan automatiskt kryptera data innan den lagras och dekrypterar automatiskt data när du hämtar dem. Processen är helt transparent för användarna. Storage Service Encryption använder 256-bitars [AES-kryptering (Advanced Encryption Standard),](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)som är ett av de starkaste blockchiffer som finns tillgängliga. AES hanterar kryptering, dekryptering och nyckelhantering transparent.

### <a name="client-side-encryption-of-azure-blobs"></a>Kryptering på klientsidan av Azure-blobbar

Du kan utföra kryptering på klientsidan av Azure-blobbar på olika sätt.

Du kan använda Azure Storage Client Library for .NET NuGet-paketet för att kryptera data i klientprogrammen innan du överför det till din Azure-lagring.

Mer information om och hämta Azure Storage Client Library för .NET NuGet-paketet finns i [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

När du använder kryptering på klientsidan med Key Vault krypteras dina data med en engångssymmetrisk content encryption key (CEK) som genereras av Azure Storage-klienten SDK. CEK krypteras med hjälp av en nyckelkrypteringsnyckel (KEK), som kan vara antingen en symmetrisk nyckel eller ett asymmetriskt nyckelpar. Du kan hantera den lokalt eller lagra den i Key Vault. De krypterade data överförs sedan till Azure Storage.

Mer information om kryptering på klientsidan med Key Vault och komma igång med instruktioner finns [i Självstudiekurs: Kryptera och dekryptera blobbar i Azure Storage med hjälp av Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Slutligen kan du också använda Azure Storage Client Library for Java för att utföra kryptering på klientsidan innan du överför data till Azure Storage och för att dekryptera data när du hämtar dem till klienten. Det här biblioteket stöder också integrering med [Key Vault](https://azure.microsoft.com/services/key-vault/) för hantering av lagringskontonyckel.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Kryptering av data i vila med Azure SQL Database

[Azure SQL Database](../../sql-database/sql-database-technical-overview.md) är en relationell databastjänst för allmänt syfte i Azure som stöder strukturer som relationsdata, JSON, spatial och XML. SQL Database stöder kryptering på båda serversidan via TDE-funktionen (Transparent Data Encryption) och kryptering på klientsidan via funktionen Alltid krypterad.

#### <a name="transparent-data-encryption"></a>Transparent datakryptering

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) används för att kryptera [SQL Server,](https://www.microsoft.com/sql-server/sql-server-2016) [Azure SQL Database](../../sql-database/sql-database-technical-overview.md)och Azure SQL Data [Warehouse-datafiler](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) i realtid med hjälp av en databaskrypteringsnyckel (DEK), som lagras i databasstartposten för tillgänglighet under återställningen.

TDE skyddar data- och loggfiler med hjälp av krypteringsalgoritmer för AES och Triple Data Encryption Standard (3DES). Kryptering av databasfilen utförs på sidnivå. Sidorna i en krypterad databas krypteras innan de skrivs till disk och dekrypteras när de läss in i minnet. TDE är nu aktiverat som standard på nyligen skapade Azure SQL-databaser.

#### <a name="always-encrypted-feature"></a>Alltid krypterad funktion

Med funktionen [Alltid krypterad](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) i Azure SQL kan du kryptera data i klientprogram innan du lagrar dem i Azure SQL Database. Du kan också aktivera delegering av lokal databasadministration till tredje part och upprätthålla separation mellan dem som äger och kan visa data och de som hanterar dem men bör inte ha tillgång till dem.

#### <a name="cell-level-or-column-level-encryption"></a>Kryptering på cellnivå eller kolumnnivå

Med Azure SQL Database kan du använda symmetrisk kryptering på en kolumn med data med hjälp av Transact-SQL. Den här metoden kallas [kryptering på cellnivå eller kryptering på kolumnnivå (CLE),](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)eftersom du kan använda den för att kryptera specifika kolumner eller till och med specifika dataceller med olika krypteringsnycklar. Om du gör det kan du ha mer detaljerad krypteringskapacitet än TDE, som krypterar data på sidor.

CLE har inbyggda funktioner som du kan använda för att kryptera data med hjälp av symmetriska eller asymmetriska nycklar, den offentliga nyckeln till ett certifikat eller en lösenfras med 3DES.

### <a name="cosmos-db-database-encryption"></a>Kryptering av Cosmos DB-databas

[Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) är Microsofts globalt distribuerade databas med flera modeller. Användardata som lagras i Cosmos DB i icke-flyktig lagring (solid state-enheter) krypteras som standard. Det finns inga kontroller för att aktivera eller inaktivera den. Kryptering i vila implementeras med hjälp av ett antal säkerhetstekniker, inklusive säkra nyckellagringssystem, krypterade nätverk och kryptografiska API:er. Krypteringsnycklar hanteras av Microsoft och roteras enligt Microsofts interna riktlinjer.

### <a name="at-rest-encryption-in-data-lake"></a>I vilande kryptering i Data Lake

[Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) är en företagsomfattande databas över alla typer av data som samlas in på en enda plats före någon formell definition av krav eller schema. DataSjöarkivet stöder "på som standard", transparent kryptering av data i vila, som ställs in när ditt konto skapas. Som standard hanterar Azure Data Lake Store nycklarna åt dig, men du har möjlighet att hantera dem själv.

Tre typer av nycklar används för att kryptera och dekryptera data: Master Encryption Key (MEK), Data Encryption Key (DEK) och Blockera krypteringsnyckel (BEK). MEK används för att kryptera DEK, som lagras på beständiga medier, och BEK härleds från DEK och datablocket. Om du hanterar dina egna nycklar kan du rotera MEK.

## <a name="encryption-of-data-in-transit"></a>Kryptering av data under överföring

Azure erbjuder många mekanismer för att hålla data privata när de flyttas från en plats till en annan.

### <a name="tlsssl-encryption-in-azure"></a>TLS/SSL-kryptering i Azure

Microsoft använder [TLS-protokollet (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security) för att skydda data när det färdas mellan molntjänsterna och kunderna. Microsoft-datacenter förhandlar om en TLS-anslutning med klientsystem som ansluter till Azure-tjänster. TLS ger stark autentisering, meddelandesekretess och integritet (möjliggör identifiering av meddelandemanipulering, avlyssning och förfalskning), interoperabilitet, algoritmflexibilitet och enkel distribution och användning.

[Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) skyddar anslutningar mellan kundernas klientsystem och Microsofts molntjänster med unika nycklar. Anslutningar använder också RSA-baserade 2 048-bitars krypteringsnyckellängder. Den här kombinationen gör det svårt för någon att fånga upp och komma åt data som är under överföring.

### <a name="azure-storage-transactions"></a>Azure Storage-transaktioner

När du interagerar med Azure Storage via Azure-portalen sker alla transaktioner via HTTPS. Du kan också använda REST-API:et för lagring via HTTPS för att interagera med Azure Storage. Du kan tillämpa användningen av HTTPS när du anropar REST-API:erna för att komma åt objekt i lagringskonton genom att aktivera den säkra överföring som krävs för lagringskontot.

Signaturer för delad åtkomst ([SAS](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)), som kan användas för att delegera åtkomst till Azure Storage-objekt, innehåller ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder Signaturer för delad åtkomst. Den här metoden säkerställer att alla som skickar länkar till SAS-token använder rätt protokoll.

[SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), som används för att komma åt Azure Files-resurser, stöder kryptering och är tillgängligt i Windows Server 2012 R2, Windows 8, Windows 8.1 och Windows 10. Det ger åtkomst mellan regioner och även åtkomst på skrivbordet.

Kryptering på klientsidan krypterar data innan de skickas till din Azure Storage-instans, så att de krypteras när de färdas över nätverket.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-kryptering över virtuella Azure-nätverk 

Genom att använda [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) i virtuella datorer som kör Windows Server 2012 eller senare kan du göra dataöverföringar säkra genom att kryptera data under överföring via Virtuella Azure-nätverk. Genom att kryptera data hjälper du till att skydda mot manipulering och avlyssning av attacker. Administratörer kan aktivera SMB-kryptering för hela servern, eller bara specifika resurser.

Som standard, efter SMB-kryptering är aktiverat för en resurs eller server, endast SMB 3.0-klienter får komma åt krypterade resurser.

## <a name="in-transit-encryption-in-vms"></a>Kryptering under överföring i virtuella datorer

Data som överförs till, från och mellan virtuella datorer som kör Windows krypteras på flera olika sätt, beroende på anslutningens art.

### <a name="rdp-sessions"></a>RDP-sessioner

Du kan ansluta och logga in på en virtuell dator med hjälp av [RDP (Remote Desktop Protocol)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) från en Windows-klientdator eller från en Mac med en RDP-klient installerad. Data som överförs via nätverket i RDP-sessioner kan skyddas av TLS.

Du kan också använda Fjärrskrivbord för att ansluta till en Virtuell Linux-dator i Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Säker åtkomst till virtuella Linux-datorer med SSH

För fjärrhantering kan du använda [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) för att ansluta till virtuella Linux-datorer som körs i Azure. SSH är ett krypterat anslutningsprotokoll som möjliggör säkra inloggningar via osäkra anslutningar. Det är standardanslutningsprotokollet för virtuella Linux-datorer som finns i Azure. Genom att använda SSH-nycklar för autentisering eliminerar du behovet av lösenord för att logga in. SSH använder ett offentligt/privat nyckelpar (asymmetrisk kryptering) för autentisering.

## <a name="azure-vpn-encryption"></a>Azure VPN-kryptering

Du kan ansluta till Azure via ett virtuellt privat nätverk som skapar en säker tunnel för att skydda sekretessen för de data som skickas över nätverket.

### <a name="azure-vpn-gateways"></a>Azure VPN Gateway

Du kan använda en [Azure VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) för att skicka krypterad trafik mellan ditt virtuella nätverk och din lokala plats över en offentlig anslutning, eller för att skicka trafik mellan virtuella nätverk.

Virtuella VPN-nätverk på plats använder [IPsec](https://en.wikipedia.org/wiki/IPsec) för transportkryptering. Azure VPN-gateways använder en uppsättning standardförslag. Du kan konfigurera Azure VPN-gateways för att använda en anpassad IPsec/IKE-princip med specifika kryptografiska algoritmer och nyckelstyrkor, i stället för standardprincipuppsättningarna för Azure.

### <a name="point-to-site-vpns"></a>Punkt-till-plats-VPN

Virtuella VPN-nätverk på plats tillåter enskilda klientdatorer åtkomst till ett virtuellt Azure-nätverk. [SSTP (Secure Socket Tunneling Protocol)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) används för att skapa VPN-tunneln. Det kan passera brandväggar (tunneln visas som en HTTPS-anslutning). Du kan använda din egen interna infrastruktur för offentliga nycklar (PKI) rotcertifikatutfärdaren (CA) för anslutning mellan punkt och plats.

Du kan konfigurera en punkt-till-plats-VPN-anslutning till ett virtuellt nätverk med hjälp av Azure-portalen med certifikatautentisering eller PowerShell.

Mer information om VPN-anslutningar från punkt till plats till virtuella Azure-nätverk finns i:

[Konfigurera en point-to-site-anslutning till ett virtuellt nätverk med hjälp av certifieringsautentisering: Azure-portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av certifikatautentisering: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Virtuella vpn på plats till plats 

Du kan använda en VPN-gateway-anslutning från plats till plats för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en VPN-tunnel i IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som har en extern offentlig IP-adress tilldelad.

Du kan konfigurera en VPN-anslutning från plats till plats till ett virtuellt nätverk med hjälp av Azure-portalen, PowerShell eller Azure CLI.

Mer information finns i:

[Skapa en plats-till-plats-anslutning i Azure-portalen](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Skapa en webbplats-till-plats-anslutning i PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Transitkryptering i Datasjö

Data under överföring (även kallat data i rörelse) krypteras också alltid i Data Lake Store. Förutom att kryptera data innan du lagrar dem i beständiga media, är data också alltid säkras under överföring med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt.

Mer information om kryptering av data under överföring i Datasjö finns i [Kryptering av data i DataSjö store](../../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Nyckelhantering med Key Vault

Utan korrekt skydd och hantering av nycklarna, är kryptering göras värdelös. Key Vault är den Microsoft-rekommenderade lösningen för att hantera och kontrollera åtkomst till krypteringsnycklar som används av molntjänster. Behörigheter för att komma åt nycklar kan tilldelas tjänster eller användare via Azure Active Directory-konton.

Key Vault avlastar organisationer med behovet av att konfigurera, korrigera och underhålla HSM -moduler (Hardware Security Modules) och nyckelhanteringsprogram. När du använder Key Vault behåller du kontrollen. Microsoft ser aldrig dina nycklar och program har inte direkt åtkomst till dem. Du kan också importera eller generera nycklar i HSM-moduler.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure-säkerhet](get-started-overview.md)
- [Översikt över Azure-nätverkssäkerhet](network-overview.md)
- [Säkerhetsöversikt för Azure-databasen](database-security-overview.md)
- [Säkerhetsöversikt för virtuella Azure-datorer](virtual-machines-overview.md)
- [Datakryptering i vila](encryption-atrest.md)
- [Metodtips för datasäkerhet och kryptering](data-encryption-best-practices.md)
