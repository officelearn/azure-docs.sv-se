---
title: "Köra ett redundanstest för fysisk serverreplikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattas de steg som du behöver för att köra ett redundanstest för [fysiska servrar som replikerar till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94aa3bfc700cad3de9fc5516c0c9a4d86ade3fed
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="step-11-run-a-test-failover-of-physical-servers-to-azure"></a>Steg 11: Kör ett redundanstest av fysiska servrar till Azure

Den här artikeln beskriver hur du kör ett redundanstest från lokala fysiska servrar till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) tjänsten i Azure-portalen.

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Innan du börjar

Innan du kör ett redundanstest rekommenderar vi att du kontrollerar egenskaperna för servern och gör eventuella ändringar behöver du. Du kan komma åt VM-egenskaper i **replikerade objekt**. Den **Essentials** bladet visar information om inställningarna för datorn och status.

## <a name="managed-disk-considerations"></a>Överväganden för hanterade diskar

[Hanterade diskar](../virtual-machines/windows/managed-disks-overview.md) förenkla Diskhantering för virtuella Azure-datorer genom att hantera de lagringskonton som är associerade med VM-diskarna. 

- När du aktiverar skyddet för en server replikerar VM-data till ett lagringskonto. Hanterade diskar skapas och anslutna till den virtuella datorn endast när växling vid fel.
- Hanterade diskar kan endast skapas för Azure virtuella datorer som distribueras med hjälp av Resource Manager-modellen.  
- Den här inställningen bara tillgänglighetsuppsättningar i resursgrupper som har **Använd hanterade diskar** aktiverat kan väljas. Virtuella datorer med hanterade diskar måste vara i tillgänglighetsuppsättningar med **Använd hanterade diskar** inställd på **Ja**. Om inställningen inte är aktiverat för virtuella datorer, kan endast tillgänglighetsuppsättningar resursgrupper utan hanterade diskar aktiverad markeras.
- [Lär dig mer](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) om hanterade diskar och tillgänglighet anger.
- Om lagringskontot som du använder för replikering har krypterats med Storage Service-kryptering, kan hanterade diskar inte skapas under växling vid fel. I det här fallet antingen inte aktivera användning av hanterade diskar, eller inaktivera skyddet för den virtuella datorn och återaktivera och använda ett lagringskonto som inte har kryptering aktiverat. [Läs mer](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="network-considerations"></a>Nätverksöverväganden

Du kan ange IP-måladressen för en Azure VM som skapades efter redundansväxlingen.

- Om du inte anger någon adress använder den redundansväxlade datorn DHCP.
- Om du anger en adress som inte är tillgänglig under växling vid fel, fungerar inte växling vid fel.
- Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket.
- Antalet nätverkskort beror på storleken du anger för den virtuella måldatorn:

     - Om antalet nätverkskort på källdatorn är samma som eller mindre än antalet nätverkskort som tillåts för måldatorns storlek och målet ska ha samma antal kort som källa.
     - Om antalet nätverkskort för den virtuella källdatorn överstiger det antalet för målstorleken, används högsta.
     - Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.     
   - Om den virtuella datorn har flera nätverkskort ansluts alla till samma nätverk.
   - Om den virtuella datorn har flera nätverkskort så att den första som visas i listan blir den *standard* nätverkskort i den virtuella Azure-datorn.
 - [Lär dig mer](vmware-walkthrough-network.md) om IP-adresser.



## <a name="view-and-modify-vm-settings"></a>Visa och ändra inställningarna för VM

Vi rekommenderar att du kontrollerar egenskaperna för källservern innan du kör en redundansväxling.

1. I **skyddade objekt**, klickar du på **replikerade objekt**, och klicka på datorn.
2. I den **replikerade objekt** fönstret visas en sammanfattning av information om datorn, hälsotillstånd och de senaste tillgängliga återställningspunkterna. Klicka på **egenskaper** visa mer information.
3. I **beräknings- och nätverksinställningar**, kan du:
    - Ändra namnet på virtuella Azure-datorn. Namnet måste uppfylla [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Ange postredundans [resursgrupp].
    - Ange en Målstorlek för den virtuella Azure-datorn
    - Välj en [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md).
    - Ange om du vill använda [hanterade diskar](#managed-disk-considerations). Välj **Ja**, om du vill bifoga hanterade diskar till din dator på migrering till Azure.
    - Visa eller ändra inställningar för nätverk, inklusive de undernät eller det nätverk som virtuella Azure-datorn kommer att finnas efter växling vid fel och IP-adressen som ska tilldelas till den.
4. I **diskar**, visas information om operativsystem och datadiskar på den virtuella datorn.

## <a name="run-a-test-failover"></a>Köra ett redundanstest

När du har konfigurerat allt, fungerar köra ett redundanstest för att kontrollera att allt som förväntat.

- Om du vill ansluta till virtuella Azure-datorer med RDP efter en växling vid fel, [kunna ansluta](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Om du vill testa fullständigt behöver du kopiera Active Directory och DNS i din testmiljö. [Läs mer](site-recovery-active-directory.md#test-failover-considerations).
 - Fullständig information om redundanstestet [i den här artikeln](site-recovery-test-failover-to-azure.md) artikel.
- Få en snabb överblick av video innan du börjar:

     
     >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]

Kör nu en växling vid fel:

1. Att växla över en enskild dator i **inställningar** > **replikerade objekt**, klicka på datorn > **+ testa redundans** ikon.

    ![Testa redundans](./media/physical-walkthrough-test-failover/test-failover.png)

2. Om du vill redundansväxla en återställningsplan går du till **Inställningar** > **Återställningsplaner**, högerklickar på planen > **Testa redundans**. Om du vill skapa en återställningsplan [följer du dessa instruktioner](site-recovery-create-recovery-plans.md).  

3. I **Redundanstest**, Välj Azure-nätverk till vilka virtuella Azure-datorer ska ansluta till efter redundansväxlingen.

4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på datorn och öppna dess egenskaper eller på den **Redundanstest** jobb i valvnamnet > **inställningar** > **jobb**  >  **Site Recovery-jobb**.

5. När redundansväxlingen är klar du bör även kunna se Azure VM visas i Azure-portalen > **virtuella datorer**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den körs.

6. Om du förberedde för anslutning efter redundansväxlingen bör du kunna ansluta till den virtuella Azure-datorn.

### <a name="delete-test-failover-vms"></a>Ta bort testa redundans virtuella datorer

1. När du är klar klickar du på **Rensa redundanstestet** på återställningsplan eller datorn.
2. I **anteckningar**, registrera och spara observationer från redundanstestningen.
3. Rensa åtgärden tar bort virtuella Azure-datorer som har skapats under redundanstestningen.

## <a name="summary"></a>Sammanfattning

Om du har slutförts redundanstestningen fysiska servrarna replikerar och du har kontrollerat att de kan växla över till Azure. Nu kan du köra redundansväxlingar i enlighet med din organisations krav. 

Kom ihåg att du kan för närvarande inte tillbaka från Azure till en fysisk server. Måste du växla tillbaka till en VMware-VM. Det innebär att du behöver en lokal VMware-infrastruktur för att återställas. [Lär dig mer](site-recovery-failback-azure-to-vmware.md) om misslyckas tillbaka virtuella Azure-datorer till VMware.


## <a name="next-steps"></a>Nästa steg

- [Köra redundansväxlingar](site-recovery-failover.md) efter behov.
