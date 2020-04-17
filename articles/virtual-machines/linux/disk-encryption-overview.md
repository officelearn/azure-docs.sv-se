---
title: Aktivera Azure Disk Encryption för virtuella Linux-datorer
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption för virtuella Linux-datorer.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: fa7e085f723d4f4c411f52e045c9437d5cb293b3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459788"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Virtuella Azure-diskkryptering för virtuella Linux-datorer 

Azure Disk Encryption hjälper till att skydda dina data så att din verksamhet uppfyller både interna och externa krav och åtaganden. Den använder [DM-Crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volymkryptering för operativsystem och datadiskar för virtuella Azure-datorer (VMs) och är integrerad med [Azure Key Vault](../../key-vault/index.yml) för att hjälpa dig att kontrollera och hantera diskkrypteringsnycklar och hemligheter. 

Om du använder [Azure Security Center](../../security-center/index.yml)får du ett meddelande om du har virtuella datorer som inte är krypterade. Aviseringarna visas som hög allvarlighetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Krypteringsavisering för Azure Security Center-disk](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Om du tidigare har använt Azure Disk Encryption med Azure AD för att kryptera en virtuell dator måste du fortsätta att använda det här alternativet för att kryptera din virtuella dator. Mer information finns i [Azure Disk Encryption med Azure AD (tidigare version).](disk-encryption-overview-aad.md) 
> - Vissa rekommendationer kan öka data-, nätverks- eller beräkningsresursanvändningen, vilket resulterar i ytterligare licens- eller prenumerationskostnader. Du måste ha en giltig aktiv Azure-prenumeration för att kunna skapa resurser i Azure i de regioner som stöds.
> - För närvarande stöder virtuella generationer av 2 inte Azure Disk Encryption. Mer information finns i [Support för virtuella generation 2-datorer på Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)

Du kan lära dig grunderna i Azure Disk Encryption för Linux på bara några minuter med [Skapa och kryptera en Linux-vm med Azure CLI snabbstart](disk-encryption-cli-quickstart.md) eller Skapa och kryptera en [Linux-VM med Azure Powershell snabbstart](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Virtuella datorer och operativsystem som stöds

### <a name="supported-vms"></a>Virtuella datorer som stöds

Virtuella linux-datorer finns i en [rad olika storlekar.](sizes.md) Azure Disk Encryption är inte tillgängligt på [virtuella datorer i Basic, A-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/)eller på virtuella datorer som inte uppfyller dessa minimikrav på minne:

| Virtuell dator | Minsta minneskrav |
|--|--|
| Virtuella Linux-datorer när du bara krypterar datavolymer| 2 GB |
| Linux virtuella datorer vid kryptering av både data- och os-volymer, och där roten (/) filsystemets användning är 4 GB eller mindre | 8 GB |
| Virtuella Linux-datorer när du krypterar både data- och OS-volymer, och där roten (/) filsystemets användning är större än 4 GB | Rotfilsystemet användning * 2. Till exempel kräver en 16 GB rotfilsystemanvändning minst 32 GB RAM |

När OS-diskkrypteringsprocessen är klar på virtuella Linux-datorer kan den virtuella datorn konfigureras för att köras med mindre minne. 

Azure Disk Encryption är också tillgängligt för virtuella datorer med premiumlagring.

Azure Disk Encryption är inte tillgängligt på [virtuella datorer i generation 2 )](generation-2.md#generation-1-vs-generation-2-capabilities)och virtuella datorer i [Lsv2-serien](../lsv2-series.md)). Fler undantag finns i [Azure Disk Encryption: Scenarier som inte stöds](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Operativsystem som stöds

Azure Disk Encryption stöds på en delmängd av [Azure-godkända Linux-distributioner](endorsed-distros.md), som i sig är en delmängd av alla Linux-server möjliga distributioner.

![Venn-diagram över Linux-serverdistributioner som stöder Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

Linux-serverdistributioner som inte stöds av Azure stöder inte Azure Disk Encryption. Av dem som är godkända stöder endast följande distributioner och versioner Azure Disk Encryption:

| Linux-distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 18.04| OS och datadisk |
| Ubuntu | 16.04| OS och datadisk |
| Ubuntu | 14.04.5</br>[med Azure-trimmad kärna uppdaterad till 4.15 eller senare](disk-encryption-troubleshooting.md) | OS och datadisk |
| RHEL | 7.7 | OS och datadisk (se anmärkning nedan) |
| RHEL | 7.6 | OS och datadisk (se anmärkning nedan) |
| RHEL | 7.5 | OS och datadisk (se anmärkning nedan) |
| RHEL | 7.4 | OS och datadisk (se anmärkning nedan) |
| RHEL | 7.3 | OS och datadisk (se anmärkning nedan) |
| RHEL | 7.2 | OS och datadisk (se anmärkning nedan) |
| RHEL | 6.8 | Datadisk (se anmärkning nedan) |
| RHEL | 6.7 | Datadisk (se anmärkning nedan) |
| CentOS | 7.7 | OS och datadisk |
| CentOS | 7.6 | OS och datadisk |
| CentOS | 7.5 | OS och datadisk |
| CentOS | 7.4 | OS och datadisk |
| CentOS | 7.3 | OS och datadisk |
| CentOS | 7.2n | OS och datadisk |
| CentOS | 6.8 | Datadisk |
| openSUSE | 42.3 | Datadisk |
| SLES | 12-SP4 | Datadisk |
| SLES | 12-SP3 | Datadisk |

> [!NOTE]
> Den nya Azure Disk Encryption-implementeringen stöds för RHEL OS och datadisk för RHEL7 Pay-As-You-Go-avbildningar.  
>
> ADE stöds också för RHEL Bring-Your-Own-Subscription Gold Images, men först **efter** att prenumerationen har registrerats . Mer information finns i [Red Hat Enterprise Linux Bring-Your-Own-Subscription Gold Images i Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Ytterligare vm-krav

Azure Disk Encryption kräver att dm-crypt- och vfat-modulerna finns på systemet. Ta bort eller inaktivera vfat från standardavbildningen kommer att hindra systemet från att läsa nyckelvolymen och få nyckeln som behövs för att låsa upp diskarna på efterföljande omstarter. Systemhärdningssteg som tar bort vfat-modulen från systemet är inte kompatibla med Azure Disk Encryption. 

Innan kryptering aktiveras måste de datadiskar som ska krypteras anges korrekt i /etc/fstab. Använd ett beständigt blockenhetsnamn för den här posten, eftersom enhetsnamn i formatet "/dev/sdX" inte kan åberopas för att associeras med samma disk över omstarter, särskilt efter att kryptering har tillämpats. Mer information om detta finns i: [Felsöka ändringar av enhetsnamn för Linux VM](troubleshoot-device-names-problems.md)

Se till att /etc/fstab-inställningarna är korrekt konfigurerade för montering. Om du vill konfigurera dessa inställningar kör du kommandot Mount -a eller starta om den virtuella datorn och utlöser återmonteringen på det sättet. När det är klart kontrollerar du utdata från kommandot lsblk för att kontrollera att enheten fortfarande är monterad. 
- Om filen /etc/fstab inte monterar enheten korrekt innan kryptering aktiveras kan Azure Disk Encryption inte montera den på rätt sätt.
- Azure Disk Encryption-processen flyttar monteringsinformationen från /etc/fstab och in i sin egen konfigurationsfil som en del av krypteringsprocessen. Bli inte rädd för att se posten saknas från / etc / fstab efter dataenhet kryptering slutförs.
- Innan du påbörjar krypteringen ska du se till att stoppa alla tjänster och processer som kan skrivas till monterade datadiskar och inaktivera dem, så att de inte startas om automatiskt efter en omstart. Dessa kan hålla filer öppna på dessa partitioner, förhindra krypteringsproceduren för att återmontera dem, vilket orsakar fel på krypteringen. 
- Efter omstart tar det tid för Azure Disk Encryption-processen att montera de nyligen krypterade diskarna. De kommer inte att vara omedelbart tillgängliga efter en omstart. Processen behöver tid för att starta, låsa upp och sedan montera de krypterade enheterna innan de är tillgängliga för andra processer att komma åt. Den här processen kan ta mer än en minut efter omstart beroende på systemets egenskaper.

Ett exempel på kommandon som kan användas för att montera datadiskarna och skapa nödvändiga /etc/fstab-poster finns i [Azure Disk Encryption förutsättningar CLI-skriptet](https://github.com/ejarvi/ade-cli-getting-started) (raderna 244-248) och Azure Disk Encryption förutsättningar [PowerShell-skriptet](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Krav på nätverk

För att aktivera Azure Disk Encryption-funktionen måste Linux virtuella datorer uppfylla följande konfigurationskrav för nätverksslutpunkt:
  - Om du vill hämta en token för att ansluta till nyckelvalvet måste Linux-virtuella datorn kunna ansluta till en Azure Active Directory-slutpunkt login.microsoftonline.com \[\].
  - Om du vill skriva krypteringsnycklarna till ditt nyckelvalv måste Linux-virtuella datorn kunna ansluta till nyckelvalvets slutpunkt.
  - Linux-datorn måste kunna ansluta till en Azure-lagringsslutpunkt som är värd för Azure-tilläggsdatabasen och ett Azure-lagringskonto som är värd för VHD-filerna.
  -  Om din säkerhetsprincip begränsar åtkomsten från virtuella Azure-datorer till Internet kan du lösa föregående URI och konfigurera en specifik regel för att tillåta utgående anslutning till IPs. Mer information finns i [Azure Key Vault bakom en brandvägg](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Lagringskrav för krypteringsnyckel  

Azure Disk Encryption kräver ett Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter. Ditt nyckelvalv och virtuella datorer måste finnas i samma Azure-region och prenumeration.

Mer information finns i [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologi
I följande tabell definieras några av de vanliga termer som används i Azure-diskkrypteringsdokumentation:

| Terminologi | Definition |
| --- | --- |
| Azure Key Vault | Key Vault är en kryptografisk, nyckelhanteringstjänst som baseras på FIPS-validerade maskinvarusäkerhetsmoduler (Federal Information Processing Standards). Dessa standarder hjälper till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i dokumentationen till [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) och skapa och konfigurera ett [nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerad för att hantera och administrera Azure-resurser från kommandoraden.|
| DM-Kryptan |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) är det Linux-baserade, transparenta diskkrypteringsundersystemet som används för att aktivera diskkryptering på virtuella Linux-datorer. |
| Nyckelkrypteringsnyckel (KEK) | Den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller slå in hemligheten. Du kan tillhandahålla en HSM-skyddad nyckel (Hardware Security Module) eller en programvaruskyddad nyckel. Mer information finns i dokumentationen till [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) och skapa och konfigurera ett [nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md). |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdlets](/powershell/azure/overview). |


## <a name="next-steps"></a>Nästa steg

- [Snabbstart - Skapa och kryptera en Virtuell Linux-dator med Azure CLI](disk-encryption-cli-quickstart.md)
- [Snabbstart – Skapa och kryptera en Virtuell Linux-dator med Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-scenarier på virtuella Linux-datorer](disk-encryption-linux.md)
- [Azure Disk Kryptering förutsättningar CLI-skript](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Kryptering förutsättningar PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Skapa och konfigurera ett nyckelvalv för Azure Disk Encryption](disk-encryption-key-vault.md)


