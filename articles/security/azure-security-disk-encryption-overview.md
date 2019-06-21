---
title: Vad är Azure Disk Encryption?
description: Den här artikeln innehåller en översikt över Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 58c5c8321e505fe2c1c7d19c58fe0d031b75b3e4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294870"
---
# <a name="azure-disk-encryption-overview"></a>Översikt över Azure Disk Encryption

Azure Disk Encryption kan du skydda och skydda dina data så att du uppfyller din organisations säkerhet och efterlevnad. Den använder den [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer i Operativsystemet och datadiskarna Azure-datorer (VM). Det är också integrerat med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklarna och hemligheterna, och ser till att alla data på VM-diskarna krypteras i vila i Azure storage. Azure Disk Encryption för Windows och Linux-datorer är allmänt tillgängliga i alla offentliga Azure-regioner och Azure Government-regioner för Standard virtuella datorer och virtuella datorer med Azure Premium Storage. 

Om du använder Azure Security Center kan du en varning om du har virtuella datorer som inte är krypterade. Aviseringar visas med hög angelägenhetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Azure Security Center disk encryption-avisering](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Vissa rekommendationerna kan öka data, nätverk, eller beräkning Resursanvändning och resulterar i ytterligare kostnader för licens eller prenumeration.


## <a name="encryption-scenarios"></a>Krypteringsscenarier

Med Azure Disk Encryption, kan du lösa organisationers säkerhets- och efterlevnadskrav genom att skydda dina virtuella Azure-datorer i vila med branschstandard krypteringsteknik. Du kan också konfigurera virtuella datorer att starta under kund-kontrollerade nycklar och principer (BYOK) och granska användning av dessa nycklar i ditt nyckelvalv.

Azure Disk Encryption har stöd för följande kundscenarier:

* Aktivera och inaktivera kryptering på den nya virtuella datorer som skapas från stöds avbildningarna i Azure-galleriet.
* Aktivera och inaktivera kryptering på den befintliga virtuella datorer som körs i Azure.
* Aktivera och inaktivera kryptering på den nya Windows virtuella datorer skapas från förkrypterade VHD och krypteringsnycklar.
* Aktivera och inaktivera kryptering på Windows VM-skalningsuppsättning anger.
* Aktivera och inaktivera kryptering på data-enheter för Linux VM-skalningsuppsättningar.
* Aktivera och inaktivera kryptering av virtuella datorer med hanterade diskar.
* Uppdaterar krypteringsinställningarna för av en befintlig krypterade Premium och icke - Premium Storage VM.
* Säkerhetskopiera och återställa krypterade virtuella datorer.
* Ta med din egen kryptering (BYOE) och bring your own key (BYOK)-scenarion där kunderna använda sina egna krypteringsnycklar och lagra dem i ett Azure key vault.

Det stöder också följande scenarier för virtuella datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault.
* [Standard-nivån VMs](https://azure.microsoft.com/pricing/details/virtual-machines/) som uppfyller den [minneskravet](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Aktivera kryptering på Windows och Linux-datorer, hanterad disk och skala Konfigurera virtuella datorer från stöds avbildningarna i Azure-galleriet.
* Inaktiverat kryptering på operativsystem och enheter för virtuella Windows-datorer, skala virtuella datorer och hanterade virtuella datorer med diskar.
* Inaktivera kryptering på enheter för virtuella Linux-datorer, skala virtuella datorer och hanterade virtuella datorer med diskar.
* Aktiverar kryptering på virtuella datorer som kör Windows klient-OS.
* Aktivera kryptering på volymer med monteringssökvägar.
* Aktiverar kryptering på den virtuella Linux-datorer som är konfigurerade med disk-striping (RAID) med hjälp av mdadm.
* Aktiverar kryptering på den virtuella Linux-datorer som använder LVM för datadiskar.
* Aktiverar kryptering på Linux VM OS och datadiskar.

   > [!NOTE]
   > OS-kryptering för vissa Linux-distributioner stöds inte. Mer information finns i den [Azure Disk Encryption operativsystem som stöds: Linux](azure-security-disk-encryption-prerequisites.md#linux).
   
* Aktivera kryptering på virtuella datorer som är konfigurerade med Windows-lagringsutrymmen från och med Windows Server 2016.
* Säkerhetskopiera och återställa krypterade virtuella datorer för både nyckelkrypteringsnyckel (KEK) och icke-KEK-scenarier.

Azure Disk Encryption fungerar inte för följande scenarier, funktioner och -teknik:

* Kryptering av basic-nivån virtuell dator eller virtuella datorer som skapas via den klassiska metoden för skapande av virtuell dator.
* Inaktivera kryptering på en OS-enhet eller en dataenhet på en Linux VM när OS-enhet är krypterad.
* Krypterar operativsystemenheten för Linux VM-skalningsuppsättning anger.
* Kryptering av Windows virtuella datorer som konfigurerats med programvarubaserade RAID-system.
* Kryptering av anpassade avbildningar på virtuella Linux-datorer.
* Integrering med ett lokalt nyckelhanteringssystem.
* Azure Files (delade filsystem).
* Network File System (NFS).
* Dynamiska volymer.

## <a name="encryption-features"></a>Krypteringsfunktioner

När du aktiverar och distribuera Azure Disk Encryption för virtuella Azure-datorer, kan du konfigurera följande funktioner är aktiverat:

* Kryptering av OS-volymen för att skydda startvolymen i vila i lagring.
* Kryptera datavolymer för att skydda datavolymer i vila i lagring.
* Inaktivera kryptering på operativsystem och enheter för Windows-datorer.
* Inaktivera kryptering av data-enheter för virtuella Linux-datorer (endast när Operativsystemets disk inte är krypterad).
* Skydda krypteringsnycklar och hemligheter i Azure Key Vault-prenumeration.
* Rapporterar krypteringsstatus för den krypterade virtuella datorn.
* Tar bort inställningar för diskkonfiguration kryptering från den virtuella datorn.
* Säkerhetskopiera och återställa krypterade virtuella datorer med hjälp av Azure Backup-tjänsten.

Azure Disk Encryption för virtuella datorer för Windows och Linux innehåller:

* [Disk encryption-tillägget för Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [Disk encryption-tillägget för Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [PowerShell-cmdlets kryptering av disk](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Disk encryption-cmdletar för Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Azure Resource Manager-mallar kryptering av disk](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Det finns ingen ytterligare avgift för att kryptera Virtuella diskar med Azure Disk Encryption. Standard [prissättning för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) gäller för nyckelvalvet som används för att lagra krypteringsnycklarna. 

## <a name="encryption-workflow"></a>Arbetsflöde för kryptering

Om du vill aktivera diskkryptering för Windows och Linux-datorer, gör du följande:

1. Välj för att aktivera diskkryptering via Azure Disk Encryption Resource Manager-mallen, PowerShell-cmdletar eller Azure CLI och anger önskad krypteringskonfiguration.

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange kryptering konfigurationen för att aktivera kryptering på en ny virtuell dator.
   * För nya virtuella datorer som skapas från galleriavbildningar som stöds, och befintliga virtuella datorer som redan kör i Azure, anger du kryptering konfigurationen för att aktivera kryptering på den virtuella datorn.

1. Bevilja åtkomst till Azure-plattformen för att läsa nyckelmaterial för kryptering (BitLocker-krypteringsnycklar för Windows-System) och lösenfras för Linux från ditt nyckelvalv och aktivera kryptering på den virtuella datorn.

1. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.

   ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Arbetsflöde för dekryptering
Om du vill inaktivera diskkryptering för virtuella datorer, utför du följande anvisningar:

1. Välja att inaktivera kryptering (dekryptering) på en virtuell dator som körs i Azure och anger konfigurationen för dekryptering. Du kan inaktivera via Azure Disk Encryption Resource Manager-mallen, PowerShell-cmdletar eller Azure CLI.

   Det här steget inaktiverar kryptering av Operativsystemet eller datavolymen eller både på virtuell dator som körs Windows. Som vi nämnde i föregående avsnitt, stöds inaktivering av OS-diskkryptering för Linux inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer så länge som OS-disken inte är krypterad.

1. Uppdateringar för Azure VM-tjänstmodellen och den virtuella datorn har markerats som dekrypteras. Innehållet i den virtuella datorn är inte längre krypterade i vila.

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

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange kryptering konfigurationen för att aktivera kryptering på en ny virtuell dator.
   * Ange den kryptering för nya virtuella datorer som har skapats från Marketplace och befintliga virtuella datorer som redan kör i Azure för att aktivera kryptering på den virtuella datorn.

1. Bevilja åtkomst till Azure-plattformen för att läsa nyckelmaterial för kryptering (BitLocker-krypteringsnycklar för Windows-System) och lösenfras för Linux från ditt nyckelvalv och aktivera kryptering på den virtuella datorn.

1. Ange Azure AD-identitet för programmet för att skriva krypteringsnyckeln material till ditt nyckelvalv. Det här steget aktiverar kryptering på den virtuella datorn för de scenarier som nämns i steg 2.

1. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.


## <a name="terminology"></a>Terminologi
I följande tabell definieras några av de vanliga termer som används i Azure disk encryption-dokumentationen:

| Terminologi | Definition |
| --- | --- |
| Azure AD | En [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) används för att autentisera, lagra och hämta hemligheter från key vault. |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som har baserat på FIPS Federal Information Processing Standards () validerade och maskinvarubaserade säkerhetsmoduler. Dessa standarder bidra till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en bransch identifieras Windows volym krypteringsteknik som används för att aktivera diskkryptering på virtuella Windows-datorer. |
| BEK | BitLocker-krypteringsnycklar (BEK) används för att kryptera startvolymen för Operativsystemet och datavolymer. BEKs skyddas som hemligheter i key vault. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerad för att hantera och administrera Azure-resurser från kommandoraden.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) är undersystemet för Linux-baserade och transparent diskkryptering som används för att aktivera diskkryptering på virtuella Linux-datorer. |
| Nyckelkrypteringsnyckel (KEK) | En asymmetrisk nyckel (RSA 2048) som du kan använda för att skydda eller packa in hemligheten. Du kan ange en maskinvarusäkerhetsmodul (HSM)-skyddade nyckel eller programvaruskyddad nyckel. Mer information finns i den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdlets](/powershell/azure/overview). |

## <a name="next-steps"></a>Nästa steg

Kom igång genom att se den [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

