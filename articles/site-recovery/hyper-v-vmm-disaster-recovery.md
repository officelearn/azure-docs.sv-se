---
title: "Ställ in katastrofåterställning för Hyper-V virtuella datorer mellan lokala platser med Azure Site Recovery | Microsoft Docs"
description: "Lär dig hur du ställer in katastrofåterställning för Hyper-V virtuella datorer mellan din lokala platser med Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 13dcc0794c1d89bd27c79cbe6636397da4f008f9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Ställ in katastrofåterställning för Hyper-V virtuella datorer till en sekundär lokal plats

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här artikeln visar hur du ställer in återställning till en sekundär plats för lokala Hyper-V-datorer hanteras i System Center Virtual Machine Manager (VMM)-moln. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Förbereda lokal VMM-servrar och Hyper-V-värdar
> * Skapa ett Recovery Services-valv för Site Recovery 
> * Ställ in källa och mål replikering miljöer. 
> * Konfigurera nätverksmappning 
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator

## <a name="prerequisites"></a>Förutsättningar

Så här slutför det här scenariot:

- Granska de [scenariots arkitektur och komponenter](hyper-v-vmm-architecture.md).
- Kontrollera att VMM-servrar och Hyper-V-värdar uppfyller [supportkrav](hyper-v-vmm-secondary-support-matrix.md).
- Kontrollera att virtuella datorer du vill replikera följa [replikerade datorn support](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Förbereda VMM-servrar för nätverksmappning.

### <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning

[Nätverksmappning](hyper-v-vmm-network-mapping.md) mappningar mellan lokala VMM VM-nätverk i käll- och moln. Mappning gör följande:

- Ansluter virtuella datorer till lämpliga mål VM-nätverk efter en redundansväxling. 
- Optimalt placerar Replikdatorerna på mål-Hyper-V-värdservrar. 
- Om du inte konfigurerar nätverksmappning ska replikering VMs inte anslutas till ett Virtuellt datornätverk efter växling vid fel.

Förbered VMM på följande sätt:

1. Kontrollera att du har [logiska nätverk i VMM](https://docs.microsoft.com/system-center/vmm/network-logical) på käll- och VMM-servrarna.
    - Det logiska nätverket på källservern ska associeras med det källmoln där Hyper-V-värdar finns.
    - Det logiska nätverket på målservern ska vara associerat med målmolnet.
1. Kontrollera att du har [Virtuella datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på käll- och VMM-servrarna. Virtuella datornätverk som ska kopplas till det logiska nätverket på varje plats.
2. Ansluta virtuella datorer på Hyper-V-värdar för källa till källnätverket. 


## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Välj en skyddsmål

Välj vad och vart du vill replikera.

1. Klicka på **Site Recovery** > **steg 1: Förbered infrastrukturen** > **skyddsmål**.
2. Välj **till återställningsplatsen**, och välj **Ja, med Hyper-V**.
3. Välj **Ja** att indikera att du använder VMM för att hantera Hyper-V-värdar.
4. Välj **Ja** om du har en sekundär VMM-server. Om du distribuerar replikering mellan moln på en VMM-server, klickar du på **nr**. Klicka sedan på **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Installera Azure Site Recovery-providern på VMM-servrar och identifiera och registrera servrar i valvet.

1. Klicka på **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server.
3. I **Lägg till Server**, kontrollera att **System Center VMM-servern** visas i **servertyp**.
4. Ladda ned installationsfilen för Azure Site Recovery-providern.
5. Ladda ned registreringsnyckeln. Du behöver detta när du installerar providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Installera providern på varje VMM-servern. Du behöver inte installera något uttryckligen på Hyper-V-värdar.

### <a name="install-the-azure-site-recovery-provider"></a>Installera Azure Site Recovery-providern

1. Kör installationsfilen på varje VMM-servern för providern. Om VMM distribueras i ett kluster, installera för första gången på följande sätt:
    -  Installera providern på en aktiv nod och slutför installationen för att registrera VMM-servern i valvet.
    - Installera sedan providern på de andra noderna. Klusternoder bör köra samma version av providern.
2. Installationsprogrammet körs några kontroller av förutsättningar och kräver behörighet att stoppa VMM-tjänsten. VMM-tjänsten kommer att startas om automatiskt när installationen är klar. Om du installerar på en VMM-klustret, uppmanas du att stoppa klusterrollen.
3. I **Microsoft Update**, kan du välja för att ange att providern uppdateringar är installerade i enlighet med Microsoft Update-princip.
4. I **Installation**acceptera eller ändra standardplatsen för installation och klicka på **installera**.
5. När installationen är klar klickar du på **registrera** att registrera servern i valvet.

    ![Installationsplats](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. I **Valvnamn** kontrollerar du namnet på valvet som servern ska registreras i. Klicka på **Nästa**.
7. I **proxyanslutningen**, ange hur providern som körs på VMM-servern ansluter till Azure.
   - Du kan ange att providern ska ansluta direkt till internet eller via en proxyserver. Ange proxyinställningarna.
   - Om du använder en proxyserver skapas VMM RunAs-konto (DRAProxyAccount) automatiskt, med hjälp av de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Inställningarna för RunAs-konto kan ändras i VMM-konsolen > **inställningar** > **säkerhet** > **kör som-konton**.
   - Starta om VMM-tjänsten för att uppdatera ändringar.
8. I **registreringsnyckel**, välja den nyckel som du laddat ned och kopieras till VMM-servern.
9. Krypteringsinställningen är inte relevant i det här scenariot. 
10. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. Ange VMM-rollen klusternamnet i ett kluster.
11. I **synkronisera molnmetadata**väljer du om du vill synkronisera metadata för alla moln på VMM-servern. Den här åtgärden behöver bara göras en gång på varje server. Lämna den här inställningen avmarkerad om du inte vill synkronisera alla moln. Du kan synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen.
12. Slutför processen genom att klicka på **Nästa**. Efter registreringen hämtas metadata från VMM-servern i Site Recovery. Servern visas i **servrar** > **VMM-servrar** i valvet.
13. När servern visas i valvet, i **källa** > **Förbered källa** Välj VMM-servern och välj det moln som Hyper-V-värden finns. Klicka sedan på **OK**.


## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj VMM-målservern och molnet:

1. Klicka på **Förbered infrastruktur** > **mål**, och välj VMM-målservern.
2. VMM-moln som är synkroniserade med Site Recovery visas. Välj målmolnet.

   ![Mål](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

Innan du börjar bör du se till att alla värdar med principer har samma operativsystem. Om värdar kör olika versioner av Windows Server, måste flera replikeringsprinciper.

1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn. Typen för källan och målet ska vara **Hyper-V**.
3. I **Hyper-V-värdversion**, Välj vilket operativsystem som körs på värden.
4. I **autentiseringstyp** och **autentiseringsport**, ange hur trafik autentiseras mellan de primära platsen och återställningsplatsen Hyper-V-värdservrarna.
    - Välj **certifikat** om du inte har en fungerande Kerberos-miljö. Azure Site Recovery kommer automatiskt att konfigurera certifikat för HTTPS-autentisering. Du behöver inte göra något manuellt.
    - Som standard öppnas port 8083 och 8084 (för certifikat) i Windows-brandväggen på Hyper-V-värdservrar.
    - Om du väljer **Kerberos**, en Kerberos-biljett används för ömsesidig autentisering av värdservrar. Kerberos är endast relevant för Hyper-V-värdservrar som körs på Windows Server 2012 R2 eller senare.
1. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).
2. I **kvarhållningstid för återställningspunkten**, ange \how lång tid (i timmar) blir kvarhållningsperiod för varje återställningspunkt. Replikerade datorer kan återställas till valfri punkt inom ett fönster.
3. I **frekvens av programkonsekventa ögonblicksbilder**, ange hur ofta (1 – 12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder skapas. Hyper-V använder två typer av ögonblicksbilder:
    - **Standardögonblicksbild**: tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn.
    - **Programkonsekventa ögonblicksbilder**: tar en ögonblicksbild i tidpunkt av programdata inuti den virtuella datorn. Volume Shadow Copy Service (VSS) säkerställer att appar finns i ett konsekvent tillstånd när ögonblicksbilden tas. Om du aktiverar programkonsekventa ögonblicksbilder, påverkar prestanda på virtuella källdatorer. Ange ett värde som är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
4. I **dataöverföring komprimering**, ange om överförda replikeringsdata ska komprimeras.
5. Välj **ta bort replikering VM**, för att ange att den replikerade virtuella datorn ska tas bort om du inaktiverar skydd för den Virtuella källdatorn. Om du aktiverar den här inställningen när du inaktiverar skyddet för källan VM det tas bort från Site Recovery-konsolen, Site Recovery-inställningarna för VMM tas bort från VMM-konsolen och repliken tas bort.
6. I **inledande Replikeringsmetod**, om du replikerar över nätverket, ange om du vill starta den inledande replikeringen eller schemalägga. Om du vill spara bandbredd i nätverket, kanske du vill schemalägga den utanför kontorstid. Klicka sedan på **OK**.

     ![Replikeringsprincip](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Ny princip associeras automatiskt med VMM-molnet. I **replikeringsprincipen**, klickar du på **OK**. 


## <a name="enable-replication"></a>Aktivera replikering

1. Klicka på **Replikera program** > **Källa**. 
2. I **källa**, Välj VMM-servern och det moln där Hyper-V-värdar som du vill replikera finns. Klicka sedan på **OK**.
3. I **mål**, kontrollera sekundär VMM-servern och molnet.
4. I **virtuella datorer**, Välj de virtuella datorerna som du vill skydda från listan.


Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. Efter den **Slutför skydd** jobbet är slutfört den inledande replikeringen är klar och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg

[Köra ett återställningstest](hyper-v-vmm-test-failover.md)
