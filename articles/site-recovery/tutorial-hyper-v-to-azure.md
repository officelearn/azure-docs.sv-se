---
title: "Ställ in haveriberedskap för lokala Hyper-V virtuella datorer till Azure med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du ställer in haveriberedskap för lokala Hyper-V virtuella datorer till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 45e9b4dc-20ca-4c14-bee3-cadb8d9b8b24
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 4d43fb03ce1c54a47315b8c3a5c83ec2082bcab9
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Ställ in haveriberedskap för lokala Hyper-V virtuella datorer till Azure

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din strategi för katastrofåterställning genom att hantera och samordna replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här kursen visar hur du ställer in haveriberedskap för lokala Hyper-V virtuella datorer till Azure. Kursen är relevant för Hyper-V virtuella datorer som hanteras i System Center Virtual Machine Manager (VMM) moln och de som inte finns. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Ställ in krav för Azure och lokalt
> * Skapa ett Recovery Services-valv för Site Recovery 
> * Konfigurera käll- och mål-replikering miljöer 
> * Konfigurera nätverksmappning (om Hyper-V hanteras av System Center VMM)
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator


## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- Se till att du förstår de [scenariots arkitektur och komponenter](concepts-hyper-v-to-azure-architecture.md).
- Granska de [supportkrav](site-recovery-support-matrix-to-azure.md) för alla komponenter.
- Se till att virtuella datorer du vill replikera följer [krav för Azure VM](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
- Ta reda på kapacitetsplanering:
    - Använd den [Kapacitetsplaneringsverktyget för Hyper-V](http://aka.ms/asr-capacity-planner-excel)att räkna ut din omsättningen.
    - Använd den [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) beräkna bandbredd för att analysera din källmiljö och mål-krav.
- Förbered Azure. Du behöver en Azure-prenumeration, Azure-nätverk och ett lagringskonto.
- Förbered lokala Hyper-V-värdar och VMM-servrar om så behövs.





### <a name="set-up-an-azure-account"></a>Konfigurera ett Azure-konto

Hämta en Microsoft [Azure-konto](http://azure.microsoft.com/).

- Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- Lär dig mer om [priserna för Site Recovery](site-recovery-faq.md#pricing), och få [prisinformationen](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ta reda på vilka [regioner stöds](https://azure.microsoft.com/pricing/details/site-recovery/) för Site Recovery.

### <a name="verify-azure-account-permissions"></a>Kontrollera behörigheter för Azure-konto

Kontrollera att ditt Azure-konto har de behörigheter som behövs för att replikera virtuella datorer.

- Granska de [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) måste du replikera datorer till Azure
- Kontrollera och ändra [rollbaserad åtkomst](../active-directory/role-based-access-control-configure.md) behörigheter. 


### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Konfigurera en [Azure-nätverk](../virtual-network/virtual-network-get-started-vnet-subnet.md).

- Virtuella Azure-datorer placeras i det här nätverket när de skapas efter växling vid fel.
- Nätverket måste finnas på samma region som Recovery Services-valvet.


### <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Konfigurera en [Azure storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- Site Recovery replikerar lokala datorer till Azure-lagring. Virtuella Azure-datorer skapas från lagringen efter redundansväxlingen.
- Lagringskontot måste vara i samma region som Recovery Services-valvet.
- Lagringskontot kan vara standard eller [premium](../virtual-machines/windows/premium-storage.md).
- Om du ställer in ett premium-konto behöver du en ytterligare standardkonto för loggdata.

### <a name="prepare-hyper-v-hosts"></a>Förbereda Hyper-V-värdar

1. Kontrollera att Hyper-V-värdar uppfyller [supportkrav](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
2. Kontrollera att värdarna kan komma åt dessa webbadresser:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP-adressbaserade brandväggsregler ska tillåta kommunikation till Azure.
    - Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.
    - Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för hantering av kontrollen och identitet).

### <a name="prepare-vmm-servers"></a>Förbereda VMM-servrar

Om Hyper-V-värdar hanteras av VMM, måste du förbereda lokal VMM-servern. 

- Kontrollera att VMM-servern uppfyller [supportkrav](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Kontrollera att VMM-servern har ett minst ett moln med en eller flera värdgrupper. Hyper-V-värden som virtuella datorer som körs måste finnas i molnet.
- VMM-servern behöver Internetåtkomst med åtkomst till följande webbadresser:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP-adressbaserade brandväggsregler ska tillåta kommunikation till Azure.
    - Tillåt [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (IP-intervall för Azures datacenter) och HTTPS-port 443.
    - Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).
- Förbereda VMM-servern för nätverksmappning.


#### <a name="prepare-vmm-for-network-mapping"></a>Förbereda VMM för nätverksmappning

Om du använder VMM [nätverksmappning](site-recovery-network-mapping.md) mappningar mellan lokala VMM VM-nätverk och virtuella Azure-nätverk. Mappning säkerställer att virtuella datorer i Azure är ansluten till rätt nätverk när de skapas efter växling vid fel.

Förbered VMM nätverksmappningen på följande sätt:

1. Kontrollera att du har en [logiskt nätverk för VMM](https://docs.microsoft.com/system-center/vmm/network-logical) som associeras med det moln där Hyper-V-värdarna finns.
2. Kontrollera att du har en [Virtuellt datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) länkad till det logiska nätverket.
3. Ansluta de virtuella datorerna till det Virtuella datornätverket.

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="select-a-protection-goal"></a>Välj en skyddsmål

Välj vad som ska replikera och var du vill replikera den till.

1. Klicka på valvet, **Site Recovery** > **Förbered infrastrukturen** > **skyddsmål**.
2. I **skyddsmål**väljer **till Azure**> **Ja, med Hyper-V**. 
    - Om Hyper-V-värdar inte hanteras av VMM, väljer **nr** att bekräfta att du inte använder VMM.
    - Om värdar hanteras i VMM-moln, Välj **Ja**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

När du ställer in källmiljön kan du installera Azure Site Recovery-providern och Azure Recovery Services-agenten och registrera lokala servrar i valvet. 

1. I **Förbered infrastrukturen**, klickar du på **källa**. 
    - Om du inte använder VMM, klickar du på **+ Hyper-V-platsen**, och ange ett platsnamn. Klicka på **+ Hyper-V Server**, och lägga till en värd eller kluster på webbplatsen.
    - Om du använder VMM i **Förbered källa**, klickar du på **+ VMM** att lägga till en VMM-server. I **Lägg till Server**, kontrollera att **System Center VMM-servern** visas i **servertyp**.
2. Ladda ned providern och agenten komponenter, beroende på din miljö.
    - Hämta installationsfilen för providern för Hyper-V bara. Du kör filen på varje Hyper-V-värden för att installera både providern och agenten.
        
        ![Providern utan VMM](./media/tutorial-hyper-v-to-azure/download-no-vmm.png)
    
    - Hyper-V med VMM, hämta providern och agenten separat. Kör providerinstallationen på VMM-servern. Kör agentinstallationen på varje Hyper-V-värd.
    
        ![Providern och agenten med VMM](./media/tutorial-hyper-v-to-azure/download-vmm.png)
    
3. Ladda ned valvregistreringsnyckeln. Du behöver detta när du kör installationsprogrammet för providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

### <a name="install-components"></a>Installera komponenter

Installera komponenterna som summmarized i tabellen. 

**Komponent** | **Detaljer** | **Hyper-V endast** | **Hyper-V med VMM**
--- | --- | --- | ---
**Azure Site Recovery-providern** | Samordnar replikering till Azure | Installera på varje Hyper-V-värd | Installera på VMM-servern
**Recovery Services-agenten** | Hanterar datareplikering | Installera på varje Hyper-V-värd | Installera på Hyper-V-värd


#### <a name="install-the-provider-on-hyper-v-without-vmm"></a>Installera providern på Hyper-V utan VMM

Installera providern på varje Hyper-V-värd som du lagt till Hyper-V-platsen. Om du installerar på en Hyper-V-kluster, kan du köra installationsprogrammet på varje nod i klustret. Installera och registrera varje klusternod för Hyper-V säkerställer att virtuella datorer är skyddade, även om de migrera mellan noder.

1. I **Microsoft Update** kan du välja uppdateringar så att provideruppdateringarna installeras i enlighet med din Microsoft Update-princip.
2. I **Installation** accepterar du eller ändrar standardinstallationsplatsen för providern och klickar på **Installera**.
4. I **Valvinställningar**, klickar du på **Bläddra** att välja valvnyckelfilen som du hämtat. Ange Azure Site Recovery-prenumerationen och valvnamnet Hyper-V-platsen som Hyper-V-servern tillhör.
5. I **proxyinställningar**, ange hur providern som körs på VMM-server eller Hyper-V-värd som ansluter till Site Recovery via internet.
    * För en direkt anslutning väljer **Anslut direkt till Azure Site Recovery utan en proxy**.
    * En proxy, Välj **Anslut till Azure Site Recovery via en proxyserver**. Ange proxy-adress, port och autentiseringsuppgifter om det behövs.
6. När servern är registrerad i valvet, klickar du på **Slutför**.

Hämtas metadata från Hyper-V-servern av Azure Site Recovery och servern visas i **Site Recovery-infrastruktur** > **Hyper-V-värdar**.


#### <a name="install-the-recovery-services-agent-on-hyper-v-host-without-vmm"></a>Installera Recovery Services-agenten på Hyper-V-värden utan VMM

Om du använder VMM i distributionen, kör du installationsprogrammet för Recovery Services-agenten på varje Hyper-V-värd.

1. I installationsguiden för det > **Kravkontroll**, klickar du på **nästa**. Alla nödvändiga komponenter som saknas installeras automatiskt.

    ![Krav för Recovery Services-agenten](./media/tutorial-hyper-v-to-azure/hyperv-agent-prerequisites.png)
3. Godkänn eller ändra installationsplatsen och cachelagringsplatsen på **Installationsinställningar**. Cacheenhet behöver minst 5 GB lagringsutrymme. Vi rekommenderar en enhet med 600 GB eller mer ledigt utrymme. Klicka på **Installera**.
4. I **Installation**, när installationen är klar, klickar du på **Stäng** och slutföra guiden.

##### <a name="set-up-internet-access-via-a-proxy"></a>Konfigurera Internetåtkomst via en proxyserver

Recovery Services-agenten på Hyper-V-värden behöver Internetåtkomst till Azure för VM-replikering. Om du ansluter till Internet via en proxyserver konfigurerar du den så här:

1. Öppna snapin-modulen Microsoft Azure Backup MMC på Hyper-V-värden. Som standard finns en genväg till Microsoft Azure Backup på skrivbordet eller i C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.
3. På den **proxykonfiguration** anger proxyinformation.

    ![Registrera MARS-agenten](./media/tutorial-hyper-v-to-azure/mars-proxy.png)
4. Kontrollera att agenten kan nå den [krävs URL: er](#prepare-hyper-v-hosts).

#### <a name="install-the-provider-on-the-vmm-server-hyper-v-with-vmm"></a>Installera providern på VMM-servern (Hyper-V med VMM)

1. I **Microsoft Update** kan du välja uppdateringar så att provideruppdateringarna installeras i enlighet med din Microsoft Update-princip.
2. I **Installation** accepterar du eller ändrar standardinstallationsplatsen för providern och klickar på **Installera**. 
3. Nu registrera VMM-servern i valvet. I **Valvinställningar**, klickar du på **Bläddra** att välja valvnyckelfilen som du hämtat. Ange Azure Site Recovery-prenumerationen och valvnamnet.

    ![Serverregistrering](./media/tutorial-hyper-v-to-azure/provider-vault-settings.png)

4. I **proxyinställningar**, ange hur providern som körs på VMM-server eller Hyper-V-värd som ansluter till Site Recovery via internet.

    * För en direkt anslutning väljer **Anslut direkt till Azure Site Recovery utan en proxy**.
    * En proxy, Välj **Anslut till Azure Site Recovery via en proxyserver**. Ange proxy-adress, port och autentiseringsuppgifter om det behövs.
    - VMM RunAs-konto (DRAProxyAccount) skapas automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Inställningarna för RunAs-konto kan ändras i VMM-konsolen > **inställningar** > **säkerhet** > **kör som-konton**. Starta om VMM-tjänsten för att uppdatera ändringar.
5. I **datakryptering**, ange om krypteras alla data som skickas till Azure. Om du väljer det här alternativet Site Recovery utfärdar ett certifikat. Du behöver det här certifikatet kan dekryptera data vid ett senare tillfälle.
6. I **VMM-servern**, ange ett eget namn som identifierar VMM-servern i valvet. I en klusterkonfiguration anger du namnet på VMM-klusterrollen. I **synkronisera molnmetadata med valvet**väljer du om du vill synkronisera metadata för alla moln på VMM-servern med valvet. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln kan du lämna den här inställningen avmarkerad och synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen.

När registreringen är klar, hämtas metadata från servern av Azure Site Recovery och VMM-servern visas i **Site Recovery-infrastruktur**.






## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera target-resurser. 

1. Klicka på **Förbered infrastruktur** > **mål**.
2. Välj prenumerationen och resursgruppen som virtuella Azure-datorer kommer att skapas efter växling vid fel. Välj distributionsmodell som du vill använda i Azure för de virtuella datorerna.

3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="configure-network-mapping-with-vmm"></a>Konfigurera nätverksmappning (med VMM)

Om du använder VMM kan du konfigurera nätverksmappning.

1. Under **Site Recovery-infrastruktur** > **Nätverksmappningar** > **Nätverksmappning** klickar du på ikonen **+Nätverksmappning**.
2. I **Lägg till nätverksmappning**, Välj VMM-källservern. Välj **Azure** som mål.
3. Kontrollera prenumerationen och distributionsmodellen efter redundansväxling.
4. I **Källnätverk**, Välj källnätverket lokalt.
5. I **målnätverket**, Välj Azure-nätverk i vilka Azure-Replikdatorerna kommer att finnas när de skapas efter växling vid fel. Klicka sedan på **OK**.

    ![Nätverksmappning](./media/tutorial-hyper-v-to-azure/network-mapping-vmm.png)

## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Klicka på **Förbered infrastruktur** > **replikeringsinställningarna** > **+ skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn.
3. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).

    > [!NOTE]
    >  En frekvens på 30 sekunder stöds inte när du replikerar till premiumlagring. Begränsningen bestäms av antalet ögonblicksbilder per blob (100) som stöds av premium-lagring. [Läs mer](../virtual-machines/windows/premium-storage.md#snapshots-and-copy-blob).

4. I **kvarhållningstid för återställningspunkten**, ange hur länge kvarhållningsperiod är (i timmar) för varje återställningspunkt. Skyddade datorer kan återställas till valfri punkt inom en period.
5. I **Appkompatibel ögonblicksbildsfrekvens** anger du hur ofta (1–12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder ska skapas. Hyper-V använder två typer av ögonblicksbilder:
    - **Standardögonblicksbild**: tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn.
    - **Programkonsekventa ögonblicksbilder**: tar en ögonblicksbild i tidpunkt av programdata inuti den virtuella datorn. Volume Shadow Copy Service (VSS) säkerställer att appar finns i ett konsekvent tillstånd när ögonblicksbilden tas. Om du aktiverar programkonsekventa ögonblicksbilder påverkar prestanda på virtuella källdatorer. Ange ett värde som är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
6. I **Starttid för inledande replikering** anger du när den inledande replikeringen ska börja. Replikeringen sker via internetbandbredden så du kanske vill schemalägga den utanför användningsnivå.
7. I **Kryptera data lagrade på Azure** anger du om du vill kryptera vilande data i Azure-lagring. Klicka sedan på **OK**.

    ![Replikeringsprincip](./media/tutorial-hyper-v-to-azure/replication-policy.png)


När du skapar en ny princip associeras den automatiskt med VMM-moln eller Hyper-V-platsen.

## <a name="enable-replication"></a>Aktivera replikering


1. Klicka på **replikera program** > **källa**. 
2. I **källa**, Välj Hyper-V plats eller VMM-servern/moln. Klicka sedan på **OK**.
3. I **mål**, kontrollera Azure som mål, valvet prenumerationen och den modell som du vill använda i Azure efter växling vid fel.
4. Välj lagringskonto för replikerade data och Azure-nätverk som virtuella Azure-datorer kommer att finnas efter växling vid fel.
5. I **virtuella datorer** > **Välj**, Välj de virtuella datorerna som du vill replikera. Klicka sedan på **OK**.

 Du kan följa förloppet för den **Aktivera skydd** åtgärden i **jobb** > **Site Recovery-jobb**. Efter den **Slutför skydd** jobbet är slutfört den inledande replikeringen är klar och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg
[Köra ett återställningstest](tutorial-dr-drill-azure.md)
