---
title: Beräkna kapacitet för replikering i Azure | Microsoft Docs
description: Använd den här artikeln för att beräkna kapacitet när du replikerar med hjälp av Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: d9c2645be73c4b6e34d194d6b2444a700e3900d2
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29875913"
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Planera kapacitet för att skydda Hyper-V virtuella datorer med Site Recovery

En ny utökad version av [Azure Site Recovery distribution Planner för Hyper-V till Azure-distribution](site-recovery-hyper-v-deployment-planner.md) är nu tillgänglig. Ersätter verktyget gamla. Använd verktyget ny för att planera din distribution.
Verktyget ger följande riktlinjer:

* VM är behörig assessment, baserat på antalet diskar, diskstorlek, IOPS, omsättning och några VM-egenskaper
* Nätverkets bandbredd måste jämfört med Återställningspunktsmål assessment
* Krav för Azure-infrastrukturen
* Lokala infrastrukturkrav
* Den inledande replikeringen batchbearbetning vägledning
* Uppskattad totala disaster recovery kostnad för Azure


Azure Site Recovery Capacity Planner hjälper dig att avgöra dina kapacitetskrav när du replikerar virtuella Hyper-V-datorer med Azure Site Recovery.

Använd Site Recovery Capacity Planner för att analysera källmiljön och arbetsbelastningar. Det hjälper dig att beräkna bandbreddsbehov måste de serverresurser som du behöver för källplatsen och resurser (t.ex virtuella datorer och lagring) på målplatsen.

Du kan köra verktyget i två lägen:

* **Snabb planera**: innehåller nätverket och servern projektioner baserat på ett genomsnittligt antal virtuella datorer, diskar, lagring och förändringstakten.
* **Detaljerad planering**: innehåller information om varje arbetsbelastning på VM-nivå. Analysera VM-kompatibilitet och får nätverks- och projektioner.

## <a name="before-you-start"></a>Innan du börjar

* Samla in information om din miljö, inklusive virtuella datorer, diskar per virtuell dator lagringsutrymme per disk.
* Identifiera dina dagliga (omsättningen) förändringstakten för replikerade data. Hämta den [Hyper-V kapacitetsplanering verktyget](https://www.microsoft.com/download/details.aspx?id=39057) få förändringstakten. [Lär dig mer](site-recovery-capacity-planning-for-hyper-v-replication.md) om det här verktyget. Vi rekommenderar att du kör det här verktyget över en vecka att avbilda medelvärden.


## <a name="run-the-quick-planner"></a>Kör snabba Planner
1. Ladda ned och öppna [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Du måste köra makron. När du uppmanas du gör inställningar för att aktivera redigering och innehåll.

2. I den **ange planner** listrutan, Välj **snabb Planner**.

   ![Kom igång](./media/site-recovery-capacity-planner/getting-started.png)

3. På den **Capacity Planner** kalkylblad, ange nödvändig information. Fyll i alla fält inringad i rött i följande skärmbild:

   a. I **Välj ditt scenario**, Välj **Hyper-V till Azure** eller **VMware/fysiska till Azure**.

   b. I **genomsnittliga dagliga data ändras hastighet (%)**, ange den information som du samlar in med hjälp av den [Hyper-V kapacitetsplanering verktyget](site-recovery-capacity-planning-for-hyper-v-replication.md) eller [Site Recovery-distribution Planner](./site-recovery-deployment-planner.md).

   c. Den **komprimering** inställningen används inte när du replikerar virtuella Hyper-V-datorer till Azure. Använda en tredjeparts-utrustning, till exempel Riverbed för komprimering.

   d. I **Kvarhållningsintervall i dagar**, ange hur lång tid att behålla replikerna dagar.

   e. I **antalet timmar för inledande replikering för grupp med virtuella datorer bör slutföras** och **antalet virtuella datorer per batch för inledande replikering**, ange inställningar som används för att beräkna krav för inledande replikering. När Platsåterställningen har distribuerats kan överföra hela första datauppsättningen.

   ![Indata](./media/site-recovery-capacity-planner/inputs.png)

4. När du har angett värden för källmiljön innehåller visas utdata:

   * **Bandbredd som krävs för deltareplikering (i megabit per sekund)**: nätverksbandbredd för deltareplikering beräknas på genomsnittliga dagliga förändringstakten för data.
   * **Bandbredd som krävs för inledande replikering (i megabit per sekund)**: nätverkets bandbredd för inledande replikering beräknas på inledande replikering värdena som du anger.
   * **Lagringsutrymme som krävs (i GB)**: den totala Azure lagringsutrymme som krävs.
   * **Totalt antal IOPS på standardlagring**: antalet beräknas baserat på 8 kB IOPS storlek på på totalt standard storage-konton. Antalet beräknas baserat på alla diskar för käll-VM och dagliga data ändra frekvensen för snabb Planner. För detaljerad Planner numret beräknas baserat på det totala antalet virtuella datorer som är mappade till standard virtuella Azure-datorer och data ändra på dessa virtuella datorer.
   * **Antalet standardlagring konton som krävs för**: det totala antalet standard storage-konton som krävs för att skydda de virtuella datorerna. Ett standardlagringskonto kan innehålla upp till 20 000 IOPS över alla virtuella datorer i standardlagring. Högst 500 IOPS stöds per disk.
   * **Antal Blob-diskar som krävs för**: antalet diskar som skapas på Azure-lagring.
   * **Antal premium-konton som krävs för**: det totala antalet premium storage-konton som krävs för att skydda de virtuella datorerna. En källa för virtuell dator med hög IOPS (större än 20 000) måste ett premiumlagringskonto. Premium-lagringskonto kan innehålla upp till 80 000 IOPS.
   * **Totalt antal IOPS på Premium-lagring**: antalet beräknas baserat på 256 kB IOPS storlek på på totalt premium storage-konton. Antalet beräknas baserat på alla diskar för käll-VM och dagliga data ändra frekvensen för snabb Planner. För detaljerad Planner numret beräknas baserat på det totala antalet virtuella datorer som är mappade till premium virtuella Azure-datorer (DS och GS-serien) och data ändra på dessa virtuella datorer.
   * **Antal Configuration-servrar krävs**: Visar hur många configuration-servrar som krävs för distributionen.
   * **Antal ytterligare servrar krävs**: Visar om ytterligare servrar krävs, förutom som körs på konfigurationsservern som standard.
   * **100% ytterligare lagringsutrymme på käll-**: Visar om ytterligare lagringsutrymme krävs på källplatsen.

      ![Resultat](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Kör detaljerad Planner

1. Ladda ned och öppna [Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel). Du måste köra makron. När du uppmanas du gör inställningar för att aktivera redigering och innehåll.

2. I **ange planner**väljer **detaljerad Planner** i listrutan.

   ![Guide för att komma igång](./media/site-recovery-capacity-planner/getting-started-2.png)

3. På den **arbetsbelastning kriteriet** kalkylblad, ange nödvändig information. Du måste fylla i de markerade fälten.

   a. I **processorkärnor**, ange det totala antalet kärnor på en källserver.

   b. I **minnesallokering (i MB)**, ange storleken för RAM-minne på en källserver.

   c. I **antalet nätverkskort**, ange antalet nätverkskort på en källserver.

   d. I **totala lagringsutrymmet (i GB)**, ange den totala storleken på VM-lagring. Om källservern har tre diskar med 500 GB, är totalt antal lagringsstorlek 1 500 GB.

   e. I **antalet diskar som är anslutna**, ange det totala antalet diskar på en källserver.

   f. I **Disk kapacitetsutnyttjande (%)**, ange genomsnittlig användning.

   g. I **dagliga data ändras hastighet (%)**, ange dagliga data ändra frekvensen för en källserver.

   h. I **mappning Azure VM-storlek**, ange den Virtuella Azure-storlek som du vill mappa. Om du inte vill att göra detta manuellt, Välj **Compute IaaS-VM**. Om du ange en manuell inställning och välj sedan **Compute IaaS-VM**, manuell inställning kan skrivas över. Compute-processen identifierar automatiskt den bästa matchningen på Azure VM-storlek.

   ![Arbetsbelastningen kriteriet kalkylblad](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Om du väljer **Compute IaaS-VM**, här är syfte:

   * Verifierar obligatoriska indata.
   * Beräknar IOPS och ger förslag på den bästa Azure VM storlek matchningen för varje virtuell dator som är berättigade för replikering till Azure. Om en lämplig storlek som virtuella Azure-datorn inte kan identifieras, visas ett fel. Till exempel visas antalet diskar som är anslutna är 65, ett fel eftersom högsta storlek för en Azure VM är 64.
   * Tyder på ett lagringskonto som kan användas för en Azure VM.
   * Beräknar antalet lagringskonton som standard och premium storage-konton som krävs för arbetsbelastning. Rulla nedåt för att visa Azure storage-typ och lagringskontot som kan användas för en källserver.
   * Slutför och sorterar resten av tabellen baserat på lagring (standard eller premium) för en virtuell dator och antalet diskar som är anslutna. För alla virtuella datorer som uppfyller kraven för Azure, kolumnen **är VM kvalificerade?** visar **Ja**. Om en virtuell dator inte kan säkerhetskopieras till Azure, visas ett fel.

Kolumnerna AA-AE är resultatet och ange information för varje virtuell dator.

![Utdatakolumnerna AA-AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exempel
Exempel för sex virtuella datorer med de värden som visas i tabellen, verktyget beräknar och tilldelar den bästa Azure VM-matchningen och Azure storage-krav.

![Arbetsbelastningen kriteriet tilldelningar](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Tänk på följande i exempel på utdata:

  * Den första kolumnen är en verifiering kolumn för virtuella datorer, diskar och omsättning.
  * Två standard storage-konton och en premium storage-konto krävs för fem virtuella datorer.
  * VM3 inte uppfyller kraven för skydd eftersom en eller flera diskar är mer än 1 TB.
  * VM1 och VM2 kan använda det första standardlagringskontot
  * VM4 kan använda andra standardlagringskontot.
  * VM5 och VM6 måste ett premiumlagringskonto och både kan använda ett konto.

    > [!NOTE]
    > IOPS på standard och premium-lagring beräknas på VM-nivå och inte på disk nivå. Standard VM kan hantera upp till 500 IOPS per disk. Om IOPS för en disk som är större än 500, måste premium-lagring. Om IOPS för en disk som är mer än 500 men IOPS för total VM-diskarna i standard Azure VM stödbegränsningarna, hämtar planeringsverktyget en vanliga virtuell dator och inte DS eller GS-serien. (De Virtuella Azure-gränserna är VM-storlek, antalet diskar, antalet nätverkskort, CPU och minne.) Du måste manuellt uppdatera mappning Azure storlek cell i lämplig DS eller GS-serien VM.


Efter att all information har angetts, Välj **skicka data till kapacitetsplaneringsverktyget för** att öppna Capacity Planner. Arbetsbelastningar som är markerade för att visa om de är berättigade till skydd.

### <a name="submit-data-in-capacity-planner"></a>Skicka data i Capacity Planner
1. När du öppnar den **Capacity Planner** kalkylbladet fylls baserat på de inställningar som du har angett. Ordet ”arbetsbelastning” visas i den **Infra indata källa** cellen som visar att indata är den **arbetsbelastning kriteriet** kalkylblad.

2. Om du vill göra ändringar, måste du ändra den **arbetsbelastning kriteriet** kalkylblad. Välj sedan **skicka data till kapacitetsplaneringsverktyget för** igen.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du kör](site-recovery-capacity-planning-for-hyper-v-replication.md) verktyget för kapacitetsplanering.
