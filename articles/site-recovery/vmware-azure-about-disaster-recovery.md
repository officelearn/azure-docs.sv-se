---
title: Om haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery-tjänsten.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 0984f53c70baa50916e125d096a612ad8f061228
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972829"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Om haveriberedskap för virtuella VMware-datorer till Azure

Den här artikeln innehåller en översikt över haveriberedskap för lokala virtuella VMware-datorer till Azure med den [Azure Site Recovery](site-recovery-overview.md) service.

## <a name="what-is-bcdr"></a>Vad är BCDR?

En affärskontinuitet och haveriberedskap (BCDR) strategi hjälper till att hålla verksamheten igång. Under planerade driftstopp och oväntade avbrott BCDR för att hålla data säkra och tillgängliga och garanterar att appar ska fortsätta köras. Förutom plattformsfunktioner BCDR, till exempel regional länkning och lagring med hög tillgänglighet tillhandahåller Azure Recovery Services som en del av din BCDR-lösning. Recovery services omfattar: 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) säkerhetskopierar dina lokala och Virtuella Azure-data. Du kan säkerhetskopiera en fil och mappar, specifika arbetsbelastningar eller en hel virtuell dator. 
- [Azure Site Recovery](site-recovery-overview.md) tillhandahåller flexibilitet och katastrofåterställning för appar och arbetsbelastningar som körs på lokala datorer eller virtuella Azure IaaS-datorer. Site Recovery dirigerar replikering och hanterar redundans till Azure vid avbrott. Den hanterar också återställning från Azure till din primära plats. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hur gör Site Recovery haveriberedskap?

1. När du har förberett Azure och lokala platser, konfigurera och aktivera replikering för dina lokala datorer.
2. Site Recovery dirigerar den inledande replikeringen av datorn, i enlighet med dina inställningar.
3. Efter den första replikeringen replikerar Site Recovery deltaändringar till Azure. 
4. När allt replikeras som förväntat, kan du köra ett programåterställningstest.
    - Detaljerade hjälper till att säkerställa att redundansväxlingen fungerar som förväntat när en verklig behovet uppstår.
    - Detaljerade utför ett redundanstest utan att påverka produktionsmiljön.
5. Om ett avbrott uppstår kan köra du en fullständig redundans till Azure. Du kan redundansväxla en enskild dator eller skapa en återställningsplan som växlar över flera datorer samtidigt.
6. Vid redundans skapas virtuella Azure-datorer från VM-data i Azure Storage. Användarna kan fortsätta att få åtkomst till appar och arbetsbelastningar från Azure-VM
7. När din lokala plats är tillgänglig igen, redundansväxla från Azure.
8. När du inte återställa och arbetar från din primära plats en gång till kan börja du replikera lokala virtuella datorer till Azure igen.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Hur vet jag om min miljö är lämplig för haveriberedskap till Azure?

Site Recovery kan replikera alla arbetsbelastningar som körs på en stöds VMware VM eller fysiska server. Här är de saker som du behöver kontrollera i din miljö:

- Om du replikerar virtuella VMware-datorer kör du rätt versioner av VMware virtualiseringsservrar? [Kontrollera här](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Är de datorer som du vill replikera som kör ett operativsystem som stöds? [Kontrollera här](vmware-physical-azure-support-matrix.md#replicated-machines).
- För Linux disaster recovery datorer kör en stöds fillagring för system/gäst? [Kontrollera här](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- De datorer du vill replikera uppfyller kraven för Azure? [Kontrollera här](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Det finns stöd för din nätverkskonfiguration [Kontrollera här](vmware-physical-azure-support-matrix.md#network).
- Det finns stöd för din konfiguration för lagring [Kontrollera här](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Vad behöver jag att ställa in i Azure innan jag börjar?

I Azure måste du förbereda följande:

1. Kontrollera att ditt Azure-konto har behörighet att skapa virtuella datorer i Azure.
2. Skapa ett lagringskonto för att lagra avbildningar av replikerade datorer.
3. Skapa ett Azure-nätverk som virtuella Azure-datorer ska ansluta till när de skapas från storage efter en redundansväxling.
4. Konfigurera ett Azure Recovery Services-valv för Site Recovery. Valvet finns i Azure-portalen och används för att distribuera, konfigurera, dirigera, övervaka och felsöka distributionen av Site Recovery.

*Behöver du mer hjälp?*

Lär dig hur du ställer in Azure av [verifiera ditt konto](tutorial-prepare-azure.md#verify-account-permissions), skapa en [lagringskonto](tutorial-prepare-azure.md#create-a-storage-account) och [nätverk](tutorial-prepare-azure.md#set-up-an-azure-network), och [ställa in ett valv](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Vad behöver jag att ställa in lokalt innan jag börjar?

Lokal här är vad du behöver göra:

1. Du behöver konfigurera några konton:

    - Om du replikerar virtuella VMware-datorer, krävs ett konto för Site Recovery för att få åtkomst till vCenter-servern eller vSphere ESXi-värdar att automatiskt identifiera virtuella datorer.
    - Ett konto krävs för att installera Site Recovery-tjänsten mobilitetsagenten på varje fysisk eller virtuell dator som du vill replikera.

2. Du måste kontrollera kompatibiliteten för VMware-infrastrukturen om du inte tidigare som.
3. Se till att du kan ansluta till virtuella Azure-datorer efter en redundansväxling. Du ställa in RDP på den lokala Windows-datorer eller SSH på Linux-datorer.

*Behöver du mer hjälp?*
- Förbereda konton för [automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) och för [installation av mobilitetstjänsten](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Kontrollera](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) att VMware-inställningarna är kompatibla.
- [Förbereda](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) så att du ansluter i Azure efter en redundansväxling.
- Om du vill ha mer detaljerad hjälp om hur du konfigurerar IP-adresser för virtuella Azure-datorer efter redundansväxling, [den här artikeln](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Hur konfigurerar jag haveriberedskap?

När du har din Azure och lokala infrastruktur på plats kan konfigurera du haveriberedskap.

1. För att förstå de komponenter som du kommer att behöva distribuera, granska de [VMware till Azure-arkitektur](vmware-azure-architecture.md), och [fysisk till Azure-arkitektur](physical-azure-architecture.md). Det finns ett antal komponenter, så det är viktigt att förstå hur de fungerar ihop.
2. **Källmiljö**: Som ett första steg i distributionen, kan du ställa in din källmiljö för replikering. Du kan ange vad du vill replikera och var du vill att replikera till.
3. **Konfigurationsservern**: Du måste ställa in en konfigurationsserver i din miljö för en lokal källa:
    - Konfigurationsservern är en enda lokal dator. För VMware-haveriberedskap rekommenderar vi att du distribuerar det som en VMware-VM som kan distribueras från en nedladdningsbar OVF-mall.
    - Konfigurationsservern samordnar kommunikationen mellan lokala och Azure
    - Några andra komponenter som körs på configuration server-datorn.
        - Processervern tar emot, optimerar och skickar data för replikering till Azure storage. Den hanterar också automatisk installation av mobilitetstjänsten på datorer som du vill replikera, samt utför automatisk identifiering av virtuella datorer på VMware-servrar.
        - Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.
    - Konfigurera innehåller registrera konfigurationsservern i valvet, ladda ned MySQL-Server och VMware PowerCLI och att ange konton som används för automatisk identifiering och installera Mobilitetstjänsten.
4. **Målmiljö**: Du konfigurerar mål-Azure-miljön genom att ange din Azure-prenumeration, lagring och nätverksinställningar.
5. **Replikeringsprincip**: Du anger du hur replikering ska ske. Inställningarna omfattar hur ofta återställningspunkter skapas och lagras och om appkonsekventa ögonblicksbilder ska skapas.
6. **Aktivera replikering**. Du aktiverar replikering för lokala datorer. Om du har skapat ett konto för att installera mobilitetstjänsten kommer sedan den att installeras när du aktiverar replikering för en dator. 

*Behöver du mer hjälp?*

- En snabb genomgång av de här stegen, du kan prova vår [VMware självstudien](vmware-azure-tutorial.md), och [fysisk server genomgången](physical-azure-disaster-recovery.md).
- [Läs mer](vmware-azure-set-up-source.md) om hur du konfigurerar din källmiljö.
- [Lär dig mer om](vmware-azure-deploy-configuration-server.md) configuration server-krav och ställer in konfigurationsservern med en OVF-mall för VMware-replikering. Om du av någon anledning som du inte kan använda en mall, eller om du replikerar fysiska servrar [Följ dessa instruktioner](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Läs mer](vmware-azure-set-up-target.md) om Målinställningar.
- [Få mer information](vmware-azure-set-up-replication.md) om hur du konfigurerar en replikeringsprincip.
- [Lär dig](vmware-azure-enable-replication.md) så här aktiverar du replikering och [undanta](vmware-azure-exclude-disk.md) diskar från replikering.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Något gick fel, hur felsöker jag?

- Som ett första steg bör försöka [övervakning distributionen](site-recovery-monitor-and-troubleshoot.md) att kontrollera status för replikerade objekt, jobb och infrastruktur problem och identifiera eventuella fel.
- Om det inte går att slutföra den inledande replikeringen eller pågående replikering inte fungerar som förväntat, [läsa den här artikeln](vmware-azure-troubleshoot-replication.md) för vanliga fel och felsökningstips.
- Om du har problem med automatisk installation av mobilitetstjänsten på datorer som du vill replikera kan du läsa vanliga fel i [i den här artikeln](vmware-azure-troubleshoot-push-install.md).
- Om redundans inte fungerar som förväntat, kontrollera vanliga fel i [i den här artikeln](site-recovery-failover-to-azure-troubleshoot.md).
- Om återställning efter fel inte fungerar kan du kontrollera om ditt problem visas i [i den här artikeln](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Nästa steg

Med replikering nu på plats, bör du [kör ett programåterställningstest](tutorial-dr-drill-azure.md) så att redundansväxlingen fungerar som förväntat. 
