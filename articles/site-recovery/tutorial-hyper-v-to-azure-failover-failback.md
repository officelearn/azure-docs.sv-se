---
title: "Växla över och misslyckas tillbaka Hyper-V VM replikerat till Azure med Site Recovery | Microsoft Docs"
description: "Lär dig hur du växla över Hyper-V virtuella datorer till Azure och växla tillbaka till den lokala platsen med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Växla över och misslyckas tillbaka Hyper-V VM replikerat till Azure

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten hanterar och samordnar replikering, redundans och återställning efter fel för lokala datorer och virtuella Azure-datorer (VM).

Den här självstudiekursen beskrivs hur du redundansväxlar en Hyper-V virtuell dator till Azure. När du har redundansväxlats växlar du tillbaka till den lokala platsen när den är tillgänglig. I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Växla över Hyper-V virtuella datorer från lokal till Azure
> * Växla tillbaka från Azure till lokala och börja replikering till Azure igen

## <a name="overview"></a>Översikt
Redundans och återställning efter fel har två steg:

1. **Växla över till Azure**: Redundansväxlar datorer från den lokala platsen till Azure.
2. **Växla tillbaka från Azure till lokala**: kör en planerad redundans från Azure till lokala. Efter den planerade redundansväxlingen aktiverar du omvänd replikering, att starta replikering från lokal till Azure igen. 


## <a name="fail-over-to-azure"></a>Växla över till Azure

### <a name="failover-prerequisites"></a>Krav för redundans

Så här slutför redundans:

- Kontrollera att du har slutfört en [disaster recovery ökad](tutorial-dr-drill-azure.md) och kontrollera att allt fungerar som förväntat.
- Om du vill förbereda att ansluta till virtuella Azure-datorer innan du redundansväxlar.
- Kontrollera egenskaper för Virtuella datorer innan du kör redundansväxlingen.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Om du vill ansluta till virtuella Azure-datorer med RDP efter en växling vid fel, gör du följande:

##### <a name="azure-vms-running-windows"></a>Azure virtuella datorer som kör Windows

1. För att komma åt virtuella Azure-datorer via internet aktiverar du RDP på den lokala virtuella datorn före redundans. Kontrollera att TCP och UDP-regler har lagts till för den **offentliga** profil och att RDP tillåts i **Windows-brandväggen** > **tillåtna appar** för alla profiler.
2. För att komma åt via plats-till-plats-VPN, aktiverar du RDP på den lokala datorn. RDP ska tillåtas i den **Windows-brandväggen** -> **tillåtna appar och funktioner** för **domän och privat** nätverk. Kontrollera att operativsystemets SAN policyn är inställd att **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135). Det ska vara ingen Windows-uppdateringar som väntar på den virtuella datorn när du utlösa redundansväxling. Om det inte finns kan du inte logga in på den virtuella datorn förrän uppdateringen är klar. 
3. På Windows Azure VM efter växling vid fel, kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn. Om du inte kan ansluta, kontrollera att den virtuella datorn körs och granska dessa [felsökningstips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


##### <a name="azure-vms-running-linux"></a>Azure virtuella datorer som kör Linux

1. Kontrollera att Secure Shell-tjänsten är inställd att starta automatiskt på datorn startar på den lokala datorn före redundans. Kontrollera att brandväggsreglerna tillåter en SSH-anslutning.
2. Tillåta inkommande anslutningar till SSH-porten för reglerna för nätverkssäkerhetsgrupper på den redundansväxlade virtuella datorn och Azure-undernätet som den är ansluten på Azure-VM efter växling vid fel.  [Lägg till en offentlig IP-adress](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) för den virtuella datorn. Du kan kontrollera **starta diagnostik** att visa en skärmbild av den virtuella datorn.


#### <a name="verify-vm-properties"></a>Kontrollera egenskaperna för VM

Kontrollera egenskaper för Virtuella datorer och kontrollera att den virtuella datorn uppfyller [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. I **skyddade objekt**, klickar du på **replikerade objekt** > VM.
2. I den **replikerade objekt** , det finns en sammanfattning av VM-information, hälsostatus, och den senaste tillgängliga återställningspunkter. Klicka på **egenskaper** visa mer information.
3. I **beräknings- och nätverksinställningar**, kan du ändra Azure namn, resursgruppens namn, Målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md), och [hanteras Diskinställningar](#managed-disk-considerations)
4. Du kan visa och ändra inställningar för nätverk, inklusive de undernät eller det nätverk som virtuella Azure-datorn kommer att finnas efter växling vid fel och IP-adressen som ska tilldelas till den.
5. I **diskar**, visas information om operativsystem och datadiskar på den virtuella datorn.


### <a name="run-a-failover-from-on-premises-to-azure"></a>Kör en redundansväxling från lokal till Azure

Du kan köra en vanlig eller planerad redundans för Hyper-V virtuella datorer

- Använda en vanlig redundans för oväntade avbrott. När du kör den här redundansen skapar en virtuell dator i Azure Site Recovery och används upp. Du kör redundansväxlingen mot en specifik återställningspunkt. Data kan gå förlorade beroende på den återställningspunkt som du använder.
- En planerad redundans kan användas för underhåll, eller under förväntade avbrott. Det här alternativet ger noll dataförlust. När en planerad redundans utlöses är virtuella källdatorer avstängda. Osynkroniserade data synkroniseras och växling vid fel utlöses.

Den här proceduren beskriver hur du kör en vanlig redundans.


1. I **inställningar** > **replikerade objekt** klickar du på den virtuella datorn > **redundans**.
2. I **redundans** väljer en **återställningspunkt** att växla över till. Du kan använda något av följande alternativ:
    - **Senaste** (standard): det här alternativet först bearbetar alla data som skickas till Site Recovery. De ger den lägsta RPO (mål för återställningspunkt) eftersom Azure VM skapas efter växling vid fel har alla data som har replikerats till Site Recovery när redundans utlöstes.
    - **Senaste bearbetas**: det här alternativet växlar den virtuella datorn till den senaste återställningspunkten som bearbetas av Site Recovery. Det här alternativet ger en låga RTO (mål), eftersom ingen tid läggs obearbetade data bearbetas.
    - **Senaste programkonsekventa**: det här alternativet växlar den virtuella datorn till den senaste programkonsekventa återställningspunkten bearbetas av Site Recovery. 
    - **Anpassad**: Ange en återställningspunkt.
3. Om du växlar över Hyper-V virtuella datorer i System Center VMM-moln till Azure och datakryptering är aktiverat för molnet i **krypteringsnyckeln**, Välj det certifikat som utfärdades när du har aktiverat datakryptering under installationsprogram på VMM-servern...
4. Välj **Stäng datorn innan du påbörjar redundans** om du vill använda Site Recovery att göra en avstängning av virtuella källdatorer innan växling vid fel. Site Recovery försöker synkronisera lokala data som ännu inte har skickats till Azure, innan växling vid fel. Observera att redundansväxlingen fortsätter även om avstängning misslyckas. Du kan följa förloppet för växling vid fel på den **jobb** sidan.
5. Om du förberett att ansluta till den virtuella Azure-datorn ansluta för att verifiera efter växling vid fel.
6. När du har kontrollerat **genomför** växling vid fel. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Inte avbryta en växling pågår**: innan redundans startas VM replikeringen har stoppats. Om du avbryter en växling pågår, stoppar för växling vid fel, men den virtuella datorn inte kommer att replikeras igen.  


## <a name="fail-back-from-azure-to-on-premises"></a>Växla tillbaka från Azure till lokala

### <a name="prerequisites-for-failback"></a>Krav för återställning efter fel

Kontrollera att den primära VMM servern/Hyper-V-servern är ansluten till Site Recovery för att slutföra återställning efter fel.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>Köra en återställning efter fel och skyddar lokala virtuella datorer

Växla över från Azure till den lokala platsen och starta replikering av virtuella datorer från den lokala platsen till Azure.

1. I **inställningar** > **replikerade objekt** klickar du på den virtuella datorn > **planerad redundans**.
2. I **bekräfta planerad redundans**kontrollerar redundansriktning (från Azure) och välj käll- och målplatserna. 
3. I **datasynkronisering**, ange hur du vill synkronisera:
    - **Synkronisera data innan redundans (synkronisera endast deltaändringar)**– det här alternativet minimerar avbrottstid för virtuell dator eftersom det synkroniserar utan att stänga av den virtuella datorn. Här är syfte:
        1. Tar en ögonblicksbild av den virtuella Azure-datorn och kopierar den till den lokala Hyper-V-värden. Den virtuella datorn körs i Azure.
        2. Stänger ned Azure VM, så att inga nya ändringar sker det. Den slutgiltiga uppsättningen deltaändringar överförs till den lokala servern och den lokala virtuella datorn har startats.
    - **Synkronisera data under växling vid fel endast (fullständig hämtning)**– Använd det här alternativet om du har kört i Azure under lång tid. Det här alternativet är snabbare, eftersom vi räknar med flera disk ändringar och inte ägna tid åt kontrollsumma beräkningar. Det här alternativet utför en disk hämtning. Det är också användbart när den lokala virtuella datorn har tagits bort.
4. Om du växlar över Hyper-V virtuella datorer i System Center VMM-moln till Azure och datakryptering är aktiverat för molnet i **krypteringsnyckeln**, Välj det certifikat som utfärdades när du har aktiverat datakryptering under installationsprogram på VMM-servern.
5. Påbörja redundans. Du kan följa förloppet för växling vid fel på den **jobb** fliken.
6. Om du har valt för att synkronisera data innan växling vid fel, efter den inledande datasynkroniseringen är klar och du är redo att stänga av den virtuella Azure-datorer klickar du på **jobb** > planerad redundans jobbnamn > **slutföra växling vid fel**. Detta stängs av Azure VM, överför den senaste ändringar lokalt och startar den virtuella datorn lokalt.
7. Logga in på den lokala virtuella datorn att kontrollera att den är tillgänglig som förväntat.
8. Den lokala virtuella datorn är nu i ett bekräfta väntande tillstånd. Klicka på **Commit**, för att genomföra redundans. Detta tar bort den virtuella Azure-datorer och dess diskar och förbereder den lokala virtuella datorn för omvänd replikering.
9. Starta replikering av den lokala virtuella datorn till Azure genom att aktivera **omvänd replikera**.


> [!NOTE]
> Omvänd replikering replikeras endast ändringar som har gjorts sedan den virtuella Azure-datorn har varit avstängd, och endast deltaändringar skickas.

