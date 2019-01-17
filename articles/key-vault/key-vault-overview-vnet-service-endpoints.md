---
ms.assetid: ''
title: Tjänstslutpunkter i virtuella nätverk för Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Översikt över tjänstslutpunkter i virtuella nätverk för Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 01/02/2019
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 2c035b7c6bbd687ca2922d5f52e2ddaa0c96e50c
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351433"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Tjänstslutpunkter i virtuella nätverk för Azure Key Vault

Tjänstslutpunkter för virtuellt nätverk för Azure Key Vault kan du begränsa åtkomsten till ett angivet virtuellt nätverk. Slutpunkter kan du begränsa åtkomsten till en lista över adressintervall för IPv4 (internet protocol version 4). Alla användare som ansluter till ditt nyckelvalv från utanför dessa källor nekas åtkomst.

Det finns ett viktigt undantag till den här begränsningen. Om en användare har valt in så att betrodda Microsoft-tjänster, kan anslutningar från dessa tjänster via brandväggen. Till exempel dessa tjänster omfattar Office 365 Exchange Online, Office 365 SharePoint Online, Azure compute, Azure Resource Manager och Azure Backup. Dessa användare fortfarande måste uppvisa en giltig Azure Active Directory-token och måste ha behörigheter (konfigurerat som åtkomstprinciper) till den begärda åtgärden. Mer information finns i [tjänstslutpunkter i virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Användningsscenarier

Du kan konfigurera [Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md) att neka åtkomst för trafik från alla nätverk (inklusive internet-trafik) som standard. Du kan ge åtkomst till trafik från specifika Azure-nätverk och offentliga internet, IP-adress-intervall, så att du kan skapa en säker nätverksgräns för dina program.

> [!NOTE]
> Key Vault-brandväggar och virtuella Nätverksregler gäller endast för den [dataplanet](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) av Key Vault. Key Vault-kontrollplanåtgärder (som att skapar, ta bort och ändra åtgärder, inställning av åtkomstprinciper, inställningen brandväggar och virtuella Nätverksregler) påverkas inte av brandväggar och virtuella Nätverksregler.

Här följer några exempel på hur du kan använda tjänstslutpunkter:

* Du använder Key Vault för att lagra krypteringsnycklar, hemligheter och certifikat, och du vill blockera åtkomst till ditt nyckelvalv från det offentliga internet.
* Vill du låsa åtkomsten till ditt nyckelvalv så att bara ditt program eller en kort lista med avsedda värdar kan ansluta till ditt nyckelvalv.
* Du har ett program som körs i Azure-nätverk och det här virtuella nätverket är låsta för all inkommande och utgående trafik. Programmet måste fortfarande ansluta till Key Vault för att hämta hemligheter eller certifikat eller använda kryptografiska nycklar.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurera Key Vault-brandväggar och virtuella nätverk

Här är de steg som krävs för att konfigurera brandväggar och virtuella nätverk. De här stegen gäller oavsett om du använder PowerShell, Azure CLI eller Azure-portalen.

1. Aktivera [Key Vault-loggning](key-vault-logging.md) kan se loggar för avancerad åtkomst. Det underlättar vid diagnostik, när brandväggar och virtuella Nätverksregler förhindra åtkomst till ett nyckelvalv. (Det här steget är valfritt men rekommenderas.)
2. Aktivera **tjänstens slutpunkter för key vault** för målets virtuella nätverk och undernät.
3. Ange brandväggar och virtuella Nätverksregler för ett nyckelvalv att begränsa åtkomst till nyckelvalvet från specifika virtuella nätverk, undernät och IPv4-adressintervall.
4. Om det här nyckelvalvet måste vara tillgängliga för alla betrodda Microsoft-tjänster, aktivera alternativet för att tillåta **betrodda Azure-tjänster** att ansluta till Key Vault.

Mer information finns i [konfigurera Azure Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md).

> [!IMPORTANT]
> När brandväggsregler är aktiverat kan användarna kan bara utföra Key Vault [dataplanet](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) åtgärder när deras begäranden som kommer från tillåtna virtuella nätverk eller IPv4-adressintervall. Detta gäller även för åtkomst till Nyckelvalv från Azure-portalen. Även om användarna kan bläddra till ett nyckelvalv i Azure Portal, kanske de inte att lista nycklar, hemligheter eller certifikat om klientdatorn inte är i listan över tillåtna. Detta påverkar även väljaren för Key Vault med andra Azure-tjänster. Användare kanske kan se listan över nyckelvalv, men inte att lista nycklar, om brandväggsregler förhindra klientdatorn.


> [!NOTE]
> Tänk på följande konfigurationsbegränsningar:
> * Högst 127 virtuella Nätverksregler och 127 IPv4-regler är tillåtna. 
> * Små-adressintervall som använder det ”/ 31” eller ”/ 32” prefix storlekar inte stöds. Konfigurera i stället dessa intervall genom att använda enskilda regler för IP-adress.
> * IP-Nätverksregler tillåts endast för offentliga IP-adresser. IP-adressintervall som reserverats för privata nätverk (som definieras i RFC 1918) är inte tillåtna i IP-regler. Privata nätverk innehåller adresser som börjar med **10.**, **172.16.**, och **192.168.**. 
> * Endast IPv4-adresser stöds just nu.

## <a name="trusted-services"></a>Betrodda tjänster

Här är en lista över betrodda tjänster som får åtkomst till key vault om den **Tillåt att betrodda tjänster** är aktiverat.

|Betrodd tjänst|Användningsscenarier|
| --- | --- |
|Distributionstjänst för Azure Virtual Machines|[Distribuera certifikat till virtuella datorer från kundhanterad Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Distributionstjänst för Azure Resource Manager-mallar|[Skicka säkra värden under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Azure Disk Encryption-volymkrypteringstjänst|Tillåt åtkomst till BitLocker Key (Windows-VM) eller DM lösenfras (Linux VM) och krypteringsnyckel, under distributionen av virtuella datorer. På så sätt kan [Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Azure Backup|Tillåt säkerhetskopiering och återställning av relevanta nycklar och hemligheter under säkerhetskopieringen av virtuella datorer i Azure, med hjälp av [Azure Backup](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online och SharePoint Online|Tillåt åtkomst till kunden nyckel för Azure Storage Service Encryption med [nyckel kund](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Tillåt åtkomst till klientnyckel för [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Distribuera Azure Web App-certifikat via Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/).|
|Azure SQL Database|[Transparent datakryptering med Bring Your Own Key-stöd för Azure SQL Database och Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Kryptering av lagringstjänst med Kundhanterade nycklar i Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Kryptering av data i Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) med en kundhanterad nyckel.|
|Azure databricks|[Snabba, enkla och samarbetsfunktioner Apache Spark-baserad analytics-tjänsten](../azure-databricks/what-is-azure-databricks.md)|



> [!NOTE]
> Du måste konfigurera relevanta åtkomstprinciper för Key Vault så att de motsvarande tjänsterna för att få åtkomst till Key Vault.

## <a name="next-steps"></a>Nästa steg

* [Säkra ditt nyckelvalv](key-vault-secure-your-key-vault.md)
* [Konfigurera Azure Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md)
