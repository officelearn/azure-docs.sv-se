---
title: Hur fungerar Hyper-V-replikering till Azure i Site Recovery? | Microsoft Docs
description: "I den här artikeln får du en översikt över hur Hyper-V-replikering fungerar i Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.lasthandoff: 04/18/2017


---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>Hur fungerar Hyper-V-replikering till Azure?

Läs den här artikeln för att förstå arkitekturen och arbetsflödena för Hyper-V-replikering till Azure med tjänsten [Azure Site Recovery](site-recovery-overview.md).

Skriv dina kommentarer längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Du kan replikera följande till Azure:
- **Hyper-V med VMM**: Virtuella datorer som finns på en lokal Hyper-V-värd hanteras i System Center Virtual Machine Manager (VMM)-moln. Värdar kan köras på valfritt [operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Du kan replikera virtuella Hyper-V-datorer som körs på ett gästoperativsystem [som stöds av Hyper-V och Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- **Hyper-V utan VMM**: Lokala virtuella datorer som finns på Hyper-V-värdar som inte hanteras i VMM-moln. Värdar kan köra valfritt [operativsystem som stöds](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Du kan replikera virtuella Hyper-V-datorer som körs på ett gästoperativsystem [som stöds av Hyper-V och Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="architectural-components"></a>Arkitekturkomponenter

**Område** | **Komponent** | **Detaljer**
--- | --- | ---
**Azure** | I Azure behöver du ett Microsoft Azure-konto, ett Azure-lagringskonto och ett Azure-nätverk. | Lagring och nätverk kan vara Resource Manager-baserade eller klassiska konton.<br/><br/> Replikerade data lagras i lagringskontot och virtuella Azure-datorer skapas med replikerad data när redundansväxling uppstår från din lokala plats.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**VMM-server** | Hyper-V-värdar i VMM-moln | Om Hyper-V-värdar hanteras i VMM-moln kan du registrera VMM-servern i Recovery Services-valvet.<br/><br/> På VMM-servern installerar du Site Recovery-providern för att samordna replikeringen med Azure.<br/><br/> Du behöver logiska nätverk och VM-nätverk för att konfigurera nätverksmappning. Ett nätverk för virtuella datorer bör vara kopplat till ett logiskt nätverk som är associerat med molnet.
**Hyper-V-värd** | Hyper-V-servrar kan distribueras med eller utan VMM-servern. | Om det inte finns någon VMM-server installeras Site Recovery-providern på värden för att dirigera replikering med Site Recovery via internet. Om det finns en VMM-server installeras providern på den och inte på värden.<br/><br/> Recovery Services-agenten installeras på värden för att hantera datareplikering.<br/><br/> Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.
**Virtuella Hyper-V-datorer** | Du behöver en eller flera virtuella datorer på Hyper-V-värdservern. | Inget behöver uttryckligen installeras på virtuella datorer

## <a name="deployment-steps"></a>Distributionssteg

1. **Azure**: Du ställer in Azure-komponenterna. Vi rekommenderar att du ställer in konton för lagring och nätverk innan du påbörjar Site Recovery-distributionen.
2. **Valv**: Du skapar ett Recovery Services-valv för Site Recovery och konfigurerar valvinställningarna, däribland inställningar för källa och mål, konfiguration av en replikeringsprincip och aktivering av replikering.
3. **Källa och mål**:
    - **Hyper-V-värdar i VMM-moln**: Som en del av att ange inställningar för datakälla kan du hämta och installera Azure Site Recovery-providern på VMM-servern och Azure Recovery Services-agenten på varje Hyper-V-värd. Källan är VMM-servern. Målet är Azure.
    - Hyper-V-värdar utan VMM: När du anger källinställningar för datakälla kan du hämta och installera providern och agenten på varje Hyper-V-värd. Under distributionen samlar du värdar på en Hyper-V-plats och anger den här platsen som källa. Målet är Azure.

    ![Hyper-V-/VMM-replikering till Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Hyper-V-platsreplikering till Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **Replikeringsprincip**: Du skapar en replikeringsprincip för Hyper-V-platsen eller VMM-molnet. Principen tillämpas på alla virtuella datorer som befinner sig på värdar på platsen eller i molnet.
5. **Aktivera replikering**: Du aktiverar replikering för virtuella Hyper-V-datorer. Inledande replikering sker i enlighet med inställningarna för replikeringsprincipen. Dataändringar spåras och replikering av deltaändringar till Azure påbörjas efter att den inledande replikeringen har slutförts. Spårade ändringar för ett objekt lagras i en .hrl-fil.
6. **Redundanstest**: Du kör ett redundanstest för att kontrollera att allt fungerar.

Lär dig mer om distribution:
- [Kom igång med replikering av virtuella Hyper-V-datorer till Azure – med VMM](site-recovery-vmm-to-azure.md)
- [Kom igång med replikering av virtuella Hyper-V-datorer till Azure](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Arbetsflöde för Hyper-V-replikering

### <a name="enable-protection"></a>Aktivera skydd

1. När du har aktiverat skydd för en virtuell Hyper-V-dator i Azure Portal eller lokalt startar **Aktivera skydd**.
2. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) som konfigurerar replikering med de inställningar som du har konfigurerat.
3. Jobbet startar den initiala replikeringen genom att aktivera metoden [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) för att initiera en fullständig VM-replikering och skicka de virtuella datorernas diskar till Azure.
4. Du kan övervaka jobbet på fliken **Jobs** (Jobb).
        ![Jobblista](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Aktivera visning av mer detaljer för skydd](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>Inledande replikering

1. En [ögonblicksbild av en virtuell Hyper-V-dator](https://technet.microsoft.com/library/dd560637.aspx) tas när den initiala replikeringen utlöses.
2. Virtuella hårddiskar replikeras en i taget tills alla har kopierats till Azure. Det kan ta en stund, beroende på den virtuella datorns storlek och nätverkets bandbredd. Om du vill optimera nätverksanvändningen läser du [How to manage on-premises to Azure protection network bandwidth usage](https://support.microsoft.com/kb/3056159) (Hantera bandbreddsanvändning för skydd från lokal plats till Azure).
3. Om diskändringar inträffar under en pågående inledande replikering spåras dessa ändringar av spårningsverktyget för Hyper-V-replikering som Hyper-V-replikeringsloggar (.hrl). De här filerna finns i samma mapp som diskarna. Varje disk har en associerad HRL-fil som skickas till den sekundära lagringsplatsen.
4. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
5. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


### <a name="finalize-protection"></a>Slutför skyddet

1. När replikeringen har slutförts konfigurerar jobbet **Slutför skydd på den virtuella datorn** nätverksinställningar och andra inställningar som krävs efter replikeringen så att den virtuella datorn är skyddad.
    ![Slutför skyddet](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Om du replikerar till Azure kan du behöva justera inställningarna för den virtuella datorn så att den är redo för redundans. Nu kan du köra ett redundanstest för att kontrollera att allt fungerar som förväntat.

### <a name="delta-replication"></a>Deltareplikering

1. Efter den första replikeringen startar deltasynkroniseringen baserat på replikeringsinställningarna.
2. Spårningsverktyget för Hyper-V-replikering spårar ändringarna till en virtuell hårddisk som .hrl-filer. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil. Loggen skickas till kundens lagringskonto när den inledande replikeringen är klar. När en logg överförs till Azure spåras ändringarna i den primära disken i en annan loggfil, i samma katalog.
3. Under initial och deltareplikering kan du övervaka den virtuella datorn i VM-vyn. [Läs mer](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="replication-synchronization"></a>Replikeringssynkronisering

1. Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så markeras en virtuell dator för omsynkronisering. Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering.
2.  Vid en omsynkronisering minimeras mängden data som skickas genom att kontrollsummor beräknas för de virtuella käll- och måldatorerna varefter endast deltadata skickas. Omsynkronisering använder en segmenteringsalgoritm med fasta block där käll- och målfiler delas in i fasta segment. Kontrollsummor skapas för varje segment skapas och jämförs sedan för att avgöra vilka block från källan som ska tillämpas på målet.
3. När omsynkroniseringen är klar ska den normala deltareplikeringen återupptas. Omsynkroniseringen schemaläggs som standard för automatisk körning utanför kontorstid, men du kan synkronisera om en virtuell dator manuellt. Du kan till exempel återuppta omsynkroniseringen om ett nätverksavbrott eller något annat avbrott inträffar. Det gör du genom att välja den virtuella datorn i portalen > **Resynchronize** (Omsynkronisera).

    ![Manuell omsynkronisering](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>Antal försök

Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Den här logiken kan delas in i två kategorier:

**Kategori** | **Detaljer**
--- | ---
**Oåterkalleliga fel** | Inga nya försök görs. Status för den virtuella datorn är **kritisk**, och administratören måste ingripa. Exempel på sådana fel är: trasig VHD-kedja, ogiltigt tillstånd för den virtuella replikdatorn, nätverksautentiseringsfel, autentiseringsfel, den virtuella datorn hittas inte (för fristående Hyper-V-servrar)
**Återkalleliga fel** | Återförsök sker varje replikeringsintervall, med ett exponentiellt undantagsläge som ökar återförsöksintervallet från det första försökets start med 1, 2, 4, 8 och 10 minuter. Om felet kvarstår försöker du var 30: e minut. Exempel: nätverksfel, för lite diskutrymme, för lite minne |

## <a name="protection-and-recovery-lifecycle"></a>Livscykel för skydd och återställning

![arbetsflöde](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Nästa steg

- [Kontrollera förhandskrav för distribution](site-recovery-prereq.md)
- Felsök med:
    - [Övervaka och felsöka skydd](site-recovery-monitoring-and-troubleshooting.md)
    - [Hjälp från Microsoft Support](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [Vanliga fel och lösningar](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

