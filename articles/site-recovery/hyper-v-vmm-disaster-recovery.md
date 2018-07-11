---
title: Konfigurera haveriberedskap för Hyper-V-datorer mellan lokala platser med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap för Hyper-V-datorer mellan din lokala platser med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 4cc4da130d9253bf40e5d02806088a95b2195e7c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915916"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Konfigurera haveriberedskap för Hyper-V-datorer till en sekundär lokal plats

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här artikeln visar hur du konfigurerar haveriberedskap till en sekundär plats för den lokala Hyper-V-datorer hanteras i System Center Virtual Machine Manager (VMM)-moln. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Förbereda lokala VMM-servrar och Hyper-V-värdar
> * Skapa ett Recovery Services-valv för Site Recovery 
> * Ställ in källa och mål replikering miljöer. 
> * Konfigurera nätverksmappning 
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här scenariot:

- Granska den [scenariots arkitektur och komponenter](hyper-v-vmm-architecture.md).
- Se till att VMM-servrar och Hyper-V-värdar som följer [supportkrav](hyper-v-vmm-secondary-support-matrix.md).
- Kontrollera att virtuella datorer som du vill replikera uppfylla [replikerade datorn support](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Förbereda VMM-servrar för nätverksmappning.

### <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning

[Nätverksmappning](hyper-v-vmm-network-mapping.md) maps mellan lokala VMM-VM-nätverk i käll- och målmolnen. Mappningen gör följande:

- Ansluter virtuella datorer till lämplig mål VM-nätverk efter redundansväxling. 
- Optimalt placerar virtuella replikdatorerna på mål-Hyper-V-värdservrar. 
- Om du inte konfigurerar nätverksmappning ska replikering av VMs inte anslutas till ett Virtuellt datornätverk efter redundansväxling.

Förbereda VMM på följande sätt:

1. Kontrollera att du har [logiska nätverk i VMM](https://docs.microsoft.com/system-center/vmm/network-logical) på VMM-servrar för källa och mål.
    - Det logiska nätverket på källservern ska associeras med det källmoln som Hyper-V-värdarna finns.
    - Det logiska nätverket på målservern ska associeras med målmolnet.
1. Kontrollera att du har [Virtuella datornätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på VMM-servrar för källa och mål. Virtuella datornätverk bör vara kopplat till det logiska nätverket på varje plats.
2. Ansluta virtuella datorer på Hyper-V-värdar för källan till VM-källnätverket. 


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Välj en skyddsmål

Välj vad och vart du vill replikera.

1. Klicka på **Site Recovery** > **steg 1: förbereda infrastrukturen** > **skyddsmål**.
2. Välj **till återställningsplats**, och välj **Ja, med Hyper-V**.
3. Välj **Ja** att indikera att du använder VMM för att hantera Hyper-V-värdar.
4. Välj **Ja** om du har en sekundär VMM-server. Om du distribuerar replikering mellan moln på en enda VMM-server, klickar du på **nr**. Klicka sedan på **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Installera Azure Site Recovery-providern på VMM-servrar, och identifiera och registrera servrar i valvet.

1. Klicka på **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server.
3. I **Lägg till Server**, kontrollerar du att **System Center VMM-server** visas i **servertyp**.
4. Ladda ned installationsfilen för Azure Site Recovery-providern.
5. Ladda ned registreringsnyckeln. Du behöver den när du installerar providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Installera providern på varje VMM-server. Du behöver inte installera något uttryckligen på Hyper-V-värdar.

### <a name="install-the-azure-site-recovery-provider"></a>Installera Azure Site Recovery-providern

1. Kör installationsfilen på varje VMM-servern för providern. Om VMM distribueras i ett kluster, installera för första gången på följande sätt:
    -  Installera providern på en aktiv nod och slutför installationen för att registrera VMM-servern i valvet.
    - Installera sedan providern på de andra noderna. Klusternoder bör köra samma version av providern.
2. Installationsprogrammet körs några nödvändiga kontroller och begär tillstånd att stoppa VMM-tjänsten. VMM-tjänsten startas automatiskt när installationen är klar. Om du installerar på en VMM-klustret, uppmanas du att stoppa klusterrollen.
3. I **Microsoft Update**, kan du välja för att ange att provideruppdateringarna installeras i enlighet med Microsoft Update-princip.
4. I **Installation**, acceptera eller ändra standardplatsen för installation och klicka på **installera**.
5. När installationen är klar klickar du på **registrera** att registrera servern i valvet.

    ![Installationsplats](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. I **Valvnamn** kontrollerar du namnet på valvet som servern ska registreras i. Klicka på **Nästa**.
7. I **proxyanslutning**, anger du hur providern som körs på VMM-servern ska ansluta till Azure.
   - Du kan ange att providern ska ansluta direkt till internet eller via en proxyserver. Ange proxyinställningar.
   - Om du använder en proxyserver skapas VMM RunAs-konto (DRAProxyAccount) automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Kan ändra inställningarna för RunAs-konto i VMM-konsolen > **inställningar** > **Security** > **kör som-konton**.
   - Starta om VMM-tjänsten för att uppdatera ändringarna.
8. I **Registreringsnyckeln**, välja den nyckel som du hämtade och kopierade till VMM-servern.
9. Krypteringsinställningen är inte relevant i det här scenariot. 
10. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I ett kluster, anger du namnet på VMM-klusterrollen.
11. I **synkronisera molnmetadata**väljer du om du vill synkronisera metadata för alla moln på VMM-servern. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln, lämnar du den här inställningen avmarkerad. Du kan synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen.
12. Slutför processen genom att klicka på **Nästa**. Site Recovery hämtar metadata från VMM-servern efter registreringen. Servern visas i **servrar** > **VMM-servrar** i valvet.
13. När servern visas i valvet i **källa** > **Förbered källa** markerar du VMM-servern och välj det moln som Hyper-V-värden finns. Klicka sedan på **OK**.


## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj VMM-målservern och molnet:

1. Klicka på **Förbered infrastruktur** > **Target**, och välj VMM-målservern.
2. VMM-moln som är synkroniserade med Site Recovery visas. Välj målmolnet.

   ![Mål](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

Innan du börjar bör du se till att alla värdar som använder principen har samma operativsystem. Om värdarna kör olika versioner av Windows Server, måste flera replikeringsprinciper.

1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** > **+Skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn. Käll- och typen ska vara **Hyper-V**.
3. I **Hyper-V-värdversion**, väljer vilket operativsystem körs på värden.
4. I **autentiseringstyp** och **autentiseringsport**, ange hur trafik autentiseras mellan de primära Hyper-V-värdservrarna.
    - Välj **certifikat** om du inte har en fungerande Kerberos-miljö. Azure Site Recovery kommer automatiskt att konfigurera certifikat för HTTPS-autentisering. Du behöver inte göra något manuellt.
    - Som standard öppnas port 8083 och 8084 (för certifikat) i Windows-brandväggen på Hyper-V-värdservrar.
    - Om du väljer **Kerberos**, en Kerberos-biljett används för ömsesidig autentisering av värdservrar. Kerberos är endast relevant för Hyper-V-värdservrar som kör på Windows Server 2012 R2 eller senare.
1. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).
2. I **kvarhållning av återställningspunkt**, ange \how lång tid (i timmar) blir kvarhållningsperioden för varje återställningspunkt. Replikerade datorer kan återställas till valfri punkt inom en period.
3. I **appkonsekvent ögonblicksbildsfrekvens**, ange hur ofta (1 – 12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder skapas. Hyper-V använder två typer av ögonblicksbilder:
    - **Standardögonblicksbild**: tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn.
    - **Appkompatibel ögonblicksbild**: en point-in-time-ögonblicksbild av programdata på den virtuella datorn. Volume Shadow Copy Service (VSS) säkerställer att appar är i ett konsekvent tillstånd när ögonblicksbilden tas. Om du aktiverar programkonsekventa ögonblicksbilder, påverkar prestanda på virtuella datorer. Ange ett värde som är mindre än antalet ytterligare återställningspunkter som du konfigurerar.
4. I **komprimering av dataöverföring**, ange om överförda replikeringsdata ska komprimeras.
5. Välj **ta bort replik VM**, för att ange att den replikerade virtuella datorn ska tas bort om du inaktiverar skydd för den Virtuella källdatorn. Om du aktiverar den här inställningen när du inaktiverar skyddet för Virtuella tas den bort från Site Recovery-konsolen källdatorn, Site Recovery-inställningar för VMM tas bort från VMM-konsolen och repliken tas bort.
6. I **inledande Replikeringsmetod**, om du replikerar över nätverket, ange om du vill starta den inledande replikeringen eller schemalägga. För att spara bandbredd i nätverket, kanske du vill schemalägga den utanför kontorstid. Klicka sedan på **OK**.

     ![Replikeringsprincip](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Den nya principen associeras automatiskt med VMM-molnet. I **replikeringsprincip**, klickar du på **OK**. 


## <a name="enable-replication"></a>Aktivera replikering

1. Klicka på **Replikera program** > **Källa**. 
2. I **källa**, väljer du VMM-servern och det moln som Hyper-V-värdar som du vill replikera finns. Klicka sedan på **OK**.
3. I **Target**, kontrollera sekundär VMM-servern och molnet.
4. I **virtuella datorer**, Välj de virtuella datorerna som du vill skydda i listan.


Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. Efter den **Slutför skydd** jobbet har slutförts, den inledande replikeringen är klar och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg

[Köra ett återställningstest](hyper-v-vmm-test-failover.md)
