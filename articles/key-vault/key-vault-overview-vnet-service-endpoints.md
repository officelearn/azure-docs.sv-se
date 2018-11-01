---
ms.assetid: ''
title: VNET-tjänstslutpunkter för Azure Key Vault | Microsoft Docs
description: Översikt över tjänstslutpunkter i virtuella nätverk för Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 6e0029e051e418bc54471284547329a0b0a2e9cd
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50246662"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Tjänstslutpunkter i virtuella nätverk för Azure Key Vault

Den tjänstslutpunkter i virtuella nätverk för Key Vault kan du begränsa åtkomsten till det angivna virtuella nätverket och/eller till en lista över adressintervall för IPv4 (Internet Protocol version 4). Alla anroparen som ansluter till ditt nyckelvalv från utanför dessa källor kommer att nekas åtkomst. Om kunden har valt in för att tillåta ”betrodda Microsoft-tjänster” som Office 365 Exchange Online, Office 365 SharePoint Online, Azure-beräkning, Azure Resource Manager, Azure Backup osv, kan anslutningar från dessa tjänster via brandväggen. Naturligtvis sådana anropare måste uppvisa en giltig AAD-token måste ha behörigheter (konfigurerat som åtkomstprinciper) till den begärda åtgärden. Läs mer teknisk information om [tjänstslutpunkter i virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan konfigurera [Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md) att neka åtkomst för trafik från alla nätverk (inklusive Internet-trafik) som standard. Åtkomst kan beviljas adressintervall trafik från specifika Azure-nätverk och/eller offentliga internet IP-adress, så att du kan skapa en säker nätverksgräns för dina program.

> [!NOTE]
> Key Vault-brandväggar och virtuella Nätverksregler gäller endast för nyckelvalvet [dataplanet](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control). Kontrollplanåtgärder för Nyckelvalv (t.ex nyckelvalvet skapa, ta bort, ändra åtgärder, ställa in åtkomstprinciper, brandväggar och virtuella Nätverksregler) påverkas inte av brandväggar och virtuella Nätverksregler.

Exempel:
* Om du använder Key Vault för att lagra krypteringsnycklar, hemligheter, certifikat och vill blockera åtkomst till ditt nyckelvalv från offentliga internet.
* Vill du låsa åtkomsten till ditt nyckelvalv så att bara ditt program eller en kort lista med avsedda värdar kan ansluta till ditt nyckelvalv
* Du har ett program som körs i Azure-nätverk (VNET) och det här virtuella nätverket är låsta för all inkommande och utgående trafik. Programmet måste fortfarande ansluta till nyckelvalvet för att hämta hemligheter eller certifikat eller använda kryptografiska nycklar.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Key Vault-brandväggar och virtuella nätverk

Här är de steg som krävs för att konfigurera brandväggar och virtuella nätverk. De här stegen är desamma oavsett vilken vilka gränssnitt (PowerShell, CLI, Azure portal) du använder för att ställa in brandväggen och virtuella Nätverksregler.
1. Valfritt men rekommenderas: aktivera [key vault-loggning](key-vault-logging.md) kan se loggar för avancerad åtkomst. Detta hjälper dig i diagnostik när brandväggar och virtuella Nätverksregler förhindra åtkomst till ett nyckelvalv.
2. Aktivera tjänstslutpunkter för key vault för målets virtuella nätverk och undernät
3. Ange brandväggar och virtuella Nätverksregler för ett nyckelvalv att begränsa åtkomst till nyckelvalvet från specifika virtuella nätverk, undernät och IPv4-adressintervall.
4. Om det här nyckelvalvet måste vara tillgängliga för alla betrodda Microsoft-tjänster, måste du aktivera alternativet för att tillåta ”betrodda Azure Services” att ansluta till nyckelvalvet.

Referera till [konfigurera Azure Key Vault brandväggar och virtuella nätverk](key-vault-network-security.md) stegvisa anvisningar.

> [!IMPORTANT]
> När brandväggsregler i praktiken är alla Nyckelvalv [dataplanet](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) åtgärder kan endast utföras när anroparen begäranden som kommer från tillåtna virtuella nätverk eller IPV4-adressintervall. (Mer information om data Hanteringsplanet plan vs genom att läsa [detta](../key-vault/key-vault-secure-your-key-vault.md#management-plane-access-control) detta gäller även för åtkomst till nyckelvalv från Azure-portalen. När en användare kan webbläsaren till ett nyckelvalv från Azure-portalen, kanske de inte att lista nycklar/hemligheter/certificates om klientdatorn inte är i listan över tillåtna. Detta påverkar även 'Key Vault väljare' med andra Azure-tjänster. Användare kanske kan se lista över viktiga valv men inte att lista nycklar, om brandväggsregler förhindra klientdatorn.


> [!NOTE]
> * En högsta 127 VNET-regler och 127 IPv4-regler är tillåtna. 
> * Små adressintervall med hjälp av ”/ 31” eller ”/ 32” prefix storlekar inte stöds. Dessa områden ska konfigureras med regler för enskilda IP-adress.
> * IP-Nätverksregler tillåts endast för offentliga IP-adresser. IP-adressintervall som reserverats för privata nätverk (som definieras i RFC 1918) är inte tillåtna i IP-regler. Privata nätverk innehåller adresser som börjar med *10.*\*, *172.16.*\*, och *192.168.*\*. 
> * Endast IPv4-adresser stöds just nu.

## <a name="trusted-services"></a>Betrodda tjänster
Här är en lista över betrodda tjänster som ska kunna komma åt ett nyckelvalv om ”Tillåt betrodd tjänster” är aktiverat.

|Betrodd tjänst|Användningsscenarier|
| --- | --- |
|Distributionstjänst för Azure Virtual Machines|[Distribuera certifikat till virtuella datorer från kundhanterad Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Distributionstjänst för Azure Resource Manager-mallar|[Skicka säkra värden under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Azure Disk Encryption-volymkrypteringstjänst|Tillåt åtkomst till BitLocker Key (Windows-VM) eller DM lösenfras (Linux VM) och krypteringsnyckel under distribution av virtuella datorer att aktivera [Azure Disk Encryption](../security/azure-security-disk-encryption.md)|
|Azure Backup|Tillåt säkerhetskopiering och återställning av relevanta nycklar och hemligheter under Azure-datorer med hjälp av [Azure Backup](../backup/backup-introduction-to-azure-backup.md)|
|Exchange Online och SharePoint Online|Tillåt åtkomst till kunden nyckel för kryptering av lagringstjänst med [nyckel kund](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Tillåt åtkomst till klientnyckel för [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|App Services|[Distribuera Azure Web App-certifikat via Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[Transparent datakryptering med Bring Your Own Key-stöd för Azure SQL Database och Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[Kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|[Kryptering av data i Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) med kunden hanterad nyckel|



> [!NOTE]
> Relevanta nyckelvalvets åtkomstprinciper måste anges för att tillåta de motsvarande tjänsterna för att få åtkomst till nyckelvalvet.

## <a name="next-steps"></a>Nästa steg

* [Säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md)
* [Konfigurera Azure Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md)
