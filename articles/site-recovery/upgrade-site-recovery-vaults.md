---
title: Uppgradera ett Site Recovery-valv till ett Azure Recovery Services-valv
description: "Lär dig hur du uppgraderar ett Azure Site Recovery-valv till Recovery Services-valvet"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Uppgradera ett Site Recovery-valv till en Azure Resource Manager-baserade Recovery Services-valvet

Den här artikeln beskriver hur du uppgraderar Azure Site Recovery-valv till Azure Resource Manager-baserade återställningstjänsten valv utan någon inverkan på pågående replikering. Mer information om Azure Resource Manager-funktioner och fördelar finns [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introduktion
Recovery Services-valvet är en Azure Resource Manager-resurs för att hantera säkerhetskopiering och katastrofåterställning recovery internt i molnet. Det är en enhetlig valv som du kan använda i den nya Azure-portalen och den ersätter den klassiska säkerhetskopieringen och Site Recovery-valv.

Recovery Services-valv erbjuder en matris med-funktioner, inklusive:

* Azure Resource Manager-stöd: du kan skydda och växla över dina virtuella datorer och fysiska datorer till en Azure Resource Manager hög.

* Utesluta disk: Om du har temporära filer eller hög omsättning data som du inte vill använda alla bandbredd för, kan du undanta volymer från replikering. Den här funktionen har aktiverats för närvarande i *VMware till Azure* och *Hyper-V till Azure* och har utökats till andra scenarier samt.

* Stöd för premium och lokalt redundant lagring: nu kan du skydda servrar i premium storage-konton som utvecklats att skydda program med högre i/o-åtgärder per sekund (IOPS). Den här funktionen är aktiverat i *VMware till Azure*.

* Effektiv Kom igång-upplevelse: förbättrad Kom igång-upplevelse är utformad för att göra det lättare katastrofåterställning installationen.

* Säkerhetskopiering och Site Recovery-hantering från samma valvet: du kan nu skydda servrar för katastrofåterställning eller utför säkerhetskopiering från samma valvet, som kan försämras avsevärt minska din hantering.

Mer information om uppgraderade upplevelse och funktioner finns i [lagring, säkerhetskopiering och återställning blogg](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Viktigaste funktioner

* Ingen inverkan på pågående replikering: pågående replikeringar fortsätta utan avbrott under och efter uppgraderingen.

* Utan extra kostnad: hämta en uppsättning uppdaterade funktioner utan extra kostnad.

* Ingen dataförlust: eftersom den här processen är inte en migrering och uppgradering, inställningar och befintliga återställningspunkter för replikeringen bevaras under och efter uppgraderingen.


## <a name="what-happens-during-the-vault-upgrade"></a>Vad händer under uppgraderingen valvet?

Du kan inte utföra åtgärder som registrerar en ny server eller att aktivera replikering för en virtuell dator (VM) under uppgraderingen. Åtgärder som innefattar läsning av data från eller skriva data till valvet, till exempel pågående replikering av skyddade objekt till valvet, fortsätta utan avbrott.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>Ändringar av automation och verktygsuppsättning efter uppgraderingen
När du uppgraderar typen valvet från den klassiska distributionsmodellen till Resource Manager-distributionsmodellen, Uppdatera befintlig automation eller verktygsuppsättning så att den fortsätter att fungera efter uppgraderingen.

### <a name="prepare-your-environment-for-the-upgrade"></a>Förbereda miljön för uppgraderingen

* [Installera PowerShell eller uppgradera till version 5 eller senare](https://www.microsoft.com/download/details.aspx?id=50395)
* [Installera den senaste versionen av Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases)
* [Hämta uppgraderingsskriptet Recovery Services-valvet](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>Krav
Om du vill uppgradera från Site Recovery-valv till Azure Resource Manager-baserade återställningstjänsten valv, måste du uppfylla följande krav:

* Minsta agent-version: versionen av Azure Site Recovery-Provider installerad på servern måste vara 5.1.1700.0 eller senare.

* Konfigurationer som stöds: du kan inte konfigurera ditt valv med lagringsnätverk (SAN) eller SQL Server AlwaysOn-Tillgänglighetsgrupper. Alla andra konfigurationer som stöds.

    >[!NOTE]
    >Du kan hantera lagring mappning endast via PowerShell efter uppgraderingen.

* Scenario för distribution som stöds: ditt valv får inte vara den *VMware till Azure* äldre distributionsmodell. Innan du fortsätter först flytta förbättrad distributionsmodell.

* Inga aktiva användarinitierad jobb som rör hantering plan operations: eftersom åtkomst till management-plan är begränsad under uppgraderingen, Slutför alla din plan hanteringsåtgärder innan du utlöser uppgraderingen. Den här processen innehåller pågående replikering.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Påverkar den här uppgraderingen min pågående replikering?**

Nej. Din pågående replikering fortsätter utan avbrott under och efter uppgraderingen.

**Vad händer med nätverksinställningar som plats-till-plats VPN och IP-inställningar?**

Uppgraderingen påverkar inte nätverksinställningarna. Alla Azure till lokala anslutningar påverkas inte.

**Vad händer med min valv om jag inte tänker uppgradera i den nära framtiden?**

Stöd för Site Recovery-valvet i gamla Azure-portalen att bli inaktuell från September 2017. Vi rekommenderar starkt att du använder funktionen uppgraderingen för att flytta till den nya portalen.

**Vad innebär det här migreringsplan för Mina befintliga verktygsuppsättning?**  

Uppdatera din verktygsuppsättning till Resource Manager-distributionsmodellen är en av de viktigaste ändringarna som du måste ta hänsyn till i din uppgradering planer. Recovery Services-valv baseras på Resource Manager-distributionsmodellen. 

**Hur lång tid tar management-plan avbrottstiden senaste?**

Uppgraderingen tar normalt ungefär 15 till 30 minuter och det kan ta upp till högst en timme.

**Kan jag återställa efter uppgraderingen?**

Nej. Återställning stöds inte när resurserna har uppgraderats.

**Kan jag verifiera min prenumeration eller resurser för att se om de kan uppgraderas?**

Ja. Det första steget i uppgraderingen är att verifiera att resurserna är utföra en uppgradering i uppgraderingsalternativet plattform som stöds. Om verifieringen misslyckas, får du felmeddelanden eller varningar.

**Hur rapporterar ett problem med uppgraderingen?**

Observera åtgärds-ID som anges i fel om det uppstår fel under uppgraderingen. Microsoft-supporten fungerar proaktivt för att lösa problemet. Du kan också Kontakta supportteamet med ditt prenumerations-ID och valvnamnet åtgärds-ID. Stöd för fungerar för att lösa problemet så snabbt som möjligt. Försök inte igen om du inte uttryckligen uppmanas att göra det från Microsoft.

## <a name="run-the-script"></a>Kör skriptet

I PowerShell kör du följande kommando:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* Prenumerations-ID: Prenumerations-ID som är kopplad till valvet som du uppgraderar.

* VaultName: Namnet på valvet som du uppgraderar.

* Plats: Platsen för valvet som du uppgraderar.

* ResourceType: HyperVRecoveryManagerVault för Site Recovery-valv.

* TargetResourceGroupName: Resursgruppen som du vill att placera uppgraderade valvet. TargetResourceGroupName kan vara en befintlig resursgrupp i Azure Resource Manager eller en ny. Om TargetResourceGroupName som har angetts inte finns, skapas den som en del av uppgraderingen på samma plats som valvet. Mer information finns i avsnittet ”resursgrupper” i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

    >[!NOTE]
    >Resurs grupp naming är vissa begränsningar. Om du vill förhindra valvet uppgraderingen skulle misslyckas, bör du se namngivningskonvention noggrant.
    >
    >Exempel:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 SubscriptionId - 1234-54123-354354-56416-8645 - VaultName gen2dr-platsen ”Norra Europa” - ResourceType hypervrecoverymanagervault - TargetResourceGroupName abc

Du kan också köra följande skript. Ange värden för de obligatoriska parametrarna.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. PowerShell-skriptet uppmanas du att ange dina autentiseringsuppgifter. Ange dessa två gånger, en gång för kontot klassisk distribution modell och en gång för Azure Resource Manager-kontot.

2. När du har angett dina autentiseringsuppgifter, körs skriptet en kontroll för att fastställa om din infrastrukturinställningar uppfyller krav som anges ovan.

3. Efter att kraven har kontrollerats och bekräftat, uppmanas du att fortsätta med uppgraderingen valvet. Uppgraderingen kan du börja uppgradera ditt valv. Hela uppgraderingen kan ta 15 till 30 minuter att slutföra.

4. När uppgraderingen har slutförts, kan du komma åt uppgraderade valvet i den nya Azure-portalen.

## <a name="post-upgrade-vault-management"></a>Efter uppgraderingen valvet management

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Replikera med hjälp av Azure Site Recovery i Recovery Services-valvet

* Nu kan du skydda dina virtuella Azure-datorer från en region till en annan. Mer information finns i [replikera virtuella Azure-datorer mellan regioner med Azure Site Recovery](site-recovery-azure-to-azure.md).

* Mer information om att replikera virtuella VMware-datorer till Azure finns [replikera virtuella VMware-datorer till Azure med Site Recovery](vmware-walkthrough-overview.md).

* Mer information om att replikera virtuella Hyper-V-datorer (utan VMM) till Azure finns [replikera Hyper-V virtuella datorer (utan VMM) till Azure](hyper-v-site-walkthrough-overview.md).

* Mer information om att replikera virtuella Hyper-V-datorer (med VMM) till Azure finns [replikera Hyper-V virtuella datorer i VMM-moln till Azure med Site Recovery på Azure-portalen](vmm-to-azure-walkthrough-overview.md).

* Mer information om replikering av Hyper-virtuella datorer (med VMM) till en sekundär plats finns [replikera Hyper-V virtuella datorer i VMM-moln till en sekundär VMM-plats med hjälp av Azure portal](site-recovery-vmm-to-vmm.md).

* Mer information om att replikera virtuella VMware-datorer till en sekundär plats finns [replikera lokala virtuella VMware-datorer eller fysiska servrar till en sekundär plats i den klassiska Azure-portalen](site-recovery-vmware-to-vmware.md).

### <a name="view-your-replicated-items"></a>Visa dina replikerade objekt

Följande bild visar sidan Recovery Services-valvet instrumentpanelen som visar viktiga entiteter för valvet. Om du vill visa en lista över skyddade enheter i valvet, Välj **Site Recovery** > **replikerade objekt**.


![Replikerade objekt](./media/upgrade-site-recovery-vaults/replicateditems.png)

Följande bild visar arbetsflödet för att visa dina replikerade objekt och **redundans** kommandot för att initiera en växling vid fel.

![Replikerade objekt](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>Visa replikeringsinställningarna för

Varje skyddsgrupp har konfigurerats med kopieringsfrekvensen, kvarhållningstid för återställningspunkten, frekvensen för programkonsekventa ögonblicksbilder och andra replikeringsinställningarna i Site Recovery-valvet. De här inställningarna är konfigurerade som en replikeringsprincip i Recovery Services-valvet. Namnet på principen är namnet på skyddsgruppen eller *primarycloud_Policy*.

Mer information om replikeringsprinciper finns [hantera replikeringsprinciper för VMware till Azure](site-recovery-setup-replication-settings-vmware.md).
