---
title: VMware-haveriberedskap med Azure Site Recovery
description: Den här artikeln innehåller en översikt över haveriberedskap av virtuella datorer med VMware till Azure med hjälp av Azure Site Recovery-tjänsten.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 589dda80d68fba73a729da4b6e59270cc09c18cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954397"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Om haveriberedskap av virtuella virtuella datorer med VMware till Azure

Den här artikeln innehåller en översikt över haveriberedskap för lokala virtuella datorer med VMware till Azure med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)

## <a name="what-is-bcdr"></a>Vad är BCDR?

En strategi för kontinuitet och katastrofåterställning (BCDR) hjälper dig att hålla igång verksamheten. Under planerade driftstopp och oväntade avbrott håller BCDR data säkra och tillgängliga och ser till att appar fortsätter att köras. Förutom plattforms-BCDR-funktioner som regional parkoppling och lagring med hög tillgänglighet tillhandahåller Azure Återställningstjänster som en integrerad del av din BCDR-lösning. Återvinningstjänster omfattar: 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) säkerhetskopierar dina lokala och Azure VM-data. Du kan säkerhetskopiera en fil och mappar, specifika arbetsbelastningar eller en hel virtuell dator. 
- [Azure Site Recovery](site-recovery-overview.md) ger återhämtning och haveriberedskap för appar och arbetsbelastningar som körs på lokala datorer eller virtuella Azure IaaS-datorer. Site Recovery dirigerar replikering och hanterar redundans till Azure när avbrott inträffar. Den hanterar också återställning från Azure till din primära plats. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Hur gör Site Recovery haveriberedskap?

1. När du har förberett Azure och din lokala plats konfigurerar och aktiverar du replikering för dina lokala datorer.
2. Site Recovery dirigerar den första replikeringen av datorn i enlighet med dina principinställningar.
3. Efter den första replikeringen replikerar Site Recovery deltaändringar i Azure. 
4. När allt replikeras som förväntat kör du en borr för haveriberedskap.
    - Borren hjälper till att säkerställa att redundansen fungerar som förväntat när ett verkligt behov uppstår.
    - Borren utför en testväxling utan att påverka din produktionsmiljö.
5. Om ett avbrott inträffar kör du en fullständig redundans till Azure. Du kan växla över en enda dator eller skapa en återställningsplan som misslyckas över flera datorer samtidigt.
6. Vid redundans skapas virtuella Azure-datorer från VM-data i hanterade diskar eller lagringskonton. Användare kan fortsätta komma åt appar och arbetsbelastningar från Den virtuella Azure-datorn
7. När din lokala webbplats är tillgänglig igen kan du återställa från Azure.
8. När du har växlat tillbaka och arbetar från din primära plats igen börjar du replikera lokala virtuella datorer till Azure igen.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Hur vet jag om min miljö är lämplig för haveriberedskap till Azure?

Site Recovery kan replikera alla arbetsbelastningar som körs på en virtuell virtuell dator eller fysisk server som stöds. Här är de saker du behöver för att kontrollera i din miljö:

- Om du replikerar virtuella virtuella datorer med VMware, kör du rätt versioner av VMware-virtualiseringsservrar? [Kolla här](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
- Är de datorer som du vill replikera som kör ett operativsystem som stöds? [Kolla här](vmware-physical-azure-support-matrix.md#replicated-machines).
- Är maskiner som kör ett filsystem/gästlagring som stöds för Linux-haveriberedskap? [Kolla här](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)
- Uppfyller de datorer som du vill replikera Azure-kraven? [Kolla här](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
- Stöds nätverkskonfigurationen? [Kolla här](vmware-physical-azure-support-matrix.md#network).
- Stöds lagringskonfigurationen? [Kolla här](vmware-physical-azure-support-matrix.md#storage).


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Vad behöver jag konfigurera i Azure innan jag börjar?

I Azure måste du förbereda följande:

1. Kontrollera att ditt Azure-konto har behörighet att skapa virtuella datorer i Azure.
2. Skapa ett Azure-nätverk som Azure virtuella datorer ansluter när de skapas från lagringskonton eller hanterade diskar efter redundans.
3. Konfigurera ett Azure Recovery Services-valv för platsåterställning. Valvet finns i Azure-portalen och används för att distribuera, konfigurera, dirigera, övervaka och felsöka distributionen av site recovery.

*Behöver du mer hjälp?*

Lär dig hur du konfigurerar Azure genom [att verifiera ditt konto,](tutorial-prepare-azure.md#verify-account-permissions)skapa ett [nätverk](tutorial-prepare-azure.md#set-up-an-azure-network)och konfigurera [ett valv](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Vad behöver jag för att konfigurera lokalt innan jag börjar?

Lokalt här är vad du behöver göra:

1. Du måste skapa ett par konton:

    - Om du replikerar virtuella virtuella datorer med VMware behövs ett konto för att Site Recovery ska komma åt vCenter Server- eller vSphere ESXi-värdar för att automatiskt identifiera virtuella datorer.
    - Ett konto behövs för att installera serviceagenten Site Recovery Mobility på varje fysisk dator eller virtuell dator som du vill replikera.

2. Du måste kontrollera kompatibiliteten för din VMware-infrastruktur om du inte tidigare gjorde det.
3. Se till att du kan ansluta till virtuella Azure-datorer efter en redundans. Du konfigurerar RDP på lokala Windows-datorer eller SSH på Linux-datorer.

*Behöver du mer hjälp?*
- Förbered konton för [automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) och installation [av mobilitetstjänsten](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation).
- [Kontrollera](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements) att dina VMware-inställningar är kompatibla.
- [Förbered](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover) så att du ansluter i Azure efter redundans.
- Om du vill ha mer djupgående hjälp med att konfigurera IP-adressering för virtuella Azure-datorer efter redundans [läser du den här artikeln](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Hur ställer jag in haveriberedskap?

När du har din Azure och lokala infrastruktur på plats kan du ställa in haveriberedskap.

1. För att förstå de komponenter som du behöver distribuera, granska [VMware till Azure-arkitektur](vmware-azure-architecture.md)och den [fysiska till Azure-arkitekturen](physical-azure-architecture.md). Det finns ett antal komponenter, så det är viktigt att förstå hur de alla passar ihop.
2. **Källmiljö**: Som ett första steg i distributionen konfigurerar du replikeringskällans miljö. Du anger vad du vill replikera och var du vill replikera till.
3. **Konfigurationsserver:** Du måste konfigurera en konfigurationsserver i den lokala källmiljön:
    - Konfigurationsservern är en enda lokal dator. För VMware-haveriberedskap rekommenderar vi att du distribuerar den som en virtuell virtuell VMware-dator som kan distribueras från en nedladdningsbar OVF-mall.
    - Konfigurationsservern samordnar kommunikationen mellan lokala och Azure
    - Ett par andra komponenter körs på konfigurationsserverdatorn.
        - Processservern tar emot, optimerar och skickar replikeringsdata till cachelagringskontot i Azure. Den hanterar också automatisk installation av mobilitetstjänsten på datorer som du vill replikera och utför automatisk identifiering av virtuella datorer på VMware-servrar.
        - Huvudmålservern hanterar replikeringsdata vid återställning efter fel från Azure.
    - Set up inkluderar registrering av konfigurationsservern i valvet, nedladdning av MySQL Server och VMware PowerCLI och ange de konton som skapats för automatisk identifiering och mobilitetstjänstinstallation.
4. **Målmiljö**: Du ställer in din Azure-målgrupp genom att ange dina Azure-prenumerations- och nätverksinställningar.
5. **Replikeringsprincip**: Du anger hur replikering ska ske. Inställningarna inkluderar hur ofta återställningspunkter skapas och lagras och om appkonsekventa ögonblicksbilder ska skapas.
6. **Aktivera replikering**. Du aktiverar replikering för lokala datorer. Om du har skapat ett konto för att installera mobilitetstjänsten installeras det när du aktiverar replikering för en dator. 

*Behöver du mer hjälp?*

- För en snabb genomgång av dessa steg, kan du prova vår [VMware handledning](vmware-azure-tutorial.md)och [fysisk server genomgång](physical-azure-disaster-recovery.md).
- [Läs mer](vmware-azure-set-up-source.md) om hur du konfigurerar källmiljön.
- [Lär dig mer om](vmware-azure-deploy-configuration-server.md) konfigurationsserverkrav och konfigurera konfigurationsservern med en OVF-mall för VMware-replikering. Om du av någon anledning inte kan använda en mall eller om du replikerar fysiska [servrar, använder](physical-azure-set-up-source.md#set-up-the-source-environment)du dessa instruktioner .
- [Läs mer](vmware-azure-set-up-target.md) om målinställningar.
- [Få mer information](vmware-azure-set-up-replication.md) om hur du ställer in en replikeringsprincip.
- [Lär dig](vmware-azure-enable-replication.md) hur du aktiverar replikering och [utesluter](vmware-azure-exclude-disk.md) diskar från replikering.


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Något gick fel, hur felsöker jag?

- Som ett första steg kan du försöka [övervaka distributionen](site-recovery-monitor-and-troubleshoot.md) för att verifiera status för replikerade objekt, jobb och infrastrukturproblem och identifiera eventuella fel.
- Om du inte kan slutföra den första replikeringen eller om den pågående replikeringen inte fungerar som förväntat [läser du den här artikeln](vmware-azure-troubleshoot-replication.md) för vanliga fel och felsökningstips.
- Om du har problem med den automatiska installationen av mobilitetstjänsten på datorer som du vill replikera läser du vanliga fel i [den här artikeln](vmware-azure-troubleshoot-push-install.md).
- Om redundans inte fungerar som förväntat kontrollerar du vanliga fel i [den här artikeln](site-recovery-failover-to-azure-troubleshoot.md).
- Om återställningen av återställningen inte fungerar kontrollerar du om problemet visas i [den här artikeln](vmware-azure-troubleshoot-failback-reprotect.md).



## <a name="next-steps"></a>Nästa steg

När replikering nu är på plats bör du [köra en borr för haveriberedskap](tutorial-dr-drill-azure.md) för att säkerställa att redundans fungerar som förväntat. 
