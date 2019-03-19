---
title: Översikt – Azure Disk Encryption för virtuella IaaS-datorer | Microsoft Docs
description: Den här artikeln innehåller en översikt över Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: a7dd061d84167b172aff52e8a62ed6d43fddc108
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862020"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption för virtuella IaaS-datorer

Microsoft Azure är förbundet till att se till att dina datasekretess och datasuveränitet. Med Azure kan du styra dina Azure-baserade data via ett utbud av avancerade tekniker för att kryptera, styra och hantera krypteringsnycklar och kontroll och granskning åtkomst av data. Den här kontrollen innehåller Azure-kunder med flexibiliteten att välja den lösning som bäst uppfyller sina affärsbehov. Den här artikeln ger en introduktion till en tekniklösning: ”Azure Disk Encryption för Windows och Linux IaaS virtuella datorer (VM)”. Den här tekniken kan skydda och skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsbestämmelser. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Översikt

Azure Disk Encryption är en funktion som hjälper dig att kryptera din Windows- och Linux IaaS VM-diskar. Diskkryptering utnyttjar branschstandard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer OS och datadiskar. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter. Lösningen innebär också att alla data på Virtuella datordiskar är krypterade i vila i din Azure-lagring.

Disk Encryption för Windows och Linux IaaS-datorer är allmänt tillgängliga i alla offentliga Azure-regioner och Azure Government-regioner för Standard virtuella datorer och virtuella datorer med Azure Premium Storage. När du använder hanteringslösning för diskkryptering så kan du uppfylla följande affärsbehov:

* Virtuella IaaS-datorer är skyddade i vila med branschstandard krypteringsteknik för att uppfylla organisationens krav för säkerhet och efterlevnad.
* Virtuella IaaS-datorer startar under kund-kontrollerade nycklar och principer. Du kan granska deras användning i ditt nyckelvalv.

Om du använder Azure Security Center kan du en varning om du har virtuella datorer som inte är krypterade. Aviseringar visas med hög angelägenhetsgrad och rekommendationen är att kryptera dessa virtuella datorer.

![Azure Security Center disk encryption-avisering](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Vissa rekommendationerna kan öka data, nätverk, eller beräkning Resursanvändning och resulterar i ytterligare kostnader för licens eller prenumeration.


## <a name="encryption-scenarios"></a>Krypteringsscenarier

Disk Encryption-lösningen har stöd för följande kundscenarier:

* Aktivera kryptering på den nya Windows virtuella IaaS-datorer skapas från förkrypterade nycklar för VHD- och kryptering.
* Aktivera kryptering på den nya virtuella IaaS-datorer skapas från stöds galleriavbildningar för Azure.
* Aktivera kryptering på befintliga virtuella IaaS-datorer som körs i Azure.
* Aktivera kryptering på Windows VM scale sets.
* Aktivera kryptering på enheter för Linux VM-skalningsuppsättningar.
* Inaktivera kryptering på Windows virtuella IaaS-datorer.
* Inaktivera kryptering på enheter för virtuella Linux IaaS-datorer.
* Inaktivera kryptering på Windows VM-skalningsuppsättningar.
* Inaktivera kryptering på enheter för Linux VM-skalningsuppsättningar.
* Aktivera kryptering av virtuella datorer med hanterade diskar.
* Uppdatera krypteringsinställningarna för en befintlig krypterade Premium- och icke - Premium Storage VM.
* Säkerhetskopiera och återställa krypterade virtuella datorer.

Lösningen stöder följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault.
* Standard-nivån virtuella datorer: [A, D, DS, G, GS, F och så vidare, serien virtuella IaaS-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/). [Virtuella Linux-datorer](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) inom de här nivåerna måste uppfylla minsta minnesstorlek som krävs av 7 GB.
* Aktivera kryptering på Windows och Linux IaaS-datorer, hanterade disk och skala Konfigurera virtuella datorer från stöds avbildningarna i Azure-galleriet.
* Inaktivera kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer, skala virtuella datorer och hanterade virtuella datorer med diskar.
* Inaktivera kryptering på enheter för virtuella Linux IaaS-datorer, skala virtuella datorer och hanterade virtuella datorer med diskar.
* Aktivera kryptering på den virtuella IaaS-datorer som kör Windows klient-OS.
* Aktivera kryptering på volymer med monteringssökvägar.
* Aktivera kryptering på den virtuella Linux-datorer som är konfigurerade med disk-striping (RAID) med hjälp av mdadm.
* Aktivera kryptering på den virtuella Linux-datorer som använder LVM för datadiskar.
* Aktivera kryptering på Linux VM OS och datadiskar.

   > [!NOTE]
   > OS-kryptering för vissa Linux-distributioner stöds inte. Mer information finns i den [Azure Disk Encryption vanliga frågor och svar](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artikeln.
   
* Aktivera kryptering på virtuella datorer som är konfigurerade med Windows-lagringsutrymmen från och med Windows Server 2016.
* Uppdatera krypteringsinställningar för en befintlig krypterade Premium- och icke - Premium Storage VM.
* Säkerhetskopiera och återställa krypterade virtuella datorer för både nyckelkrypteringsnyckel (KEK) och icke-KEK-scenarier.
* Alla offentliga Azure och Azure Government-regioner stöds.

Lösningen stöder inte följande scenarier, funktioner och -teknik:

* Basic-nivån virtuella IaaS-datorer.
* Inaktivera kryptering på en operativsystemenhet för virtuella Linux IaaS-datorer.
* Inaktivera kryptering på en dataenhet när OS-enhet är krypterad för virtuella Linux IaaS-datorer.
* Virtuella IaaS-datorer som skapas med hjälp av metod för skapande av klassiska virtuella datorer.
* Aktivera kryptering av kunden anpassade avbildningar på virtuella Linux IaaS-datorer.
* Integrering med din lokala nyckelhanteringssystem.
* Azure Files (delade filsystem).
* Network File System (NFS).
* Dynamiska volymer.
* Windows virtuella datorer som är konfigurerade med programvarubaserade RAID-system.

## <a name="encryption-features"></a>Krypteringsfunktioner

När du aktiverar och distribuera Disk Encryption för virtuella Azure IaaS-datorer, är följande funktioner aktiverade beroende på den angivna konfigurationen:

* Kryptering av OS-volymen för att skydda startvolymen i vila i lagring.
* Kryptering av datavolymer för att skydda datavolymer i vila i lagring.
* Inaktivera kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer.
* Inaktivera kryptering på dataenheterna för virtuella Linux IaaS-datorer (endast när Operativsystemets disk inte är krypterad).
* Skydda krypteringsnycklar och hemligheter i Azure Key Vault-prenumerationen.
* Rapportera krypteringsstatus av krypterade IaaS-VM.
* Ta bort inställningar för diskkonfiguration kryptering från IaaS-VM.
* Säkerhetskopiera och återställa krypterade virtuella datorer med hjälp av Azure Backup-tjänsten.

Azure Disk Encryption för IaaS VMS för Windows och Linux-lösningen innehåller:

* Kryptering av disk tillägget för Windows.
* Kryptering av disk tillägg för Linux.
* PowerShell disk encryption-cmdlets.
* Cmdletar för Azure CLI disk encryption.
* Mallar för resurshanteraren för Azure disk encryption.

Azure Disk Encryption-lösningen stöds för virtuella IaaS-datorer som kör Windows eller Linux-operativsystem. Mer information om operativsystem som stöds finns i den [krav](azure-security-disk-encryption-prerequisites.md) artikeln.

> [!NOTE]
> Det finns ingen ytterligare avgift för att kryptera Virtuella diskar med Azure Disk Encryption. Standard [prissättning för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) gäller för nyckelvalvet som används för att lagra krypteringsnycklarna. 


## <a name="encryption-workflow"></a>Arbetsflöde för kryptering

Om du vill aktivera diskkryptering för Windows och Linux-datorer, gör du följande:

1. Välj ett scenario för kryptering från de scenarier som beskrivs i den [krypteringssituationer](#encryption-scenarios) avsnittet.

1. Välj för att aktivera diskkryptering via Azure Disk Encryption Resource Manager-mallen, PowerShell-cmdletar eller Azure CLI och anger önskad krypteringskonfiguration.

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange kryptering konfigurationen för att aktivera kryptering på en ny IaaS-VM.
   * Tillhandahåller kryptering konfigurationen för att aktivera kryptering på IaaS-VM för nya virtuella datorer som har skapats från Marketplace och befintliga virtuella datorer som redan kör i Azure.

1. Bevilja åtkomst till Azure-plattformen för att läsa nyckelmaterial för kryptering (BitLocker-krypteringsnycklar för Windows-System) och lösenfras för Linux från ditt nyckelvalv och aktivera kryptering på IaaS-VM.

1. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.

   ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Arbetsflöde för dekryptering
Om du vill inaktivera disk encryption för virtuella IaaS-datorer, utför du följande anvisningar:

1. Välja att inaktivera kryptering (dekryptering) på en pågående IaaS-VM i Azure och anger önskad konfiguration för dekryptering. Du kan inaktivera via Azure Disk Encryption Resource Manager-mallen, PowerShell-cmdletar eller Azure CLI.

   Det här steget inaktiverar kryptering av Operativsystemet datavolym och/eller som kör Windows virtuella IaaS-datorer. Som vi nämnde i föregående avsnitt, stöds inaktivering av OS-diskkryptering för Linux inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer så länge som OS-disken inte är krypterad.

1. Uppdateringar för Azure VM-tjänstmodellen och IaaS-VM har markerats som dekrypteras. Innehållet i den virtuella datorn är inte längre krypterade i vila.

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

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange kryptering konfigurationen för att aktivera kryptering på en ny IaaS-VM.
   * Tillhandahåller kryptering konfigurationen för att aktivera kryptering på IaaS-VM för nya virtuella datorer som har skapats från Marketplace och befintliga virtuella datorer som redan kör i Azure.

1. Bevilja åtkomst till Azure-plattformen för att läsa nyckelmaterial för kryptering (BitLocker-krypteringsnycklar för Windows-System) och lösenfras för Linux från ditt nyckelvalv och aktivera kryptering på IaaS-VM.

1. Ange Azure AD-identitet för programmet för att skriva krypteringsnyckeln material till ditt nyckelvalv. Det här steget aktiverar kryptering på IaaS-VM för scenarier som nämns i steg 2.

1. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.


## <a name="terminology"></a>Terminologi
I följande tabell definieras några av de vanliga termer som används i den här tekniken:

| Terminologi | Definition |
| --- | --- |
| Azure AD | En [Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) används för att autentisera, lagra och hämta hemligheter från key vault. |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som har baserat på FIPS Federal Information Processing Standards () validerade och maskinvarubaserade säkerhetsmoduler. Dessa standarder bidra till att skydda dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en bransch identifieras Windows volym krypteringsteknik som används för att aktivera diskkryptering på Windows virtuella IaaS-datorer. |
| BEK | BitLocker-krypteringsnycklar (BEK) används för att kryptera startvolymen för Operativsystemet och datavolymer. BEKs skyddas som hemligheter i key vault. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) är optimerad för att hantera och administrera Azure-resurser från kommandoraden.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) är undersystemet för Linux-baserade och transparent diskkryptering som används för att aktivera diskkryptering på virtuella Linux IaaS-datorer. |
| KEK | Nyckelkrypteringsnyckel (KEK) är en asymmetrisk nyckel (RSA 2048) som du kan använda för att skydda eller packa in hemligheten. Du kan ange en maskinvarusäkerhetsmodul (HSM)-skyddade nyckel eller programvaruskyddad nyckel. Mer information finns i den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| PowerShell-cmdletar | Mer information finns i [Azure PowerShell-cmdlets](/powershell/azure/overview). |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Krav för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
