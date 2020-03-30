---
title: Aktivera Azure DiskKryptering för Virtuella Windows-datorer
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption för virtuella Windows-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266785"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Virtuella Azure-diskkryptering för virtuella Windows-datorer 

Azure Disk Encryption hjälper till att skydda dina data så att din verksamhet uppfyller både interna och externa krav och åtaganden. Den använder [Bitlocker-funktionen](https://en.wikipedia.org/wiki/BitLocker) i Windows för att tillhandahålla volymkryptering för operativsystem och datadiskar för virtuella Azure-datorer (VMs) och är integrerad med [Azure Key Vault](../../key-vault/index.yml) för att hjälpa dig att styra och hantera diskkrypteringsnycklar och hemligheter. 

Om du använder [Azure Security Center](../../security-center/index.yml)får du ett meddelande om du har virtuella datorer som inte är krypterade. Aviseringarna visas som hög allvarlighetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Krypteringsavisering för Azure Security Center-disk](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera din virtuella dator. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version).](disk-encryption-overview-aad.md) 
> - Vissa rekommendationer kan öka data-, nätverks- eller beräkningsresursanvändningen, vilket resulterar i ytterligare licens- eller prenumerationskostnader. Du måste ha en giltig aktiv Azure-prenumeration för att kunna skapa resurser i Azure i de regioner som stöds.

Du kan lära dig grunderna i Azure Disk Encryption för Windows på bara några minuter med [Skapa och kryptera en Windows VM med Azure CLI snabbstart](disk-encryption-cli-quickstart.md) eller [Skapa och kryptera en Windows VM med Azure Powershell snabbstart](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Virtuella datorer och operativsystem som stöds

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

Virtuella datorer i Windows finns i en [rad olika storlekar](sizes-general.md). Azure Disk Encryption är inte tillgängligt på [virtuella datorer i Basic, A-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/)eller på virtuella datorer med mindre än 2 GB minne.

Azure Disk Encryption är också tillgängligt för virtuella datorer med premiumlagring.

### <a name="supported-operating-systems"></a>Operativsystem som stöds

- Windows-klient: Windows 8 och senare.
- Windows Server: Windows Server 2008 R2 och senare.  
 
> [!NOTE]
> Windows Server 2008 R2 kräver att .NET Framework 4.5 installeras för kryptering. installera den från Windows Update med valfria uppdateringen Microsoft .NET Framework 4.5.2 för Windows Server 2008 R2 x64-baserade system ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core och Windows Server 2016 Core kräver att bdehdcfg-komponenten installeras på den virtuella datorn för kryptering.


## <a name="networking-requirements"></a>Krav på nätverk
Om du vill aktivera Azure Disk Encryption måste de virtuella datorerna uppfylla följande konfigurationskrav för nätverksslutpunkt:
  - Om du vill hämta en token för att ansluta till nyckelvalvet måste Windows \[VM kunna\]ansluta till en Azure Active Directory-slutpunkt login.microsoftonline.com .
  - Om du vill skriva krypteringsnycklarna till nyckelvalvet måste Windows VM kunna ansluta till nyckelvalvets slutpunkt.
  - Windows VM måste kunna ansluta till en Azure-lagringsslutpunkt som är värd för Azure-tilläggsdatabasen och ett Azure-lagringskonto som är värd för VHD-filerna.
  -  Om din säkerhetsprincip begränsar åtkomsten från virtuella Azure-datorer till Internet kan du lösa föregående URI och konfigurera en specifik regel för att tillåta utgående anslutning till IPs. Mer information finns i [Azure Key Vault bakom en brandvägg](../../key-vault/key-vault-access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Krav på grupprincip

Azure Disk Encryption använder BitLocker extern nyckelskydd för Windows virtuella datorer. För domänanslutna virtuella datorer ska du inte driva några gruppprinciper som framtvingar TPM-skydd. Information om grupprincipen "Tillåt BitLocker utan kompatibel TPM" finns i [BitLocker Group Policy Reference](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

BitLocker-principen för domänanslutna virtuella datorer med anpassad gruppprincip måste innehålla följande inställning: [Konfigurera användarlagring av BitLocker-återställningsinformation -> Tillåt 256-bitars återställningsnyckel](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption misslyckas när anpassade grupprincipinställningar för BitLocker är inkompatibla. På datorer som inte har rätt principinställning använder du den nya principen, tvingar den nya principen att uppdatera (gpupdate.exe /force) och sedan kan det krävas att starta om.

Azure Disk Encryption misslyckas om domännivågruppprincip blockerar AES-CBC-algoritmen, som används av BitLocker.

## <a name="encryption-key-storage-requirements"></a>Lagringskrav för krypteringsnyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter. Ditt nyckelvalv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologi
I följande tabell definieras några av de vanliga termer som används i Azure-diskkrypteringsdokumentation:

| Terminologi | Definition |
| --- | --- |
| Azure Key Vault | Key Vault är en kryptografisk, nyckelhanteringstjänst som baseras på FIPS-validerade maskinvarusäkerhetsmoduler (Federal Information Processing Standards). Dessa standarder hjälper till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i dokumentationen till [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) och skapa och konfigurera ett [nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerad för att hantera och administrera Azure-resurser från kommandoraden.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en branscherkänd Windows-volymkrypteringsteknik som används för att aktivera diskkryptering på virtuella windows-datorer. |
| Nyckelkrypteringsnyckel (KEK) | Den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller slå in hemligheten. Du kan tillhandahålla en HSM-skyddad nyckel (Hardware Security Module) eller en programvaruskyddad nyckel. Mer information finns i dokumentationen till [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) och skapa och konfigurera ett [nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdlets](/powershell/azure/overview). |


## <a name="next-steps"></a>Nästa steg

- [Snabbstart – Skapa och kryptera en Windows-virtuell dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Snabbstart – Skapa och kryptera en Windows-virtuell dator med Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenarier på virtuella Windows-datorer](disk-encryption-windows.md)
- [Azure Disk Kryptering förutsättningar CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Kryptering förutsättningar PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md)


