---
title: Kör en borrmaskin för haveriberedskap till Azure med Azure Site Recovery
description: Lär dig hur du kör en haveriberedskapsövning från lokalt till Azure med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5bd9f5316f8b8799633de8c0c84c61424c0e4f4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238904"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Köra ett programåterställningstest till Azure

I den här artikeln beskrivs hur du kör en haveriberedskapsövning för en lokal dator till Azure med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md) Med ett test kan du verifiera din replikeringsstrategi utan dataförlust.


Det här är den fjärde självstudien i en serie som visar hur du konfigurerar haveriberedskap till Azure för lokala datorer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Ställer in ett isolerat nätverk för redundanstestet
> * Förbereder för att ansluta till den virtuella Azure-datorn efter en redundansväxling
> * Kör en testväxling för en enda dator.

> [!NOTE]
> Självstudier visar den enklaste distributionsvägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Om du vill veta mer om stegen för att återställa katastrofer mer i detalj [läser du den här artikeln](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Innan du börjar

Slutför de tidigare självstudierna:

1. Kontrollera att du har [konfigurerat Azure](tutorial-prepare-azure.md) för lokal haveriberedskap av virtuella datorer, virtuella datorer med hyper-V och fysiska datorer till Azure.
2. Förbered din lokala [VMware-](vmware-azure-tutorial-prepare-on-premises.md) eller [Hyper-V-miljö](hyper-v-prepare-on-premises-tutorial.md) för haveriberedskap. Om du konfigurerar haveriberedskap för fysiska servrar läser du [supportmatrisen](vmware-physical-secondary-support-matrix.md).
3. Konfigurera haveriberedskap för [virtuella datorer,](vmware-azure-tutorial.md)virtuella datorer med [hyper-v eller](hyper-v-azure-tutorial.md) [fysiska datorer](physical-azure-disaster-recovery.md).
 

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera den virtuella datorns egenskaper innan du kör ett redundanstest, och se till att din [virtuella Hyper-V-dator](hyper-v-azure-support-matrix.md#replicated-vms) eller [virtuella VMware-dator](vmware-physical-azure-support-matrix.md#replicated-machines) uppfyller kraven för Azure.

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > och VM.
2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **Beräkning och Nätverk**kan du ändra Azure-namn, resursgrupp, målstorlek, tillgänglighetsuppsättning och hanterade diskinställningar.
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för redundanstest

Vi rekommenderar att du för redundanstest väljer ett nätverk som är isolerat från produktionsnätverkets återställningsplats specifik i inställningarna för **Beräkning och nätverk** för varje virtuella dator. Som standard, när du skapar ett virtuellt Azure-nätverk är det isolerat från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernäten ska ha samma namn.
- Textnätverket ska använda samma IP-adressintervall.
- Uppdatera DNS för testnätverket med IP-adressen som anges för DNS-VM i inställningarna i **Beräkning och nätverk**. Mer information finns i [Saker att tänka på vid redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Köra ett redundanstest för en enstaka virtuell dator

När du kör ett redundanstest händer följande:

1. En kravkontroll körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
2. Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten, skapas en återställningspunkt från data.
3. En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

Kör redundanstestet på följande sätt:

1. Klicka på den virtuella datorn > **+Test Redundans**i **Inställningar** > **replikerade objekt.**
2. Välj återställningspunkten **Senast bearbetade** för den här självstudien. Då redundansväxlar den virtuella datorn till den senast tillgängliga tidpunkten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
3. I **Redundanstest** väljer du det Azure-målnätverk som de virtuella Azure-datorerna ska ansluta till efter redundans.
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** >
   **Platsåterställningsjobb**.
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den körs.
6. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
7. Vill du ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på den virtuella datorn. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att redundanstiden är längre för VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Ansluta efter redundans

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter redundans [förbereder du dig för att ansluta](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Om du stöter på anslutningsproblem efter redundans följer du [felsökningsguiden.](site-recovery-failover-to-azure-troubleshoot.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kör en redundans- och redundans för virtuella datorer](vmware-azure-tutorial-failover-failback.md)
> med VMware[Kör en redundans- och redundans för virtuella hyper-virtuella datorer](hyper-v-azure-failover-failback-tutorial.md)
> [Kör en redundans- och redundans för fysiska datorer](physical-to-azure-failover-failback.md)
