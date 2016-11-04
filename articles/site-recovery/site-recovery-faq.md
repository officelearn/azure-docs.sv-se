---
title: 'Site Recovery: Vanliga frågor och svar | Microsoft Docs'
description: Den här artikeln innehåller vanliga frågor och svar om Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2016
ms.author: raynew

---
# Azure Site Recovery: Vanliga frågor och svar (FAQ)
Den här artikeln innehåller vanliga frågor och svar om tjänsten Azure Site Recovery.

Om du fortfarande har frågor efter att ha läst artikeln kan du skriva dem längst ned i artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## Allmänt
### Vad gör Site Recovery?
Site Recovery bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att samordna och automatisera replikeringen från lokala virtuella datorer och fysiska servrar till Azure, eller till ett sekundärt datacenter. [Läs mer](site-recovery-overview.md).

### Vad kan Site Recovery replikera?
* **Virtuella Hyper-V-datorer**: Site Recovery kan replikera alla arbetsbelastningar som körs på en virtuell Hyper-V-dator som stöds. 
* **Fysiska servrar**: Site Recovery kan replikera alla arbetsbelastningar som körs på en Windows-/Linux-baserad fysisk server.
* **Virtuella VMware-datorer**: Site Recovery kan replikera alla arbetsbelastningar som körs på en virtuell VMware-dator som stöds.

### Vad behöver jag i Hyper-V?
Vad du behöver för Hyper-V-värdservern beror på distributionsscenariot. Du hittar kraven för Hyper-V i:

* [Replikera virtuella Hyper-V-datorer (utan VMM) till Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
* [Replikera virtuella Hyper-V-datorer (med VMM) till Azure](site-recovery-vmm-to-azure.md#before-you-start)
* [Replikera virtuella Hyper-V-datorer till ett sekundärt datacenter](site-recovery-vmm-to-vmm.md#before-you-start)

För gästerna som körs på Hyper-V-värdservern:

* Om du replikerar till en sekundär plats läser du om [gästoperativsystem som stöds för virtuella Hyper-V-datorer](https://technet.microsoft.com/library/mt126277.aspx).
* Om du replikerar till Azure stöder Site Recovery samma gästoperativsystem som [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### Kan jag skydda virtuella datorer om Hyper-V körs i ett klientoperativsystem?
Nej, virtuella datorer måste finnas på en Hyper-V-värdserver som körs på en Windows-serverdator som stöds. Om du behöver skydda en klientdator kan du replikera den som en fysisk dator [till Azure](site-recovery-vmware-to-azure-classic.md), eller till ett [sekundärt datacenter](site-recovery-vmware-to-vmware.md).

### Vilka arbetsbelastningar kan jag skydda med Site Recovery?
Site Recovery kan replikera alla arbetsbelastningar som körs på en virtuell dator eller fysisk server som stöds. Site Recovery har stöd för programmedveten replikering så att appar kan återställas till en intelligent tillstånd. Tjänsten integrerar med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory och kan användas tillsammans med ledande varumärken som Oracle, SAP, IBM och Red Hat. [Lär dig mer](site-recovery-workload.md) om arbetsbelastningsskydd.

### Måste Hyper-V-värdarna finnas i System Center VMM-moln för att kunna replikerar med Site Recovery?
Om du vill replikera till ett sekundärt datacenter måste virtuella Hyper-V-datorer finnas på Hyper-V-värdservrar i ett VMM-moln. Om du vill replikera till Azure kan du replikera virtuella datorer på Hyper-V-värdservrar med eller utan VMM-moln. [Läs mer](site-recovery-hyper-v-site-to-azure.md). 

### Kan jag distribuera Site Recovery med VMM om jag bara har en VMM-server?
Ja. Du kan antingen replikera virtuella datorer på Hyper-V-servrar i VMM-molnet till Azure eller replikera mellan VMM-moln på samma server. För replikering lokalt till lokalt rekommenderar vi att du har en VMM-server både på de primära och sekundära platserna. [Läs mer](site-recovery-single-vmm.md)

### Vilka fysiska servrar kan jag skydda?
Du kan skydda fysiska servrar som kör Windows- och Linux-operativsystem som stöds till Azure eller till en sekundär plats. [Läs mer](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) om operativsystemkraven för Azure eller replikering till sekundära platser. Observera att fysiska servrar som replikeras till Azure-lagring körs som virtuella datorer i Azure när en redundansväxling sker. När du växlar tillbaka från Azure till den lokala platsen går det för närvarande inte att växla tillbaka till en fysisk server. Du kan bara växla tillbaka till en virtuell dator som körs i VMware.

### Vilka virtuella VMware-datorer kan jag skydda?
För att skydda virtuella VMware-datorer behöver du en vSphere-hypervisor och virtuella datorer som kör VMware-verktyg. Vi rekommenderar också att du har en VMware vCenter-server för att hantera hypervisorer. [Lär dig mer](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) om de exakta kraven för att replikera VMware-servrar och virtuella datorer till Azure eller till en sekundär plats.

### Kan jag hantera haveriberedskap för mina avdelningskontor med Site Recovery?
Ja. När du använder Site Recovery för att samordna replikering och redundans på avdelningskontor kan du styra och visa alla arbetsbelastningar på kontoren på en central plats. Du kan enkelt köra redundansväxlingar och administrera haveriberedskap för alla avdelningskontor från huvudkontoret, utan att besöka avdelningskontoren. 

## Säkerhet
### Skickas replikeringsdata till Site Recovery-tjänsten?
Nej. Site Recovery fångar inte upp replikerade data och har ingen information om vad som körs på dina virtuella datorer eller fysiska servrar.

Replikeringsdata utbyts mellan lokala Hyper-V-värdar, VMware-hypervisorer eller fysiska servrar och Azure-lagring eller din sekundära plats.  Site Recovery har inte möjlighet att fånga upp dessa data. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten. 

Site Recovery-tjänsten är certifierad enligt ISO 27001:2005 och genomgår för närvarande HIPAA-, DPA- och FedRAMP JAB-utvärderingar.

### Av efterlevnadsskäl måste även metadata från våra lokala miljöer finnas inom samma geografiska region. Kan Site Recovery göra detta?
Ja. När du skapar ett Site Recovery-valv i en region ser vi till att alla metadata som vi behöver för att aktivera och samordna replikering och redundans finns inom regionens geografisk gränser.

### Krypterar Site Recovery replikering?
För virtuella datorer och fysiska servrar stöds replikering under överföring mellan lokala platser. För virtuella datorer och fysiska servrar som replikerar till Azure stöds både kryptering under överföring och kryptering i vila (i Azure). 

## Replikering
### Finns det några krav för att replikera virtuella datorer till Azure?
Virtuella datorer som du vill replikera till Azure måste uppfylla [kraven för Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Kan jag replikera virtuella Hyper-V-datorer i generation 2 till Azure?
Ja. Site Recovery konverterar virtuella datorer från generation 2 till generation 1 under redundansväxlingen. Vid återställningen konverteras den virtuella datorn tillbaka till generation 2. [Lär dig mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Hur betalar jag för virtuella datorer i Azure om jag replikerar till Azure?
Vid vanlig replikering replikeras data till geo-redundant Azure-lagring och du behöver inte betala några avgifter för IaaS-baserade virtuella datorer i Azure.  När du kör en redundansväxling till Azure skapar Site Recovery automatiskt IaaS-baserade virtuella Azure-datorer. Därefter debiteras du för de beräkningsresurser som du använder i Azure.

### Finns det något SDK som jag kan använda för att automatisera Site Recovery arbetsflöden?
Ja. Du kan automatisera Site Recovery-arbetsflöden med hjälp av REST-API:et, PowerShell och Azure SDK. Lär dig mer om hur du [distribuerar Site Recovery med hjälp av PowerShell och Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).

### Vilken typ av lagringskonto behöver jag om jag replikerar till Azure?
Du behöver ett lagringskonto med [geo-redundant standardlagring](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Premium-lagring stöds inte för närvarande.

### Hur ofta kan jag replikera data?
* **Hyper-V:** Virtuella Hyper-V-datorer som kör Windows Server 2012 R2 kan replikeras var 30:e sekund, var femte minut eller varje kvart. Om du har konfigurerat SAN-replikering är replikeringen synkron.
* **VMware och fysiska servrar:** Replikeringsfrekvensen är irrelevant här. Replikeringen är kontinuerlig. 

### Kan jag utöka replikeringen från den befintliga återställningsplatsen till en annan återställningsplats?
Utökad eller länkad replikering stöds inte. [Lämna feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) om den här funktionen.

### Kan jag göra en offlinereplikering första gången jag replikerar till Azure?
Det stöds inte. [Lämna feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) om den här funktionen.

### Kan jag undanta specifika diskar från replikeringen?
Det stöds inte. [Lämna feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) om den här funktionen.

### Kan jag replikera virtuella datorer med dynamiska diskar?
Dynamiska diskar stöds vid replikering av virtuella Hyper-V-datorer. De stöds också vid replikering av virtuella VMware-datorer och fysiska datorer om du använder [förbättrad distribution](site-recovery-vmware-to-azure-classic.md). Observera att operativsystemdisken måste vara en standarddisk.

### Kan jag begränsa bandbredden som tilldelas för Hyper-V-replikeringstrafik?
* Om du replikerar mellan virtuella Hyper-V-datorer till lokala platser kan du använda Windows QoS. Här är ett exempelskript: 
  
        New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
        gpupdate.exe /force
* Om du replikerar virtuella Hyper-V-datorer till Azure kan du konfigurera begränsning med hjälp av följande PowerShell-cmdletexempel:
  
        Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)
* [Lär dig mer](https://support.microsoft.com/en-us/kb/3056159) om hur du begränsar Hyper-V-trafik.
* [Lär dig mer](site-recovery-vmware-to-azure-classic.md#capacity-planning) om hur du begränsar VMware-replikering till Azure.

## Redundans
### Hur kommer jag åt virtuella Azure-datorer efter en redundansväxling till Azure?
Du kan komma åt virtuella Azure-datorer via en säker Internetanslutning, via ett plats-till-plats-VPN eller över Azure ExpressRoute. Kommunikationen via en VPN-anslutning sker till interna portar i Azure-nätverk där den virtuella datorn finns. Kommunikationen via Internet mappas till de offentliga slutpunkterna i Azure-molntjänsten för de virtuella datorerna.

### Hur ser Azure till att mina data är flexibla vid en redundansväxling till Azur?
Azure är utformat med flexibilitet i fokus. Site Recovery är redan utformat för redundansväxling till ett sekundärt Azure-datacenter i enlighet med Azure SLA om det behövs. Om detta händer ser vi till att dina metadata och valv finns kvar i samma geografiska region som du valde för ditt valv. 

### Vad händer om det uppstår ett oväntat avbrott på min primära plats när jag replikerar mellan två platser?
Du kan utlösa en oplanerad redundansväxling från den sekundära platsen. Site Recovery behöver ingen anslutning från den primära platsen för att utföra redundansväxlingen.

### Sker redundansväxlingen automatisk?
Den sker inte automatiskt. Du startar redundansväxlingar med ett enkelt klick på portalen eller använder [PowerShell-cmdlets för Site Recovery](https://msdn.microsoft.com/library/dn850420.aspx) för att utlösa dem. Återställningen består av en rad åtgärder på Site Recovery-portalen.

Om du vill automatisera redundans kan du använda lokala Orchestrator eller Operations Manager för att identifiera fel på virtuella datorer och sedan utlösa en redundansväxling med hjälp av SDK.

## 
### Jag är en tjänstprovider. Fungerar Site Recovery för dedikerade eller delade infrastrukturmodeller?
Ja, Site Recovery stöder både dedikerade och delade infrastrukturmodeller.

### Jag är en tjänstprovider. Delas identiteten för min klient med Site Recovery-tjänsten?
Nej. Klienterna behöver inte åtkomst till Site Recovery-portalen. Endast tjänstproviderns administratör interagerar med portalen.

### Hamnar mina klienters programdata någonsin hos Azure?
När du replikerar mellan platser som ägs av tjänstproviders hamnar programdata aldrig hos Azure. Data krypteras under överföring och replikeras direkt mellan tjänstproviderns platser.

Om du replikera till Azure skickas programdata till Azure-lagring men inte till Site Recovery-tjänsten. Data krypteras under överföringen och förblir krypterade i Azure. 

### Får mina klienter en faktura för några Azure-tjänster?
Nej. Azures faktureringsrelation sker direkt med tjänstprovidern. Tjänstprovidern ansvarar för att skapa fakturor för sina klienter.

### Måste jag alltid köra virtuella datorer i Azure om jag replikerar till Azure?
Nej. Data replikeras till ett geo-redundant Azure-lagringskonto i din prenumeration. När du utför ett redundanstest (DR-test) eller en riktig redundansväxling skapar Site Recovery automatiskt virtuella datorer i din prenumeration.

### Garanterar ni isolering på klientnivå när jag replikerar till Azure?
Ja.

### Vilka plattformar stöds för närvarande?
Vi stöder Azure Pack-, Cloud Platform System- och System Center-distributioner (2012 och senare). Mer information om Azure Pack-integrering finns i [Konfigurera skydd för virtuella datorer](https://technet.microsoft.com/library/dn850370.aspx).

### Finns det stöd för distributioner med ett enda Azure Pack och en enda VMM-server?
Ja, du kan replikera virtuella Hyper-V-datorer och Azure, eller mellan en tjänstproviders platser.  Observera att integrering med Azure-runbooks inte är tillgängligt om du replikerar mellan en tjänstproviders platser.

## Nästa steg
* Läs [Site Recovery-översikten](site-recovery-overview.md)
* Lär dig mer om [Site Recovery-arkitekturen](site-recovery-components.md)  

<!--HONumber=Jun16_HO2-->


