---
title: Vanliga frågor - VMware Azure replikering med Azure Site Recovery | Microsoft Docs
description: Den här artikeln sammanfattar vanliga frågor när du replikera lokala virtuella VMware-datorer till Azure med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.openlocfilehash: 345b73db423c6e12b56bb3308f7700917a372dda
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="common-questions---vmware-to-azure-replication"></a>Vanliga frågor - VMware till Azure-replikering

Den här artikeln innehåller svar på vanliga frågor som visas när replikera lokala virtuella VMware-datorer till Azure. Om du har frågor när du har läst den här artikeln kan publicera dem på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Allmänt
### <a name="how-is-site-recovery-priced"></a>Hur prissätts Site Recovery?
Granska [priser för Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/) information.

### <a name="how-do-i-pay-for-azure-vms"></a>Hur betalar jag för virtuella Azure-datorer?
Data replikeras till Azure-lagring vid replikering, och du betalar inte ändringar VM. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer. Därefter använder du debiteras för de beräkningsresurser som du använder i Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Vad kan jag göra med VMware Azure replikering?
- **Katastrofåterställning**: du kan ställa in fullständig katastrofåterställning. I det här scenariot kan replikera du lokala virtuella VMware-datorer till Azure-lagring. Sedan om den lokala infrastrukturen är tillgänglig, kan du växla över till Azure. När du växlar över skapas virtuella Azure-datorer med hjälp av replikerade data. Du kan komma åt appar och arbetsbelastningar på Azure Virtual Machines tills ditt lokala datacenter är tillgänglig igen. Sedan kan du växla tillbaka från Azure till lokala platsen.
- **Migrering**: du kan använda Site Recovery för att migrera lokala virtuella VMware-datorer till Azure. I det här scenariot replikera lokala virtuella VMware-datorer till Azure-lagring. Sedan kan växla du över från lokal till Azure. Efter växling vid fel, din appar och arbetsbelastningar är tillgängliga och körs på virtuella Azure-datorer.



## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>Vad behöver jag i Azure?
Du behöver en Azure-prenumeration, ett Recovery Services-valv, ett lagringskonto och ett virtuellt nätverk. Valvet, storage-konto och nätverk måste vara i samma region.

### <a name="what-azure-storage-account-do-i-need"></a>Vad Azure storage-konto behöver jag?
Du behöver ett LRS eller GRS-lagringskonto. Vi rekommenderar GRS så att dina data är flexibla i händelse av ett regionalt strömavbrott, eller om det inte går att återställa den primära regionen. Premium-lagring stöds.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Min Azure-konto behöver ha behörighet att skapa virtuella datorer?
Om du är en administratör för prenumerationen kan behörighet du replikering du behöver. Om du inte behöver behörighet att skapa en Azure VM i resursgruppen och virtuella nätverk som du anger när du konfigurerar platsen Reocvery och behörighet att skriva till den valda accout. [Läs mer](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Lokal 

### <a name="what-do-i-need-on-premises"></a>Vad gör jag behöver lokalt?
På plats måste du Site Recovery-komponenter som installeras på en enda VMware-VM. Du måste också ha en VMware-infrastruktur, med minst en ESXi-värd och vi rekommenderar en vCenter-server. Dessutom behöver du en eller flera virtuella VMware-datorer för replikering. [Lär dig mer](vmware-azure-architecture.md) om VMware för Azure-arkitektur.

Konfigurationsservern lokalt kan distribueras i ett av följande två sätt

1. Distribuera den med hjälp av en mall som har konfigurationsservern förinstallerat. [Läs mer här](vmware-azure-tutorial.md#download-the-vm-template).
2. Distribuera den med hjälp av installation på en Windows Server 2016-dator som du väljer. [Läs mer här](physical-azure-disaster-recovery.md#set-up-the-source-environment).

För att identifiera den komma igång steg för att distribuera konfigurationsservern på egna Windows Server-datorer i skydd syftet med att aktivera skydd, Välj **till Azure > inte virtualiserade/andra**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Där replikera lokala virtuella datorer till?
Data replikeras till Azure-lagring. När du kör en redundansväxling skapar Site Recovery automatiskt virtuella Azure-datorer från lagringskontot.

### <a name="what-apps-can-i-replicate"></a>Vilka appar kan replikera?
Du kan replikera en app eller arbetsbelastningar som körs på en VMware-VM som uppfyller [replikeringskrav](vmware-physical-azure-support-matrix.md##replicated-machines). Site Recovery har stöd för Programmedveten replikering så att appar kan växlas vid fel och inte kunde tillbaka till en intelligent tillstånd. Site Recovery integrerar med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och användas tillsammans med ledande, inklusive Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Kan jag replikerar till Azure med en plats-till-plats VPN-anslutning
Site Recovery replikerar data från lokala till Azure-lagring via en offentlig slutpunkt eller med hjälp av offentliga ExpressRoute-peering. Replikering via ett plats-till-plats VPN-nätverk stöds inte.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Kan jag replikera till Azure med ExpressRoute?
Ja, ExpressRoute kan användas för att replikera virtuella datorer till Azure. Site Recovery replikerar data till ett Azure Storage-konto via en offentlig slutpunkt, och du behöver att ställa in [offentlig peering](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) för Site Recovery replikering. När virtuella datorer som redundansväxlas till Azure-nätverk, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Varför går inte att replikera via VPN?

När du replikerar till Azure replikeringstrafik når offentliga slutpunkter för ett Azure Storage-konto och därför kan du bara replikera via det offentliga internet med med ExpressRoute (offentlig peering) VPN fungerar inte. 



## <a name="what-are-the-replicated-vm-requirements"></a>Vilka är kraven för replikerade virtuella datorn?

För replikering kör en VMware VM ett operativsystem som stöds. Dessutom kan måste den virtuella datorn uppfylla kraven för virtuella Azure-datorer. [Lär dig mer](vmware-physical-azure-support-matrix.md##replicated-machines) i supportmatrisen.

## <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan replikera till Azure?
Replikeringen är kontinuerlig vid replikering av virtuella VMware-datorer till Azure.

## <a name="can-i-extend-replication"></a>Kan jag utöka replikeringen?
Utökad eller länkad replikering stöds inte. Begär den här funktionen i [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Kan jag göra en inledande offlinereplikering?
Det stöds inte. Begär den här funktionen i den [Feedbackforum](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Kan jag undanta diskar?
Ja, du kan exkludera diskar från replikeringen. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar kan replikeras. Operativsystemdisken måste vara en standarddisk.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>Kan jag lägga till en ny virtuell dator i en befintlig replikeringsgrupp?
Ja.

## <a name="configuration-server"></a>Konfigurationsservern

### <a name="what-does-the-configuration-server-do"></a>Vad är konfigurationsservern?
Konfigurationsservern körs lokalt Site Recovery-komponenter, inklusive: 
- Konfigurationsservern som samordnar kommunikationen mellan lokala och Azure och hanterar datareplikering.
- Processerver som fungerar som en replikerings-gateway. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering; och skickar till Azure storage-., processervern installeras även Mobilitetstjänsten på virtuella datorer du vill replikera och utför automatisk identifiering av lokala virtuella VMware-datorer.
- Huvudmålservern som hanterar replikeringsdata vid återställning från Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>Var konfigurerar jag konfigurationsservern?
Du behöver en enda högtillgänglig lokal VMware VM för konfigurationsservern.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Vilka är kraven för konfigurationsservern?

Granska de [krav](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Kan jag manuellt konfigurera konfigurationsservern istället för att använda en mall?
Vi rekommenderar att du använder den senaste versionen av OVF-mall att [skapa konfigurationsservern VM](vmware-azure-deploy-configuration-server.md). Om du av någon anledning som du kan till exempel du inte har åtkomst till VMware-servern, kan du [hämta Unified installationsfilen](physical-azure-set-up-source.md) från portalen, och köra den på en virtuell dator. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>En konfigurationsservern kan replikera till mer än en region?
Nej. Om du vill göra detta måste du konfigurera en konfigurationsserver i varje region.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Kan jag vara värd för en konfigurationsserver i Azure?
Även om det går måste den virtuella Azure-datorn kör konfigurationsservern kommunicera med dina lokala VMware infrastructure och virtuella datorer. Arbetet är förmodligen inte lönsam.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Var hittar den senaste versionen av mallen configuration server?
Hämta den senaste versionen från den [Microsoft Download Center](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Hur uppdaterar konfigurationsservern?
Du kan installera samlade uppdateringar. Du hittar den senaste uppdateringsinformationen i den [uppdateringar wiki-sida](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Mobilitetstjänsten

### <a name="where-can-i-find-the-mobility-service-installers"></a>Var hittar jag Mobility service installationsprogram
Installationsprogram förvaras i den **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** mapp på konfigurationsservern.

## <a name="how-do-i-install-the-mobility-service"></a>Hur installerar mobilitetstjänsten?
Installation på varje virtuell dator som du vill replikera, med hjälp av en [push-installation](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery), eller en manuell installation från [Användargränssnittet](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui), eller [med hjälp av PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). Du kan också distribuera med ett distributionsverktyg, till exempel [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md), eller [Azure Automation och DSC](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Säkerhet

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Vilken Site Recovery behöver VMware-servrar?
Site Recovery måste ha åtkomst till VMware-servrarna för att:

- Konfigurera en VMware-VM som kör konfigurationsservern och andra lokala Site Recovery-komponenter. [Läs mer](vmware-azure-deploy-configuration-server.md)
- Automatiskt identifiera virtuella datorer för replikering. Lär dig mer om hur du förbereder ett konto för automatisk identifiering. [Läs mer](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Vilken Site Recovery behöver virtuella VMware-datorer?

- För att replikera, måste en VMware VM ha Site Recovery mobilitetstjänsten installeras och körs. Du kan distribuera verktyget manuellt eller ange Site Recovery ska göra en push-installation av tjänsten när du aktiverar replikering för en virtuell dator. Site Recovery måste ett konto som kan användas för att installera tjänstkomponenten som för push-installation. [Läs mer](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Processervern (som körs som standard på konfigurationsservern) utför installationen. [Lär dig mer](vmware-azure-install-mobility-service.md) om Mobility Tjänstinstallationen.
- Replikering kommunicerar virtuella datorer med under Site Recovery på följande sätt:
    - Virtuella datorer kommunicera med konfigurationsservern på port 443 för HTTPS för replikeringshantering av.
    - Virtuella datorer skicka replikeringsdata till processervern på port HTTPS 9443 (kan ändras).
    - Om du aktiverar konsekvens för flera kommunicerar virtuella datorer med varandra via port 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Skickas replikeringsdata till Site Recovery?
Nej, Site Recovery fånga upp inte replikerade data och har inte någon information om vad som körs på din virtuella dator. Replikeringsdata utbyts mellan VMware-hypervisorer och Azure-lagring. Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.  

Site Recovery är 27001: ISO 2013, 27018, HIPAA, DPA certifierad och håller på att SOC2 och FedRAMP JAB-utvärderingar.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Kan vi behålla lokala metadata i en geografiska områden?
Ja. När du skapar ett valv i en region, kontrollera att alla metadata som används av Site Recovery kvarstår inom regionens geografisk gräns.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?
Ja, både kryptering under överföring och [kryptering i Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) stöds.


## <a name="failover-and-failback"></a>Redundans och återställning efter fel
### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?
VMware till Azure är den äldsta återställningspunkten som du kan använda 72 timmar.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Hur kommer jag åt virtuella Azure-datorer efter redundans
Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller över Azure ExpressRoute efter redundans. Du måste du förbereda ett antal åtgärder för att kunna ansluta. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Det gick inte över data flexibel?
Azure är utformat med flexibilitet i fokus. Site Recovery är utformad för redundanskluster till ett sekundärt Azure datacenter i enlighet med Azure-serviceavtalet. När det uppstår redundans, vi Kontrollera att dina metadata och valv finns kvar i samma geografiska region som du valde för ditt valv.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?
[Redundans](site-recovery-failover.md) inträffar inte automatiskt. Du startar redundansväxlingar med ett enda klick på portalen eller använda [ PowerShell](/powershell/module/azurerm.siterecovery) att utlösa en växling vid fel.

### <a name="can-i-fail-back-to-a-different-location"></a>Kan jag växla tillbaka till en annan plats?
Ja, om du växlar över till Azure kan du växla tillbaka till en annan plats om den ursprungliga som inte är tillgänglig. [Läs mer](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Varför måste jag göra en VPN eller ExpressRoute ska återställas?

När du växlar tillbaka från Azure data från Azure kopieras tillbaka till den lokala virtuella datorn och privat åtkomst krävs. 



## <a name="automation-and-scripting"></a>Automation och skript

### <a name="can-i-set-up-replication-with-scripting"></a>Kan jag konfigurera replikering med skript?
Ja. Du kan automatisera Site Recovery arbetsflöden med hjälp av Rest-API: et, PowerShell eller Azure SDK. [Mer](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Prestanda och kapacitet
### <a name="can-i-throttle-replication-bandwidth"></a>Kan jag begränsa bandbredden för replikering?
Ja. [Läs mer](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Nästa steg
* [Granska](vmware-physical-azure-support-matrix.md) krav.
* [Ställ in](vmware-azure-tutorial.md) VMware till Azure-replikering.
