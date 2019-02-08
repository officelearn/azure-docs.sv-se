---
title: Uppdaterar Azure Site Recovery | Microsoft Docs
description: Innehåller en översikt över uppdateringar av tjänsten och hur du uppgraderar komponenter som används i Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/05/2019
ms.author: rajanaki
ms.openlocfilehash: 67fe5da86b4736daa43a85a7a1a077c329732b60
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890475"
---
# <a name="service-updates-in-azure-site-recovery"></a>Uppdateringar av tjänsten i Azure Site Recovery
Som en organisation måste du ta reda på hur du ska skydda data och appar/arbetsbelastningar som körs när planerade och oplanerade avbrott inträffar. Azure Site Recovery bidrar till din BCDR-strategi genom att hålla appar som körs på virtuella datorer och fysiska servrar tillgängliga om en plats kraschar. Site Recovery replikerar arbetsbelastningar som körs på virtuella datorer och fysiska servrar så att de fortfarande är tillgängliga på en sekundär plats om den primära platsen inte är tillgänglig. Den återställer arbetsbelastningar till den primära platsen när den är igång och körs igen.

Site Recovery kan hantera replikering för:

- [Azure VM-replikering mellan Azure-regioner](azure-to-azure-tutorial-dr-drill.md).
- Lokala virtuella datorer och fysiska servrar replikerar till Azure eller till en sekundär plats.
Du behöver veta mer finns i dokumentationen [här](https://docs.microsoft.com/azure/site-recovery) .

Azure Site Recovery publicerar uppdateringar av tjänsten med jämna mellanrum – inklusive tillägg av nya funktioner, förbättringar i stödmatris och felkorrigeringar eventuellt. För att behålla den aktuella dra nytta av alla de senaste funktioner & förbättringar och felkorrigeringar om någon bör användare alltid uppdatera till de senaste versionerna av Azure SIte Recovery-komponenter. 
 
## <a name="support-statement-for-azure-site-recovery"></a>Supportmeddelande för Azure Site Recovery 

> [!IMPORTANT]
> **Med varje ny version ”n” för en Azure Site Recovery-komponent som släpps, alla versioner under ' n – 4' anses support upphör om**. Det är därför alltid lämpligt att uppgradera till de senaste versionerna som är tillgängliga.

> [!IMPORTANT]
> Det officiella stödet för uppgraderingar är från > N-4 till N version (N är den senaste versionen). Om du är på N-6, måste du först uppgradera till N-4 och sedan uppgraderar till N.

### <a name="upgrading-when-the-difference-between-current-version-and-latest-released-version-is-greater-than-4"></a>Uppgradera när skillnaden mellan aktuell version och senaste utgivna versionen är större än 4

1. Uppgradera den installerade komponenten från version say N till N + 4 som ett första steg och flyttar till nästa kompatibel version. Anta att den aktuella versionen är 9,24 och du har en 9.16, först uppgradera till 9.20 och sedan till 9,24.
2. Följ samma steg för alla komponenter beroende på scenario.

### <a name="support-for-latest-oskernel-versions"></a>Stöd för senaste OS/kernel-versioner

> [!NOTE]
> Om du har en schemalagd underhållsperiod och en omstart är en del av samma, rekommenderar vi du först uppgradera Site Recovery-komponenter och fortsätta med resten av schemalagda aktiviteter.

1. Innan du uppgraderar din Kernel/OS-version måste du först kontrollera om målversionen stöds av Azure Site Recovery. Du hittar informationen i vår dokumentation för virtuella Azure-datorer [virtuella VMware-datorer](vmware-physical-azure-support-matrix.md) & Hyper-V-datorer i
2. Se våra [tjänstuppdateringar](https://azure.microsoft.com/updates/?product=site-recovery) för att ta reda på vilken version av Site Recovery-komponenter stöder den specifika versionen som du vill uppgradera till.
3. Uppgradera först till den senaste versionen för webbplatser.
4. Uppgradera nu OS/Kernel till önskad versioner.
5. Utför en omstart.
6. Detta säkerställer att OS/Kernel-version på dina datorer är uppgraderade till den senaste versionen och även de senaste ändringarna i Site Recovery som krävs för att stödja den nya versionen också läses in på källdatorn.



## <a name="azure-vm-disaster-recovery-to-azure"></a>Haveriberedskap för virtuell Azure-dator till Azure
I det här scenariot rekommenderar vi att du [aktivera](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-autoupdate) automatiska uppdateringar. Du kan välja att tillåta Site Recovery för att hantera uppdateringar på följande sätt:

- Aktivera replikering steget
- Visa/Dölj tillägget uppdatera inställningarna i valvet

Om du har valt att hantera uppdateringar manuellt gör du följande:

1. Gå till Azure portal och gå sedan till ”Recovery services-valvet”.
2. Gå till fönstret ”replikerade objekt” i Azure-portalen för ”Recovery services-valvet”.
3. Klicka på följande meddelande längst upp på skärmen:
    
    *Nya agentuppdatering för Site Recovery-replikering är tillgänglig*
    
    *Klicka om du vill installera ->*

4. Välj de virtuella datorerna som du vill tillämpa uppdateringen och klicka sedan på **OK**.

## <a name="between-two-on-premises-vmm-sites"></a>Mellan två lokala VMM-platser
1. Ladda ned den senaste uppdateringen samlad för Microsoft Azure Site Recovery-providern.
2. Installera Samlad uppdatering först på den lokala VMM-servern som hanterar återställningsplatsen.
3. Efter återställningen plats har uppdaterats, installera Samlad uppdatering på VMM-servern som hanterar den primära platsen.

> [!NOTE]
> Om VMM är en mycket tillgänglig VMM (klustrad VMM) kan du kontrollera att du installerar uppgraderingen på alla noder i klustret där VMM-tjänsten är installerad.

## <a name="between-an-on-premises-vmm-site-and-azure"></a>Mellan en lokal VMM-plats och Azure
1. Hämta Samlad uppdatering för Microsoft Azure Site Recovery-providern.
2. Installera uppdateringen på en lokal VMM-servern.
3. Installera den senaste agent MARS-agenten på alla Hyper-V-värdar.

> [!NOTE]
> Om din VMM är en mycket tillgänglig VMM (klustrad VMM) kan du kontrollera att du installerar uppgraderingen på alla noder i klustret där VMM-tjänsten är installerad.

## <a name="between-an-on-premises-hyper-v-site-and-azure"></a>Mellan en lokal Hyper-V-platsen och Azure

1. Hämta Samlad uppdatering för Microsoft Azure Site Recovery-providern.
2. Installera providern på varje nod i Hyper-V-servrar som du har registrerat i Azure Site Recovery.

> [!NOTE]
> Om dina Hyper-V är en värd klustrade Hyper-V-server, se till att du installerar uppgraderingen på alla noder i klustret

## <a name="between-an-on-premises-vmware-or-physical-site-to-azure"></a>Mellan en lokal VMware eller fysiska plats till Azure

Innan du fortsätter med uppdateringar som avser [Site Recovery-supportmeddelande](#support-statement-for-azure-site-recovery) att förstå uppgraderingsvägen.

1. Baserat på din aktuella version och stöd för instruktionen som anges ovan, installera uppdateringen först på den lokala management-servern genom att följa riktlinjerna [här](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). Det här är den server där konfigurationsservern och Process server-roller.
2. Om du har skalbara bearbeta servrar, uppdatera dem sedan genom att följa riktlinjerna [här](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Sedan för att uppdatera mobilitetsagenten på varje skyddat objekt, gå till Azure portal och gå sedan till den **skyddade objekt** > **replikerade objekt** sidan. Välj en virtuell dator på den här sidan. Välj den **Update-agenten** som visas längst ned på sidan för varje virtuell dator. Detta uppdaterar Mobilitetstjänstagenten för alla skyddade virtuella datorerna.

### <a name="reboot-of-source-machine-after-mobility-agent-upgrade"></a>Omstart av källdatorn när mobilitetsagenten har uppgraderat

En omstart rekommenderas efter varje uppgradering av mobilitetsagenten för att se till att alla senaste ändringarna har lästs in på källdatorn. Det är dock **inte obligatoriska**. Om skillnaden mellan agentversion under senaste omstart och aktuella versionen är större än 4, är det obligatoriskt med en omstart. Se följande tabell detaljerad förklaring.

|**Agentversion under senaste omstart** | **Uppgradera till** | **Starta är om obligatorisk?**|
|---------|---------|---------|--------|
|9.16 |  9.18 | Ej obligatoriskt|
|9.16 | 9.19 | Ej obligatoriskt|
| 9.16 | 9.20 | Ej obligatoriskt
 | 9.16 | 9.21 | Ja, först uppgradera till 9.20 och sedan starta om innan du uppgraderar till 9.21 som skillnaden mellan versionerna (9.16 där den senaste omstarten har utförts och målversionen 9.21) är > 4

## <a name="links-to-currently-supported-update-rollups"></a>Länkar till stöds för närvarande samlade uppdateringar

|Samlad uppdatering  |Leverantör  |Enhetlig installation| OVF  |MARS|
|---------|---------|---------|---------|--------|
|[Uppdatera samlad 34](https://support.microsoft.com/en-us/help/4490016/update-rollup-34-for-azure-site-recovery) -snabbkorrigering     |   5.1.3950.0  |  9.22.5142.1   |  5.1.3950.0  | 2.0.9155.0
|[Samlad uppdatering 33](https://support.microsoft.com/en-us/help/4489582/update-rollup-33-for-azure-site-recovery)     |   5.1.3900.0  |  9.22.5109.1   |  5.1.3900.0  | 2.0.9155.0
|[Samlad uppdatering 32](https://support.microsoft.com/en-us/help/4485985/update-rollup-32-for-azure-site-recovery)     |   5.1.3800.0  |  9.21.5091.1   |  5.1.3800.0  |2.0.9144.0
|[Samlad uppdatering 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)     |     5.1.3700.0      |   9.20.5051.1      |     5.1.3700.0    |2.0.9144.0
|[Samlad uppdatering 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30)     |    5.1.3650.0   |   9.19.5007.1    |     5.1.3650.0    |2.0.9139.0
|[Samlad uppdatering 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery)     |   5.1.3650.0      |   9.19.4973.1     |     5.1.3700.0    |2.0.9131.0
|[Samlad uppdatering 28 ](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery)     |  5.1.3600 .0      |    9.19.4973.1     |  5.1.3600.0       |2.0.9131.0
| [Samlad uppdatering 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)       |   5.1.3550.0      |    9.18.4946.1     | 5.1.3550.0         |2.0.9125.0


## <a name="previous-update-rollups"></a>Samlade uppdateringar tidigare
- [Samlad uppdatering 26](https://support.microsoft.com/help/4344054/update-rollup-26-for-azure-site-recovery)  
- [Samlad uppdatering 25](https://support.microsoft.com/help/4278275/update-rollup-25-for-azure-site-recovery) 
- [Samlad uppdatering 23](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 
- [Samlad uppdatering 22](https://support.microsoft.com/help/4072852/update-rollup-22-for-azure-site-recovery) 
- [Samlad uppdatering 21](https://support.microsoft.com/help/4051380/update-rollup-21-for-azure-site-recovery) 
- [Samlad uppdatering 20](https://support.microsoft.com/help/4041105/update-rollup-20-for-azure-site-recovery) 
- [Samlad uppdatering 19](https://support.microsoft.com/help/4034599/update-rollup-19-for-azure-site-recovery) 
