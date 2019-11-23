---
title: Aktivera Azure Disk Encryption för virtuella Windows-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Windows-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72435746"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption för virtuella Windows-datorer 

Azure Disk Encryption skyddar och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Den använder [BitLocker](https://en.wikipedia.org/wiki/BitLocker) -funktionen i Windows för att tillhandahålla volym kryptering för operativ system och data diskar för virtuella Azure-datorer (VM) och är integrerat med [Azure Key Vault](../../key-vault/index.yml) som hjälper dig att styra och hantera disk krypterings nycklar och hemligheter. 

Om du använder [Azure Security Center](../../security-center/index.yml)får du en avisering om du har virtuella datorer som inte är krypterade. Aviseringar visas med hög angelägenhetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Azure Security Center disk encryption-avisering](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) . 
> - Vissa rekommendationerna kan öka data, nätverk eller Resursanvändning för beräkning, vilket resulterar i ytterligare kostnader för licens eller prenumeration. Du måste ha en giltig aktiv Azure-prenumeration att skapa resurser i Azure i regionerna som stöds.

Du kan lära dig grunderna i Azure Disk Encryption för Windows på bara några minuter med guiden [skapa och kryptera en virtuell Windows-dator med Azure CLI snabb start](disk-encryption-cli-quickstart.md) eller [skapa och kryptera en virtuell Windows-dator med Azure PowerShell snabb start](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Virtuella datorer och operativ system som stöds

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

Virtuella Windows-datorer är tillgängliga i [flera storlekar](sizes-general.md). Azure Disk Encryption är inte tillgänglig på virtuella datorer med [Basic, A-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/)eller på virtuella datorer med mindre än 2 GB minne.

Azure Disk Encryption är också tillgängligt för virtuella datorer med Premium Storage.

### <a name="supported-operating-systems"></a>Operativsystem som stöds

- Windows-klient: Windows 8 och senare.
- Windows Server: Windows Server 2008 R2 och senare.  
 
> [!NOTE]
> Windows Server 2008 R2 kräver att .NET Framework 4,5 installeras för kryptering. installera den från Windows Update med den valfria uppdateringen Microsoft .NET Framework 4.5.2 för Windows Server 2008 R2 x64-baserade system ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core och Windows Server 2016 Core kräver att BdeHdCfg-komponenten installeras på den virtuella datorn för kryptering.


## <a name="networking-requirements"></a>Nätverks krav
Om du vill aktivera Azure Disk Encryption måste de virtuella datorerna uppfylla följande konfigurations krav för nätverks slut punkt:
  - Om du vill hämta en token för att ansluta till ditt nyckel valv måste den virtuella Windows-datorn kunna ansluta till en Azure Active Directory-slutpunkt \[login.microsoftonline.com\].
  - Om du vill skriva krypterings nycklarna till ditt nyckel valv måste den virtuella Windows-datorn kunna ansluta till Key Vault-slutpunkten.
  - Den virtuella Windows-datorn måste kunna ansluta till en Azure Storage-slutpunkt som är värd för Azure Extension-lagringsplatsen och ett Azure Storage-konto som är värd för VHD-filerna.
  -  Om din säkerhetsprincip begränsar åtkomst från virtuella Azure-datorer till Internet, kan du matcha den föregående URI: N och konfigurera en specifik regel som tillåter utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brandvägg](../../key-vault/key-vault-access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>grupprincip krav

Azure Disk Encryption använder BitLockers externa nyckel skydd för virtuella Windows-datorer. Domänanslutna virtuella datorer, inte skicka någon grupprinciper som tillämpar TPM-skydd. Läs om hur en grupprincip för ”Tillåt BitLocker utan en kompatibel TPM” [gruppolicy referens för BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

BitLocker-principen på domänanslutna virtuella datorer med anpassad grup princip måste innehålla följande inställning: [Konfigurera användar lagring av BitLocker-återställningsinformation – > tillåt 256-bitars återställnings nyckel](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption Miss fungerar när anpassade grup princip inställningar för BitLocker inte är kompatibla. Tillämpa den nya principen på datorer som inte har rätt principinställningen tvinga den nya principen för att uppdatera (gpupdate.exe/Force) och sedan omstart kan krävas.

Azure Disk Encryption Miss fungerar om grup principen på domän nivå blockerar AES-CBC-algoritmen som används av BitLocker.

## <a name="encryption-key-storage-requirements"></a>Lagrings krav för krypterings nyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt nyckel valv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologi
I följande tabell definieras några vanliga termer som används i dokumentationen för Azure Disk Encryption:

| Terminologi | Definition |
| --- | --- |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som har baserat på FIPS Federal Information Processing Standards () validerade och maskinvarubaserade säkerhetsmoduler. Dessa standarder bidra till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerad för att hantera och administrera Azure-resurser från kommandoraden.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en bransch känd Windows Volume Encryption-teknik som används för att aktivera disk kryptering på virtuella Windows-datorer. |
| Nyckel krypterings nyckel (KEK) | Den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller figursätta hemligheten. Du kan ange en maskinvarusäkerhetsmodul (HSM)-skyddade nyckel eller programvaruskyddad nyckel. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdlets](/powershell/azure/overview). |


## <a name="next-steps"></a>Nästa steg

- [Snabb start – skapa och kryptera en virtuell Windows-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Snabb start – skapa och kryptera en virtuell Windows-dator med Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenarier på virtuella Windows-datorer](disk-encryption-windows.md)
- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md)


