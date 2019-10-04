---
title: Aktivera Azure Disk Encryption för virtuella Linux-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure disk kryptering för virtuella Linux-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 61c85d17e88016e5e9067d4be86e1cd290b4b12a
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948189"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption för virtuella Linux-datorer 

Azure Disk Encryption skyddar och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Den använder [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt) funktionen i Linux för att tillhandahålla volym kryptering för operativ system och data diskar för virtuella Azure-datorer (VM) och är integrerat med [Azure Key Vault](../../key-vault/index.yml) för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter. 

Om du använder [Azure Security Center](../../security-center/index.yml)får du en avisering om du har virtuella datorer som inte är krypterade. Aviseringar visas med hög angelägenhetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Azure Security Center disk encryption-avisering](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version)](disk-encryption-overview-aad.md) . 
> - Vissa rekommendationerna kan öka data, nätverk eller Resursanvändning för beräkning, vilket resulterar i ytterligare kostnader för licens eller prenumeration. Du måste ha en giltig aktiv Azure-prenumeration att skapa resurser i Azure i regionerna som stöds.

Du kan lära dig grunderna i Azure Disk Encryption för Linux på bara några minuter med [skapa och kryptera en virtuell Linux-dator med Azure CLI snabb start](disk-encryption-cli-quickstart.md) eller [skapa och kryptera en virtuell Linux-dator med snabb start för Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Virtuella datorer och operativ system som stöds

### <a name="supported-vm-sizes"></a>VM-storlekar som stöds

Virtuella Linux-datorer är tillgängliga i [flera storlekar](sizes.md). Azure Disk Encryption är inte tillgänglig på virtuella datorer med [Basic, A-serien](/pricing/details/virtual-machines/series/)eller på virtuella datorer som inte uppfyller de minsta minnes kraven:

| Virtuell dator | Minsta minnes krav |
|--|--|
| Virtuella Linux-datorer vid endast kryptering av data volymer| 2 GB |
| Virtuella Linux-datorer vid kryptering av både data-och OS-volymer och där roten (/) fil systemets användning är 4 GB eller mindre | 8 GB |
| Virtuella Linux-datorer vid kryptering av både data-och OS-volymer och där roten (/) fil systemets användning är större än 4 GB | Rot fil systemets användning * 2. Till exempel kräver en 16 GB rot fil system användning minst 32 GB RAM-minne |

När operativ systemets disk krypterings process har slutförts på virtuella Linux-datorer kan den virtuella datorn konfigureras för att köras med mindre minne. 

Azure Disk Encryption är också tillgängligt för virtuella datorer med Premium Storage. 

### <a name="supported-operating-systems"></a>Operativsystem som stöds

Azure Disk Encryption stöds på en delmängd av [Azure-godkända Linux-distributioner](endorsed-distros.md), som i sig är en delmängd av alla möjliga distributioner av Linux-servrar.

![Venndiagram av Linux Server-distributioner som stöder Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Linux Server-distributioner som inte har godkänts av Azure stöder inte Azure Disk Encryption. för de som har påtecknats har endast följande distributioner och versioner stöd Azure Disk Encryption:

| Linux-distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 18,04| OS- och disk |
| Ubuntu | 16.04| OS- och disk |
| Ubuntu | 14.04.5</br>[med Azures justerade kernel uppdaterat till 4,15 eller senare](disk-encryption-troubleshooting.md) | OS- och disk |
| RHEL | 7,7 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7,6 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.5 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.4 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.3 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.2 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 6.8 | Data disk (se OBS! nedan) |
| RHEL | 6.7 | Data disk (se OBS! nedan) |
| CentOS | 7,7 | OS- och disk |
| CentOS | 7,6 | OS- och disk |
| CentOS | 7.5 | OS- och disk |
| CentOS | 7.4 | OS- och disk |
| CentOS | 7.3 | OS- och disk |
| CentOS | 7.2n | OS- och disk |
| CentOS | 6.8 | Datadisk |
| openSUSE | 42,3 | Datadisk |
| SLES | 12-SP4 | Datadisk |
| SLES | 12-SP3 | Datadisk |

> [!NOTE]
> Den nya ADE-implementeringen stöds för RHEL OS och datadisk för RHEL7 avbildningar enligt principen betala per användning. ADE stöds för närvarande inte för RHEL-avbildningar (BYOS). 

## <a name="additional-vm-requirements"></a>Ytterligare krav för virtuell dator

Azure Disk Encryption kräver att dm-crypt-och vfat-modulerna finns i systemet. Om du tar bort eller inaktiverar vfat från standard avbildningen så förhindras systemet från att läsa nyckel volymen och hämta den nyckel som behövs för att låsa upp diskarna vid efterföljande omstarter. System härdnings steg som tar bort vfat-modulen från systemet är inte kompatibla med Azure Disk Encryption. 

Innan du aktiverar kryptering måste data diskarna som ska krypteras anges korrekt i/etc/fstab. Använd ett beständigt block enhetsnamn för den här posten som enhetens namn i formatet ”/ dev/sdX” det går inte att förlita sig på som ska associeras med samma disk mellan omstarter, särskilt när kryptering används. Mer information om det här problemet finns i: [Felsöka ändringar av enhets namn för virtuella Linux-datorer](troubleshoot-device-names-problems.md)

Kontrollera att/etc/fstab-inställningarna har konfigurerats korrekt för montering. Kör mount - ett kommando för att konfigurera de här inställningarna, eller starta om den virtuella datorn och utlösa återmontering på så sätt. När detta är slutfört kan du kontrollera resultatet av kommandot lsblk att verifiera att enheten fortfarande är ansluten. 
- Om filen/etc/fstab inte montera enheten korrekt innan du aktiverar kryptering, Azure Disk Encryption inte montera den korrekt.
- Azure Disk Encryption-processen flyttar mount-information från/etc/fstab och i sin egen konfigurationsfilen som en del av krypteringsprocessen. Inte vara alarmed att se posten saknas i/etc/fstab när data diskkryptering har slutförts.
- Innan du startar kryptering måste du stoppa alla tjänster och processer som kan skrivas till monterade data diskar och inaktivera dem, så att de inte startar om automatiskt efter en omstart. Dessa kan hålla filerna öppna på dessa partitioner, vilket förhindrar krypterings proceduren att ommontera dem, vilket orsakar att krypteringen Miss lyckas. 
- Efter omstart tar det tid för Azure Disk Encryption-processen för att montera de nyligen krypterade diskarna. De kommer inte blir tillgängliga omedelbart efter en omstart. Processen behöver tid att börja låsa upp och sedan montera de krypterade enheterna innan är tillgängliga för andra processer att få åtkomst till. Den här processen kan ta mer än en minut efter omstart beroende på system-egenskaper.

Ett exempel på kommandon som kan användas för att montera data diskarna och skapa nödvändiga/etc/fstab-poster finns i [skriptet för Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) (rader 244-248) och [Azure Disk Encryption förutsättningar för PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Nätverks krav

Om du vill aktivera funktionen Azure Disk Encryption måste de virtuella Linux-datorerna uppfylla följande konfigurations krav för nätverks slut punkt:
  - Om du vill hämta en token för att ansluta till ditt nyckel valv måste den virtuella Linux-datorn kunna ansluta till en Azure Active Directory-slutpunkt, @no__t -0login. microsoftonline. com @ no__t-1.
  - Om du vill skriva krypterings nycklarna till ditt nyckel valv måste den virtuella Linux-datorn kunna ansluta till Key Vault-slutpunkten.
  - Den virtuella Linux-datorn måste kunna ansluta till en Azure Storage-slutpunkt som är värd för Azure Extension-lagringsplatsen och ett Azure Storage-konto som är värd för VHD-filerna.
  -  Om din säkerhetsprincip begränsar åtkomst från virtuella Azure-datorer till Internet, kan du matcha den föregående URI: N och konfigurera en specifik regel som tillåter utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brandvägg](../../key-vault/key-vault-access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Lagrings krav för krypterings nyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter. Ditt nyckel valv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologi
I följande tabell definieras några vanliga termer som används i dokumentationen för Azure Disk Encryption:

| Terminologi | Definition |
| --- | --- |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som har baserat på FIPS Federal Information Processing Standards () validerade och maskinvarubaserade säkerhetsmoduler. Dessa standarder bidra till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerad för att hantera och administrera Azure-resurser från kommandoraden.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) är det Linux-baserade, transparenta disk krypterings systemet som används för att aktivera disk kryptering på virtuella Linux-datorer. |
| Nyckel krypterings nyckel (KEK) | Den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller figursätta hemligheten. Du kan ange en maskinvarusäkerhetsmodul (HSM)-skyddade nyckel eller programvaruskyddad nyckel. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -dokumentationen och [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdlets](/powershell/azure/overview). |


## <a name="next-steps"></a>Nästa steg

- [Snabb start – skapa och kryptera en virtuell Linux-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Snabb start – skapa och kryptera en virtuell Linux-dator med Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption scenarier på virtuella Linux-datorer](disk-encryption-linux.md)
- [Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [PowerShell-skript för Azure Disk Encryption krav](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md)


