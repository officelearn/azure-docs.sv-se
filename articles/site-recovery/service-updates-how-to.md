---
title: Uppdateringar och komponentuppgraderingar i Azure Site Recovery
description: Innehåller en översikt över uppdateringar av Azure Site Recovery-tjänsten och komponentuppgraderingar.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257828"
---
# <a name="service-updates-in-site-recovery"></a>Tjänstuppdateringar i Site Recovery

Den här artikeln innehåller en översikt över Uppdateringar av [Azure Site Recovery](site-recovery-overview.md) och beskriver hur du uppgraderar site recovery-komponenter.

Site Recovery publicerar serviceuppdateringar regelbundet. Uppdateringar inkluderar nya funktioner, supportförbättringar, komponentuppdateringar och buggfixar. För att kunna dra nytta av de senaste funktionerna och korrigeringarna rekommenderar vi att du kör de senaste versionerna av Site Recovery-komponenter. 
 
 
## <a name="updates-support"></a>Stöd för uppdateringar

### <a name="support-statement-for-azure-site-recovery"></a>Supportsats för Azure Site Recovery

Vi rekommenderar att du alltid uppgraderar till de senaste komponentversionerna:

**Med varje ny version "N" av en Azure Site Recovery-komponent som släpps anses alla versioner under "N-4" vara utan stöd**. 

> [!IMPORTANT]
> Officiellt stöd är för uppgradering från > N-4 version till N-version. Om du till exempel kör du på N-6 måste du först uppgradera till N-4 och sedan uppgradera till N.


### <a name="links-to-currently-supported-update-rollups"></a>Länkar till sammanslagningar som stöds för närvarande

 Granska den senaste samlade uppdateringen (version N) i [den här artikeln](site-recovery-whats-new.md). Kom ihåg att Site Recovery ger stöd för N-4-versioner.



## <a name="component-expiry"></a>Komponentens utgång

Site Recovery meddelar dig om utgångna komponenter (eller nära att gå ut) via e-post (om du prenumererar på e-postmeddelanden) eller på instrumentpanelen i valvet i portalen.

- När uppdateringar är tillgängliga visas dessutom en **tillgänglig uppdateringsknapp bredvid** komponenten i infrastrukturvyn för ditt scenario i portalen. Den här knappen omdirigerar dig till en länk för att hämta den senaste komponentversionen.
-  Instrumentpanelsaviseringar för valv är inte tillgängliga om du replikerar virtuella hyper-v-datorer. 

E-postmeddelanden skickas enligt följande.

**Tid** | **Frekvens**
--- | ---
60 dagar före komponentens utgång | En gång varannan vecka
Nästa 53 dagar | En gång i veckan
Senaste 7 dagarna | En gång om dagen
Efter utgången | En gång varannan vecka


### <a name="upgrading-outside-official-support"></a>Uppgradering utanför officiellt stöd

Om skillnaden mellan komponentversionen och den senaste versionen är större än fyra, anses detta vara av stöd. I det här fallet uppgraderar du enligt följande: 

1. Uppgradera den installerade komponenten till din nuvarande version plus fyra. Om din version till exempel är 9.16 uppgraderar du till 9.20.
2. Uppgradera sedan till nästa kompatibla version. Så i vårt exempel, efter uppgradering 9,16 till 9,20, uppgradera till 9,24. 

Följ samma process för alla relevanta komponenter.

### <a name="support-for-latest-operating-systemskernels"></a>Stöd för de senaste operativsystemen/kärnorna

> [!NOTE]
> Om du har schemalagt ett underhållsfönster och en omstart ingår i det, rekommenderar vi att du först uppgraderar Site Recovery-komponenter och fortsätter sedan med resten av schemalagda aktiviteter i underhållsfönstret.

1. Innan du uppgraderar operativsystem-/kärnversioner kontrollerar du om målversionen stöds för Site Recovery. 

    - [Azure VM-stöd.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
    - [Stöd för VMware/fysisk server](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Stöd för [Hyper-V.](hyper-v-azure-support-matrix.md#replicated-vms)
2. Granska [tillgängliga uppdateringar](site-recovery-whats-new.md) för att ta reda på vad du vill uppgradera.
3. Uppgradera till den senaste site recovery-versionen.
4. Uppgradera operativsystemet/kärnan till de versioner som krävs.
5. Omstart.


Den här processen säkerställer att datorns operativsystem/kärna uppgraderas till den senaste versionen och att de senaste site recovery-ändringarna som behövs för att stödja den nya versionen läses in på datorn.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Haveriberedskap för virtuell Azure-dator till Azure

I det här fallet rekommenderar vi starkt att du [aktiverar automatiska uppdateringar](azure-to-azure-autoupdate.md). Du kan tillåta att webbplatsåterställning hanterar uppdateringar på följande sätt:

- Under aktivera replikeringsprocessen.
- Genom att ställa in inställningarna för tilläggsuppdatering i valvet.

Om du vill hantera uppdateringar manuellt gör du följande:

1. Klicka på det här meddelandet högst upp på skärmen i **valvets > replikerade objekt:** 
    
    **Uppdatering av ny replikeringsagent för webbplatsåterställning är tillgänglig. Klicka här om du vill installera ->**

4. Markera de virtuella datorer som du vill installera uppdateringen för och klicka sedan på **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/fysisk serverkatastrofåterställning till Azure

1. Baserat på din aktuella version och [supportsatsen](#support-statement-for-azure-site-recovery)installerar du uppdateringen först på den lokala [konfigurationsservern](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)med hjälp av dessa instruktioner . 
2. Om du har utskalningsprocessservrar uppdaterar du dem nästa gång med hjälp av [dessa instruktioner](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Information om hur du uppdaterar mobilitetsagenten på varje skyddad dator finns i [den här](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) artikeln.

### <a name="reboot-after-mobility-service-upgrade"></a>Starta om efter uppgradering av mobilitetstjänsten

En omstart rekommenderas efter varje uppgradering av mobilitetstjänsten för att säkerställa att alla de senaste ändringarna läses in på källmaskinen.

En omstart är inte obligatorisk, såvida inte skillnaden mellan agentversionen under den senaste omstarten och den aktuella versionen är större än fyra.

Exemplet i tabellen visar hur det fungerar.

|**Agentversion (senaste omstart)** | **Uppgradera till** | **Obligatorisk omstart?**|
|---------|---------|---------|
|9.16 |  9.18 | Inte obligatoriskt|
|9.16 | 9.19 | Inte obligatoriskt|
| 9.16 | 9.20 | Inte obligatoriskt
 | 9.16 | 9.21 | Obligatorisk.<br/><br/> Uppgradera till 9,20 och starta sedan om innan du uppgraderar till 9.21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Haveriberedskap för virtuell Hyper-V-dator till Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Mellan en Hyper-V-plats och Azure

1. Hämta uppdateringen för Microsoft Azure Site Recovery Provider.
2. Installera providern på varje Hyper-V-server som är registrerad i Site Recovery. Om du kör ett kluster uppgraderar du på alla klusternoder.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Mellan en lokal VMM-plats och Azure
1. Hämta uppdateringen för Microsoft Azure Site Recovery Provider.
2. Installera providern på VMM-servern. Om VMM distribueras i ett kluster installerar du providern på alla klusternoder.
3. Installera den senaste Microsoft Azure Recovery Services-agenten på alla Hyper-V-värdar eller klusternoder.


## <a name="between-two-on-premises-vmm-sites"></a>Mellan två lokala VMM-platser
1. Hämta den senaste uppdateringen för Microsoft Azure Site Recovery Provider.
2. Installera den senaste providern på VMM-servern som hanterar den sekundära återställningsplatsen. Om VMM distribueras i ett kluster installerar du providern på alla klusternoder.
3. När återställningsplatsen har uppdaterats installerar du providern på VMM-servern som hanterar den primära platsen.

## <a name="next-steps"></a>Nästa steg

Följ vår [sida Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) för att spåra nya uppdateringar och versioner.
