---
title: Vad är Azure Disk Encryption?
description: Den här artikeln innehåller en översikt över Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 07/29/2019
ms.custom: seodec18
ms.openlocfilehash: 1dfcc69d90daae5869c3b69c922e99eab3585e14
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164626"
---
# <a name="azure-disk-encryption-overview"></a>Översikt över Azure Disk Encryption

Azure Disk Encryption skyddar och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Den använder [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -funktionen i Windows och funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volym kryptering för operativ system och data diskar för virtuella datorer i Azure. Det är också integrerat med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter och se till att alla data på de virtuella dator diskarna är krypterade i vila i Azure Storage. Azure Disk Encryption för virtuella Windows-och Linux-datorer är allmänt tillgängliga i alla offentliga Azure-regioner och Azure Government regioner för virtuella standard datorer och virtuella datorer med Azure Premium Storage. 

Om du använder Azure Security Center kan du en varning om du har virtuella datorer som inte är krypterade. Aviseringar visas med hög angelägenhetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Azure Security Center disk encryption-avisering](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Vissa rekommendationerna kan öka data, nätverk, eller beräkning Resursanvändning och resulterar i ytterligare kostnader för licens eller prenumeration.


## <a name="encryption-scenarios"></a>Krypteringsscenarier

Med Azure Disk Encryption kan du lösa organisationens krav på säkerhet och efterlevnad genom att skydda dina virtuella Azure-datorer i vila med hjälp av krypterings teknik som är bransch standard. Du kan också konfigurera virtuella datorer att starta under kundstyrda nycklar och principer (BYOK) och granska användningen av dessa nycklar i ditt nyckel valv.

Azure Disk Encryption stöder följande kund scenarier:

* Aktivera och inaktivera kryptering på nya virtuella datorer som skapats från de Azure Gallery-avbildningar som stöds.
* Aktivera och inaktivera kryptering på befintliga virtuella datorer som körs i Azure.
* Aktivera och inaktivera kryptering på nya virtuella Windows-datorer som skapats från förkrypterade virtuella hård diskar och krypterings nycklar.
* Aktivera och inaktivera kryptering i skalnings uppsättningar för virtuella Windows-datorer.
* Aktivera och inaktivera kryptering på data enheter för skalnings uppsättningar för virtuella Linux-datorer.
* Aktivera och inaktivera kryptering av virtuella datorer med hanterade diskar.
* Uppdatera krypterings inställningarna för en befintlig krypterad Premium-och icke-Premium Storage virtuell dator.
* Säkerhetskopiera och återställa krypterade virtuella datorer.
* Ta med din egen kryptering (BYOE) och ta med dina egna nyckel scenarier (BYOK), där kunderna använder sina egna krypterings nycklar och lagrar dem i ett Azure Key Vault.

Den har också stöd för följande scenarier för virtuella datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault.
* [Virtuella datorer på standard nivå](https://azure.microsoft.com/pricing/details/virtual-machines/) som uppfyller [minimi kraven på minne](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Aktivera kryptering på virtuella Windows-och Linux-datorer, hanterade diskar och skalnings uppsättningar för virtuella datorer från de Azure Gallery-avbildningar som stöds.
* Inaktivera kryptering på operativ system och data enheter för virtuella Windows-datorer, skalnings uppsättningar virtuella datorer och hanterade virtuella hård diskar.
* Inaktivera kryptering på data enheter för virtuella Linux-datorer, skalnings uppsättningar virtuella datorer och hanterade virtuella hård diskar.
* Aktivera kryptering på virtuella datorer som kör Windows-klientens operativ system.
* Aktivera kryptering på volymer med monterings Sök vägar.
* Aktivera kryptering på virtuella Linux-datorer som är konfigurerade med disk striping (RAID) med hjälp av mdadm.
* Aktivera kryptering på virtuella Linux-datorer som använder LVM för data diskar.
* Aktivera kryptering på virtuella Linux-operativsystem och data diskar.

   > [!NOTE]
   > OS-kryptering för vissa Linux-distributioner stöds inte. Mer information finns i Azure Disk Encryption operativ [system som stöds: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Aktivera kryptering på virtuella datorer som är konfigurerade med Windows lagrings utrymmen från och med Windows Server 2016. Lagringsdirigering (S2D) stöds inte ännu.
* Säkerhetskopiera och återställa krypterade virtuella datorer för både nyckel krypterings nyckel (KEK) och icke-KEK scenarier.

Azure Disk Encryption fungerar inte för följande scenarier, funktioner och teknik:

* Kryptering av virtuella datorer på Basic-nivå eller virtuella datorer som skapats via den klassiska skapande metoden för virtuella datorer.
* Inaktivera kryptering på en operativ system enhet eller data enhet på en virtuell Linux-dator när operativ system enheten är krypterad.
* Krypterar OS-enheten för skalnings uppsättningar för virtuella Linux-datorer.
* Kryptera virtuella Windows-datorer som kon figurer ATS med programvarubaserade RAID-system.
* Kryptera anpassade avbildningar på virtuella Linux-datorer.
* Integrering med ett lokalt nyckel hanterings system.
* Azure Files (delade filsystem).
* Network File System (NFS).
* Dynamiska volymer.
* Tillfälliga OS-diskar.
* Kryptering av delade/distribuerade fil system som (men inte begränsat till): DFS, GFS, DRDB, CephFS osv.

## <a name="encryption-features"></a>Krypteringsfunktioner

När du aktiverar och distribuerar Azure Disk Encryption för virtuella Azure-datorer kan du konfigurera följande funktioner som ska aktive ras:

* Kryptering av OS-volymen för att skydda start volymen i vila i lagrings utrymmet.
* Kryptering av data volymer för att skydda data volymerna i vila i lagrings utrymmet.
* Inaktivera kryptering på operativ system och data enheter för virtuella Windows-datorer.
* Inaktivera kryptering på data enheterna för virtuella Linux-datorer (endast när operativ system enheten inte är krypterad).
* Skydda krypterings nycklarna och hemligheterna i Azure Key Vault prenumerationen.
* Rapporterar krypterings status för den krypterade virtuella datorn.
* Tar bort konfigurations inställningarna för disk kryptering från den virtuella datorn.
* Säkerhetskopiera och återställa krypterade virtuella datorer med hjälp av tjänsten Azure Backup.

Azure Disk Encryption för virtuella datorer för Windows och Linux innehåller:

* [Disk krypterings tillägget för Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [Disk krypterings tillägget för Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [PowerShell disk](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0)Encryption-cmdletar.
* [Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest)-diskens krypterings-cmdletar.
* [Mallarna för Azure Resource Manager disk kryptering](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Det finns ingen ytterligare avgift för att kryptera Virtuella diskar med Azure Disk Encryption. Standard [prissättning för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) gäller för nyckelvalvet som används för att lagra krypteringsnycklarna. 

## <a name="encryption-workflow"></a>Arbetsflöde för kryptering

Om du vill aktivera diskkryptering för Windows och Linux-datorer, gör du följande:

1. Välj för att aktivera diskkryptering via Azure Disk Encryption Resource Manager-mallen, PowerShell-cmdletar eller Azure CLI och anger önskad krypteringskonfiguration.

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange sedan krypterings konfigurationen för att aktivera kryptering på en ny virtuell dator.
   * För nya virtuella datorer som skapas från Galleri avbildningar som stöds och befintliga virtuella datorer som redan körs i Azure, ange krypterings konfigurationen för att aktivera kryptering på den virtuella datorn.

1. Bevilja åtkomst till Azure-plattformen för att läsa krypterings nyckel materialet (BitLocker-krypterings nycklar för Windows-system och lösen fras för Linux) från ditt nyckel valv för att aktivera kryptering på den virtuella datorn.

1. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.

   ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Arbetsflöde för dekryptering
Du inaktiverar disk kryptering för virtuella datorer genom att utföra följande steg på hög nivå:

1. Välj att inaktivera kryptering (dekryptering) på en virtuell dator som körs i Azure och ange dekrypterings konfigurationen. Du kan inaktivera via Azure Disk Encryption Resource Manager-mallen, PowerShell-cmdletar eller Azure CLI.

   Det här steget inaktiverar kryptering av operativ systemet eller data volymen eller både och på den virtuella Windows-datorn som körs. Som vi nämnde i föregående avsnitt, stöds inaktivering av OS-diskkryptering för Linux inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer så länge som OS-disken inte är krypterad.

1. Azure uppdaterar VM-tjänstemodellen och den virtuella datorn markeras som dekrypterad. Innehållet i den virtuella datorn är inte längre krypterade i vila.

   > [!NOTE]
   > Åtgärden inaktivera kryptering tar inte bort nyckelvalvet och nyckelmaterial för kryptering (BitLocker-krypteringsnycklar för Windows-System) eller en lösenfras för Linux.
   >
   > Inaktivering av OS-diskkryptering för Linux stöds inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer.
   >
   > Inaktivera data-diskkryptering för Linux stöds inte om den OS-enheten är krypterad.


## <a name="encryption-workflow-previous-release"></a>Kryptering arbetsflöde (tidigare version)

Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure Active Directory (Azure AD) application parameter om du vill aktivera VM-diskkryptering. Med den nya versionen kan måste du inte längre ange en Azure AD-autentiseringsuppgifter under steget Aktivera kryptering. Alla nya virtuella datorer måste vara krypterat utan parametrar för Azure AD-program när du använder den nya versionen. Virtuella datorer som redan har krypterats med Azure AD-program parametrar stöds fortfarande och bör fortsätta att underhållas med Azure AD-syntax. Om du vill aktivera diskkryptering för Windows och Linux-datorer (tidigare version), gör du följande:

1. Välj ett scenario för kryptering från de scenarier som beskrivs i den [krypteringssituationer](#encryption-scenarios) avsnittet.

1. Välj för att aktivera diskkryptering via Azure Disk Encryption Resource Manager-mallen, PowerShell-cmdletar eller Azure CLI och anger önskad krypteringskonfiguration.

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange sedan krypterings konfigurationen för att aktivera kryptering på en ny virtuell dator.
   * För nya virtuella datorer som skapas från Marketplace och befintliga virtuella datorer som redan körs i Azure anger du krypterings konfigurationen för att aktivera kryptering på den virtuella datorn.

1. Bevilja åtkomst till Azure-plattformen för att läsa krypterings nyckel materialet (BitLocker-krypterings nycklar för Windows-system och lösen fras för Linux) från ditt nyckel valv för att aktivera kryptering på den virtuella datorn.

1. Ange Azure AD-identitet för programmet för att skriva krypteringsnyckeln material till ditt nyckelvalv. Det här steget möjliggör kryptering på den virtuella datorn för de scenarier som anges i steg 2.

1. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.


## <a name="terminology"></a>Terminologi
I följande tabell definieras några vanliga termer som används i dokumentationen för Azure Disk Encryption:

| Terminologi | Definition |
| --- | --- |
| Azure AD | En [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) används för att autentisera, lagra och hämta hemligheter från key vault. |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som har baserat på FIPS Federal Information Processing Standards () validerade och maskinvarubaserade säkerhetsmoduler. Dessa standarder bidra till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en bransch känd Windows Volume Encryption-teknik som används för att aktivera disk kryptering på virtuella Windows-datorer. |
| BEK | BitLocker-krypteringsnycklar (BEK) används för att kryptera startvolymen för Operativsystemet och datavolymer. BEKs skyddas som hemligheter i key vault. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerad för att hantera och administrera Azure-resurser från kommandoraden.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) är det Linux-baserade, transparenta disk krypterings systemet som används för att aktivera disk kryptering på virtuella Linux-datorer. |
| Nyckel krypterings nyckel (KEK) | Den asymmetriska nyckeln (RSA 2048) som du kan använda för att skydda eller figursätta hemligheten. Du kan ange en maskinvarusäkerhetsmodul (HSM)-skyddade nyckel eller programvaruskyddad nyckel. Mer information finns i den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdlets](/powershell/azure/overview). |

## <a name="next-steps"></a>Nästa steg

Se [Azure Disk Encryption förutsättningar](azure-security-disk-encryption-prerequisites.md)för att komma igång.

