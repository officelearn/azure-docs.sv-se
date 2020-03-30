---
title: Slutpunkter för virtuella nätverkstjänster för Azure Key Vault – Azure Key Vault | Microsoft-dokument
description: Översikt över slutpunkter för virtuella nätverkstjänster för Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 2d7ea5618f4c0e8b6d348ea6c34127492b3e1096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457414"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Slutpunkter för virtuella nätverkstjänster för Azure Key Vault

Slutpunkterna för den virtuella nätverkstjänsten för Azure Key Vault gör att du kan begränsa åtkomsten till ett angivet virtuellt nätverk. Ändpunkterna gör det också möjligt att begränsa åtkomsten till en lista över IPv4-adressintervall (internetprotokoll version 4). Alla användare som ansluter till ditt nyckelvalv utanför dessa källor nekas åtkomst.

Det finns ett viktigt undantag från denna begränsning. Om en användare har valt att tillåta betrodda Microsoft-tjänster, släpps anslutningar från dessa tjänster genom brandväggen. Dessa tjänster omfattar till exempel Office 365 Exchange Online, Office 365 SharePoint Online, Azure-beräkning, Azure Resource Manager och Azure Backup. Sådana användare måste fortfarande presentera en giltig Azure Active Directory-token och måste ha behörigheter (konfigurerade som åtkomstprinciper) för att utföra den begärda åtgärden. Mer information finns i [slutpunkter för virtuella nätverkstjänster.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan konfigurera [Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md) för att neka åtkomst till trafik från alla nätverk (inklusive internettrafik) som standard. Du kan bevilja åtkomst till trafik från specifika virtuella Azure-nätverk och offentliga IP-adressintervall för internet, så att du kan skapa en säker nätverksgräns för dina program.

> [!NOTE]
> Key Vault-brandväggar och virtuella nätverksregler gäller endast för [dataplanet](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) i Key Vault. Key Vault-kontrollplansåtgärder (till exempel skapa, ta bort och ändra åtgärder, ange åtkomstprinciper, ställa in brandväggar och virtuella nätverksregler) påverkas inte av brandväggar och virtuella nätverksregler.

Här är några exempel på hur du kan använda tjänstslutpunkter:

* Du använder Key Vault för att lagra krypteringsnycklar, programhemligheter och certifikat, och du vill blockera åtkomst till ditt nyckelvalv från det offentliga internet.
* Du vill låsa åtkomsten till nyckelvalvet så att endast ditt program, eller en kort lista med utsedda värdar, kan ansluta till nyckelvalvet.
* Du har ett program som körs i ditt virtuella Azure-nätverk och det här virtuella nätverket är låst för all inkommande och utgående trafik. Ditt program måste fortfarande ansluta till Key Vault för att hämta hemligheter eller certifikat, eller använda kryptografiska nycklar.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Key Vault-brandväggar och virtuella nätverk

Här är de steg som krävs för att konfigurera brandväggar och virtuella nätverk. De här stegen gäller oavsett om du använder PowerShell, Azure CLI eller Azure-portalen.

1. Aktivera [Loggning av Key Vault](key-vault-logging.md) för att se detaljerade åtkomstloggar. Detta hjälper till med diagnostik, när brandväggar och virtuella nätverksregler förhindrar åtkomst till ett nyckelvalv. (Det här steget är valfritt, men rekommenderas starkt.)
2. Aktivera **tjänstslutpunkter för nyckelvalv** för virtuella nätverk och undernät.
3. Ange brandväggar och virtuella nätverksregler för ett nyckelvalv för att begränsa åtkomsten till nyckelvalvet från specifika virtuella nätverk, undernät och IPv4-adressintervall.
4. Om det här nyckelvalvet måste vara tillgängligt för alla betrodda Microsoft-tjänster aktiverar du alternativet att låta **Betrodda Azure-tjänster** ansluta till Key Vault.

Mer information finns i [Konfigurera Azure Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md).

> [!IMPORTANT]
> När brandväggsregler har gällande kan användare bara utföra Key [Vault-dataplansåtgärder](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) när deras begäranden kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även för åtkomst till Key Vault från Azure-portalen. Även om användare kan bläddra till ett nyckelvalv från Azure-portalen kanske de inte kan lista nycklar, hemligheter eller certifikat om klientdatorn inte finns i listan över tillåtna. Detta påverkar också Nyckelvalvväljaren av andra Azure-tjänster. Användare kanske kan se en lista över nyckelvalv, men inte listnycklar, om brandväggsregler förhindrar klientdatorn.


> [!NOTE]
> Var medveten om följande konfigurationsbegränsningar:
> * Högst 127 virtuella nätverksregler och 127 IPv4-regler är tillåtna. 
> * Små adressintervall som använder prefixstorlekarna "/31" eller "/32" stöds inte. Konfigurera i stället dessa intervall med hjälp av individuella IP-adressregler.
> * IP-nätverksregler är endast tillåtna för offentliga IP-adresser. IP-adressintervall som reserverats för privata nätverk (enligt definitionen i RFC 1918) är inte tillåtna i IP-regler. Privata nätverk omfattar adresser som börjar med **10.**, **172.16-31**och **192.168.**. 
> * Endast IPv4-adresser stöds för tillfället.

## <a name="trusted-services"></a>Betrodda tjänster

Här är en lista över betrodda tjänster som har åtkomst till ett nyckelvalv om alternativet **Tillåt betrodda tjänster** är aktiverat.

|Betrodd tjänst|Användningsscenarier som stöds|
| --- | --- |
|Distributionstjänst för virtuella datorer i Azure|[Distribuera certifikat till virtuella datorer från kundhanterad Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Distributionstjänsten för Azure Resource Manager-mall|[Passera säkra värden under distributionen](../azure-resource-manager/templates/key-vault-parameter.md).|
|Krypteringstjänst för azure diskkrypteringsvolym|Tillåt åtkomst till BitLocker Key (Windows VM) eller DM Passphrase (Linux VM) och nyckelkrypteringsnyckel under distribution av virtuella datorer. Detta möjliggör [Azure Disk Encryption](../security/fundamentals/encryption-overview.md).|
|Azure Backup|Tillåt säkerhetskopiering och återställning av relevanta nycklar och hemligheter under säkerhetskopiering av virtuella Azure-datorer med hjälp av [Azure Backup](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online & SharePoint Online|Tillåt åtkomst till kundnyckel för Azure Storage Service Encryption med [kundnyckel](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|Tillåt åtkomst till klientnyckel för [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure Apptjänst|[Distribuera Azure Web App-certifikat via Nyckelvalvet](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent datakryptering med stöd för din egen nyckel för Azure SQL Database och Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Kryptering av lagringstjänster med kundhanterade nycklar i Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Kryptering av data i Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) med en kundhanterad nyckel.|
|Azure Databricks|[Snabb, enkel och samarbetsinriktad Apache Spark-baserad analystjänst](../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[Distribuera certifikat för anpassad domän från Nyckelvalv med MSI](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Hämta autentiseringsuppgifter för datalagring i Key Vault från Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Tillåt åtkomst till ett nyckelvalv för scenario med hanterade nycklar som hanteras av kunden](https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key)|
|Azure Service Bus|[Tillåt åtkomst till ett nyckelvalv för scenario med hanterade nycklar som hanteras av kunden](https://docs.microsoft.com/azure/service-bus-messaging/configure-customer-managed-key)|
|Azure Import/Export| [Använda kundhanterade nycklar i Azure Key Vault for Import/Export-tjänsten](https://docs.microsoft.com/azure/storage/common/storage-import-export-encryption-key-portal)

> [!NOTE]
> Du måste ställa in relevanta principer för nyckelvalv för att tillåta motsvarande tjänster att få åtkomst till Key Vault.

## <a name="next-steps"></a>Nästa steg

* [Säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md)
* [Konfigurera Azure Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md)
