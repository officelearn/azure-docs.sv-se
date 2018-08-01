---
title: Azure Disk Encryption för översikt över virtuella datorer av IaaS | Microsoft Docs
description: Den här artikeln innehåller en översikt över Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30324adb-e24c-433a-9214-2d1465cbdf45
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 0e81a48c1215e8590f90c42aee0861e6fda3db8e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391853"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Azure Disk Encryption för virtuella IaaS-datorer 
Microsoft Azure är förbundet till att säkerställa ditt datasekretess, datasuveränitet och gör att du kan styra dina Azure-värdbaserade data via ett utbud av avancerade tekniker för att kryptera, styra och hantera krypteringsnycklar och kontroll och granskning åtkomst av data. Den här kontrollen innehåller Azure-kunder flexibilitet att välja den lösning som bäst uppfyller sina affärsbehov. Den här artikeln ger en introduktion till en tekniklösning, ”Azure Disk Encryption för Windows och Linux IaaS-VM”, för att skydda och skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadsbestämmelser. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Översikt
Azure Disk Encryption (ADE) är en funktion som hjälper dig att kryptera din Windows- och Linux IaaS VM-diskar. ADE utnyttjar branschstandard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer OS och datadiskar. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter. Lösningen innebär också att alla data på diskar i virtuella datorer krypteras i vila i din Azure-lagring.

Azure disk encryption för Windows och Linux IaaS-datorer finns i **allmänt tillgängliga** i alla offentliga Azure-regioner och AzureGov regioner för Standard virtuella datorer och virtuella datorer med premium storage. När du använder Azure Disk Encryption-hanteringslösningen så kan du uppfylla följande affärsbehov:

* Virtuella IaaS-datorer är skyddade i vila med branschstandard krypteringsteknik till organisationens säkerhets- och efterlevnadskrav.
* Start för virtuella IaaS-datorer under kund-kontrollerade nycklar och principer och du kan granska deras användning i ditt nyckelvalv.

> [!NOTE]
> Vissa rekommendationerna kan öka data, nätverk eller Resursanvändning för beräkning, vilket resulterar i ytterligare kostnader för licens eller prenumeration.


## <a name="encryption-scenarios"></a>Krypteringsscenarier
Azure Disk Encryption-lösningen har stöd för följande kundscenarier:

* Aktivera kryptering på den nya Windows virtuella IaaS-datorer skapas från förkrypterade nycklar för VHD- och kryptering 
* Aktivera kryptering på den nya virtuella IaaS-datorer skapas från stöds galleriavbildningar för Azure
* Aktivera kryptering på befintliga virtuella IaaS-datorer som körs i Azure
* Inaktivera kryptering på Windows virtuella IaaS-datorer
* Inaktivera kryptering på enheter för virtuella Linux IaaS-datorer
* Aktivera kryptering av virtuella datorer med hanterade diskar
* Uppdatera krypteringsinställningarna för en befintlig krypterade premium- och icke-premium storage VM
* Säkerhetskopiering och återställning av krypterade virtuella datorer

Lösningen stöder följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault
* Standard-nivån virtuella datorer: [A, D, DS, G, GS, F och så vidare serien virtuella IaaS-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/)
    * [Virtuella Linux-datorer](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) inom de här nivåerna måste uppfylla minsta minnesstorlek som krävs av 7 GB
* Aktivera kryptering på Windows och Linux IaaS-datorer och virtuella datorer med hanterade diskar från stöds avbildningarna i Azure-galleriet
* Inaktivera kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer och virtuella datorer med hanterade diskar
* Inaktivera kryptering på enheter för virtuella Linux IaaS-datorer och virtuella datorer med hanterade diskar
* Aktivera kryptering på den virtuella IaaS-datorer som kör Windows klient-OS
* Aktivera kryptering på volymer med monteringssökvägar
* Aktivera kryptering på Linux-datorer som konfigurerats med disk striping (RAID) med hjälp av mdadm
* Aktivera kryptering på virtuella Linux-datorer med hjälp av LVM för datadiskar
* Aktivera kryptering på Linux VM OS och datadiskar 
* Aktivera kryptering på Windows virtuella datorer som konfigurerats med lagringsutrymmen
* Uppdatera krypteringsinställningarna för en befintlig krypterade premium- och icke-premium storage VM
* Säkerhetskopiering och återställning av krypterade virtuella datorer för både utan KEK och KEK-scenarier (KEK - krypteringsnyckel)
* Alla offentliga Azure och AzureGov regioner som stöds

Lösningen stöder inte följande scenarier, funktioner och -teknik:

* Basic-nivån virtuella IaaS-datorer
* Inaktivering av kryptering på en OS-enhet för virtuella Linux IaaS-datorer
* Inaktivera kryptering på en dataenhet om Operativsystemets disk är krypterad för virtuella Linux Iaas-datorer
* Virtuella IaaS-datorer som skapas med hjälp av klassiska metod för skapande av virtuell dator
* Aktivera kryptering på Linux IaaS-datorer inte finns stöd för anpassade avbildningar för kunden.
* Integrering med din lokala nyckelhanteringstjänst
* Azure Files (delade filsystem), Network File System (NFS), dynamiska volymer och Windows-datorer som är konfigurerade med programvarubaserade RAID-system

## <a name="encryption-features"></a>Krypteringsfunktioner
När du aktiverar och distribuera Azure Disk Encryption för virtuella Azure IaaS-datorer, aktiveras följande funktioner, beroende på konfigurationen som tillhandahålls:

* Kryptering av OS-volymen för att skydda startvolymen i vila i lagring
* Kryptering av datavolymer för att skydda datavolymer i vila i lagring
* Inaktivering av kryptering på operativsystem och enheter för Windows virtuella IaaS-datorer
* Inaktivera kryptering av data enheter för virtuella Linux IaaS-datorer (endast om operativsystemenhet är inte krypterad)
* Skydda krypteringsnycklar och hemligheter i key vault-prenumeration
* Rapportering krypteringsstatus för krypterade IaaS VM
* Borttagning av diskkryptering konfigurationsinställningar från IaaS-dator
* Säkerhetskopiering och återställning av krypterade virtuella datorer med hjälp av Azure Backup-tjänsten

Azure Disk Encryption för IaaS VMS för Windows och Linux-lösningen innehåller:

* Kryptering av disk tillägget för Windows.
* Kryptering av disk tillägg för Linux.
* Diskkryptering PowerShell-cmdletar.
* Diskkryptering cmdlets för Azure-kommandoradsgränssnittet (CLI).
* Disk encryption Azure Resource Manager-mallar.

Azure Disk Encryption-lösningen stöds för virtuella IaaS-datorer som kör Windows eller Linux-operativsystem. Mer information om operativsystem som stöds finns i den [krav](azure-security-disk-encryption-prerequisites.md) artikeln.

> [!NOTE]
> Det finns inte en extra avgift för att kryptera Virtuella diskar med Azure Disk Encryption. Standard [prissättning för Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) gäller för nyckelvalvet som används för att lagra krypteringsnycklarna. 

## <a name="encryption-workflow"></a>Arbetsflöde för kryptering
Om du vill aktivera diskkryptering för Windows och Linux-datorer, gör du följande:

1. Välj ett scenario för kryptering bland de föregående kryptering-scenarierna.
2. Välj att aktivera diskkryptering via Azure Disk Encryption Resource Manager-mall, PowerShell-cmdletar eller CLI-kommando och ange krypteringskonfiguration.

   * Ladda upp den krypterade virtuella Hårddisken till ditt lagringskonto och kryptering nyckelmaterial till ditt nyckelvalv för scenariot kund-krypterade virtuella Hårddisken. Ange kryptering konfigurationen för att aktivera kryptering på en ny IaaS-VM.
   * Tillhandahåller kryptering konfigurationen för att aktivera kryptering på IaaS-VM för nya virtuella datorer som har skapats från Marketplace och befintliga virtuella datorer som redan körs i Azure.

3. Bevilja åtkomst till Azure-plattformen för att läsa krypteringsnyckeln material (BitLocker-krypteringsnycklar för Windows-System) och lösenfras för Linux från ditt nyckelvalv och aktivera kryptering på IaaS-VM.

4. Ange Programidentitet Azure Active Directory (Azure AD) för att skriva krypteringsnyckeln material till ditt nyckelvalv. Detta aktiverar kryptering på IaaS-VM för scenarier som nämns i steg 2.

5. Azure uppdaterar tjänstmodellen virtuell dator med kryptering och konfiguration av key vault och ställer in den krypterade virtuella datorn.

 ![Microsoft Antimalware i Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Arbetsflöde för dekryptering
Om du vill inaktivera disk encryption för virtuella IaaS-datorer, utför du följande anvisningar:

1. Välja att inaktivera kryptering (dekryptering) på en pågående IaaS-VM i Azure och anger önskad konfiguration för dekryptering. Du kan inaktivera via Azure Disk Encryption Resource Manager-mall, PowerShell-cmdletar eller Azure CLI.

 Det här steget inaktiverar kryptering av Operativsystemet datavolym och/eller som kör Windows virtuella IaaS-datorer. Men som nämns i föregående avsnitt, stöds inaktivering av OS-diskkryptering för Linux inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer som OS-disken inte är krypterad.
2. Azure uppdaterar modellen för VM-service och IaaS VM markeras dekrypterade. Innehållet i den virtuella datorn är inte längre krypterade i vila.

> [!NOTE]
> Inaktivera kryptering åtgärden tar inte bort nyckelvalvet och nyckelmaterial för kryptering (BitLocker-krypteringsnycklar för Windows-System) eller en lösenfras för Linux.
 > Inaktivering av OS-diskkryptering för Linux stöds inte. Steg för dekryptering tillåts endast för enheter på virtuella Linux-datorer.
Inaktivera data-diskkryptering för Linux stöds inte om den OS-enheten är krypterad.

## <a name="terminology"></a>Terminologi
För att förstå några av de vanliga termer som används av den här tekniken kan använda tabellen nedan terminologi:

| Terminologi | Definition |
| --- | --- |
| Azure AD | Azure AD är [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). En Azure AD-konto används för autentisering, lagra och hämta hemligheter från key vault. |
| Azure Key Vault | Key Vault är en kryptografisk, key management-tjänst som baseras på FIPS Federal Information Processing Standards () verifierade maskinvarusäkerhetsmoduler som skyddar dina kryptografiska nycklar och känsliga hemligheter. Mer information finns i [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) är en bransch identifieras Windows volym krypteringsteknik som används för att aktivera diskkryptering på Windows virtuella IaaS-datorer. |
| BEK | BitLocker-nycklar för kryptering används för att kryptera startvolymen för Operativsystemet och datavolymer. BitLocker-nycklar skyddas i ett nyckelvalv som hemligheter. |
| CLI | Se [kommandoradsgränssnittet](/cli/azure/install-azure-cli).|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) är undersystemet för Linux-baserade och transparent diskkryptering som används för att aktivera diskkryptering på virtuella Linux IaaS-datorer. |
| KEK | Nyckelkrypteringsnyckel är en asymmetrisk nyckel (RSA 2048) som du kan använda för att skydda eller packa in hemligheten. Du kan ange en maskinvarusäkerhetsmodul (HSM)-skyddade nyckel eller programvaruskyddad nyckel. Mer information finns i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentation. |
| PS-cmdletar | Se [Azure PowerShell-cmdlets](/powershell/azure/overview). |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
