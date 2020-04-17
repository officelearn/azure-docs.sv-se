---
title: Azure Disk Encryption med Azure AD-app förutsättningar (tidigare version)
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 029743dbb44ab0ebb6ab8f695c0c966137c45107
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459805"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure DiskKryptering med Azure AD (tidigare version)

Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure Active Directory (Azure AD) programparameter för att aktivera VM-diskkryptering. Med den nya versionen behöver du inte längre ange Azure AD-autentiseringsuppgifter under aktivera krypteringssteget. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrarna med hjälp av den nya versionen. Instruktioner om hur du aktiverar VM-diskkryptering med den nya versionen finns i [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.

Den här artikeln innehåller tillägg till [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md) med ytterligare krav och förutsättningar för Azure Disk Encryption med Azure AD (tidigare version).

Informationen i dessa avsnitt förblir densamma:

- [Virtuella datorer och operativsystem som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Ytterligare vm-krav](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Nätverk och grupprinciper

Om du vill aktivera Azure Disk Encryption-funktionen med hjälp av den äldre AAD-parametersyntaxen måste infrastrukturen som en tjänst (IaaS) virtuella datorer uppfylla följande konfigurationskrav för nätverksslutpunkt: 
  - För att få en token för att ansluta till ditt nyckelvalv måste IaaS \[VM kunna\]ansluta till en Azure AD-slutpunkt, login.microsoftonline.com .
  - Om du vill skriva krypteringsnycklarna till nyckelvalvet måste IaaS VM kunna ansluta till nyckelvalvets slutpunkt.
  - IaaS-datorn måste kunna ansluta till en Azure-lagringsslutpunkt som är värd för Azure-tilläggsdatabasen och ett Azure-lagringskonto som är värd för VHD-filerna.
  -  Om din säkerhetsprincip begränsar åtkomsten från virtuella Azure-datorer till internet kan du lösa föregående URI och konfigurera en specifik regel för att tillåta utgående anslutning till IPs. Mer information finns i [Azure Key Vault bakom en brandvägg](../../key-vault/general/access-behind-firewall.md).
  - Om TLS 1.0 är inaktiverat och .NET-versionen inte uppdateras till 4.6 eller senare i Windows, gör följande registerändring det möjligt för Azure Disk Encryption att välja den nyare TLS-versionen:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Grupprincip
 - Azure Disk Encryption-lösningen använder BitLocker-klientskyddet för virtuella datorer med Windows IaaS. För domänanslutna virtuella datorer ska du inte driva några grupprinciper som framtvingar TPM-skydd. Information om grupprincipen för alternativet **Tillåt BitLocker utan kompatibel TPM**finns i [BitLocker Group Policy reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- BitLocker-principen för domänanslutna virtuella datorer med en anpassad grupprincip måste innehålla följande inställning: [Konfigurera användarlagring av BitLocker-återställningsinformation -> Tillåt 256-bitars återställningsnyckel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption misslyckas när anpassade grupprincipinställningar för BitLocker är inkompatibla. På datorer som inte har rätt principinställning använder du den nya principen, tvingar den nya principen att uppdatera (gpupdate.exe /force) och startar sedan om det behövs. 

## <a name="encryption-key-storage-requirements"></a>Lagringskrav för krypteringsnyckel 

Azure Disk Encryption kräver Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter. Ditt nyckelvalv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Nästa steg

- [Skapa och konfigurera ett nyckelvalv för Azure Disk-kryptering med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)
- [Aktivera Azure Disk Encryption med Azure AD på virtuella Linux-datorer (tidigare version)](disk-encryption-linux-aad.md)
- [Azure Disk Kryptering förutsättningar CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Kryptering förutsättningar PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)