---
title: Azure DiskKryptering med Azure AD (tidigare version)
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 025d02ccdf38e72682cf67cc07a8b2edd549e599
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081582"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure DiskKryptering med Azure AD (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera VM-diskkryptering. Med den nya versionen behöver du inte längre ange Azure AD-autentiseringsuppgifter under aktivera krypteringssteget. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrar med den nya versionen. Information om hur du visar instruktioner för att aktivera VM-diskkryptering med den nya versionen finns i [Azure Disk Encryption for Windows VMs](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.**

Den här artikeln kompletterar [Azure Disk Encryption för Virtuella Windows-datorer](disk-encryption-overview.md) med ytterligare krav och förutsättningar för Azure Disk-kryptering med Azure AD (tidigare version). Avsnittet [Virtuella datorer och operativsystem som stöds](disk-encryption-overview.md#supported-vms-and-operating-systems) förblir desamma.

## <a name="networking-and-group-policy"></a>Nätverk och grupprinciper

**Om du vill aktivera Azure Disk Encryption-funktionen med den äldre AAD-parametersyntaxen måste IaaS virtuella datorer uppfylla följande konfigurationskrav för nätverksslutpunkt:** 
  - För att få en token för att ansluta till ditt nyckelvalv måste IaaS \[VM kunna\]ansluta till en Azure Active Directory-slutpunkt, login.microsoftonline.com .
  - Om du vill skriva krypteringsnycklarna till nyckelvalvet måste IaaS VM kunna ansluta till nyckelvalvets slutpunkt.
  - IaaS-datorn måste kunna ansluta till en Azure-lagringsslutpunkt som är värd för Azure-tilläggsdatabasen och ett Azure-lagringskonto som är värd för VHD-filerna.
  -  Om din säkerhetsprincip begränsar åtkomsten från virtuella Azure-datorer till Internet kan du lösa föregående URI och konfigurera en specifik regel för att tillåta utgående anslutning till IPs. Mer information finns i [Azure Key Vault bakom en brandvägg](../../key-vault/key-vault-access-behind-firewall.md).
  - Den virtuella datorn som ska krypteras måste vara konfigurerad för att använda TLS 1.2 som standardprotokoll. Om TLS 1.0 uttryckligen har inaktiverats och .NET-versionen inte har uppdaterats till 4.6 eller högre, gör följande registerändring det möjligt för ADE att välja den nyare TLS-versionen:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Grupprincip:**
 - Azure Disk Encryption-lösningen använder BitLocker-klientskyddet för virtuella datorer med Windows IaaS. För domänanslutna virtuella datorer ska du inte driva några gruppprinciper som framtvingar TPM-skydd. Information om grupprincipen "Tillåt BitLocker utan kompatibel TPM" finns i [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  BitLocker-principen för domänanslutna virtuella datorer med anpassad gruppprincip måste innehålla följande inställning: [Konfigurera användarlagring av BitLocker-återställningsinformation -> Tillåt 256-bitars återställningsnyckel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption misslyckas när anpassade grupprincipinställningar för BitLocker är inkompatibla. På datorer som inte har rätt principinställning använder du den nya principen, tvingar den nya principen att uppdatera (gpupdate.exe /force) och sedan kan det krävas att starta om.  

## <a name="encryption-key-storage-requirements"></a>Lagringskrav för krypteringsnyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter. Ditt nyckelvalv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Nästa steg

- [Skapa och konfigurera ett nyckelvalv för Azure Disk-kryptering med Azure AD (tidigare version)](disk-encryption-key-vault-aad.md)
- [Aktivera Azure Disk Encryption med Azure AD på virtuella Windows-datorer (tidigare version)](disk-encryption-windows-aad.md)
- [Azure Disk Kryptering förutsättningar CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Kryptering förutsättningar PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
