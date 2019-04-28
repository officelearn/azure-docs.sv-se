---
title: Planera kapacitet för Hyper-V-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Använd den här artikeln för att beräkna kapacitet när du konfigurerar haveriberedskap med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: eeadfd6a57ff8a26f3f124e2a807fcd66e77b85f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036782"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planera kapacitet för Hyper-V VM-katastrofåterställning 

En ny och förbättrad version av [Azure Site Recovery Deployment Planner för Hyper-V till Azure-distribution](site-recovery-hyper-v-deployment-planner.md) är nu tillgänglig. Det ersätter verktyget gamla. Använd det nya verktyget för att planera din distribution.
Verktyget tillhandahåller följande riktlinjer:

* Kvalificering utvärdering av virtuella datorer, baserat på antal diskar, diskstorlek, IOPS, dataomsättning och några VM-egenskaper
* Nätverkets bandbreddsbehov kontra utvärdering av Återställningspunktmål
* Krav för Azure-infrastrukturen
* Kraven på lokal infrastruktur
* Inledande vägledning till batchreplikering
* Uppskattad total kostnad för haveriberedskap till Azure


Azure Site Recovery Capacity Planner hjälper dig att avgöra dina kapacitetsbehov när du replikerar virtuella Hyper-V-datorer med Azure Site Recovery.

Använd Site Recovery Capacity Planner för att analysera din källmiljö och arbetsbelastningar. Det hjälper dig att beräkna bandbreddsbehov genom de serverresurser som du behöver för källplatsen, och resurserna (till exempel virtuella datorer och lagring) på målplatsen.

Du kan köra verktyget i två lägen:

* **Snabb planering**: Ger nätverks- och projektioner baserat på det genomsnittliga antalet virtuella datorer, diskar, lagring och förändringstakten.
* **Detaljerad planera**: Innehåller information om varje arbetsbelastning på VM-nivå. Analysera VM-kompatibilitet och få nätverks- och projektioner.

## <a name="before-you-start"></a>Innan du börjar

* Samla in information om din miljö, inklusive virtuella datorer, diskar per virtuell dator, lagringsutrymme per disk.
* Identifiera dina dagliga (omsättningen) förändringstakten för replikerade data. Ladda ned den [kapacitetsplanering för Hyper-V verktyget](https://www.microsoft.com/download/details.aspx?id=39057) att hämta förändringstakten. [Läs mer](site-recovery-capacity-planning-for-hyper-v-replication.md) om det här verktyget. Vi rekommenderar att du kör det här verktyget i veckan, avbilda medelvärden.


## <a name="run-the-quick-planner"></a>Kör Quick Planeringsverktyget
1. Ladda ned och öppna [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Du måste köra makron. När du uppmanas göra val för att aktivera redigering och innehåll.

2. I den **Välj en typ av planner** listrutan Välj **snabb Planner**.

   ![Kom igång](./media/site-recovery-capacity-planner/getting-started.png)

3. På den **Capacity Planner** kalkylblad, ange informationen som krävs. Fyll i alla fält som inringat i rött i följande skärmbild:

   a. I **väljer ditt scenario**, Välj **Hyper-V till Azure** eller **VMware/fysisk till Azure**.

   b. I **genomsnittliga dagliga dataändringshastighet (%)**, ange den information som du har samlat in med hjälp av den [kapacitetsplanering för Hyper-V verktyget](site-recovery-capacity-planning-for-hyper-v-replication.md) eller [Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).

   c. Den **komprimering** inställningen används inte när du replikerar virtuella Hyper-V-datorer till Azure. Använd en tredje parts-installation, till exempel Riverbed för komprimering.

   d. I **kvarhållning i dagar**, ange hur lång tid att behålla replikerna dagarna.

   e. I **antalet timmar under där inledande replikering för batch med virtuella datorer bör slutföras** och **antalet virtuella datorer per batch för inledande replikering**, ange inställningar som används för att beräkna krav för den inledande replikeringen. När Site Recovery har distribuerats kan hela första datamängden har laddats upp.

   ![Indata](./media/site-recovery-capacity-planner/inputs.png)

4. När du har angett värden för källmiljön innehåller visas utdata:

   * **Bandbredd som krävs för deltareplikering (i megabit per sekund)**: Nätverkets bandbredd för deltareplikering beräknas på den genomsnittliga dagliga förändringstakten för data.
   * **Bandbredd som krävs för inledande replikering (i megabit per sekund)**: Nätverkets bandbredd för inledande replikering beräknas på den inledande replikeringen värdena som du anger.
   * **Lagring som krävs (i GB)**: Totalt antal Azure storage krävs.
   * **Totalt antal IOPS med standardlagring**: Hur många beräknas baserat på 8 kB IOPS enhetens storlek på totalt antal standard storage-konton. Hur många beräknas baserat på alla diskar för käll-VM och daglig dataändringshastighet för snabb Planner. För detaljerad Planner antalet beräknas baserat på det totala antalet virtuella datorer som är mappade till standard virtuella Azure-datorer och dataändringshastighet på de virtuella datorerna.
   * **Antal Standard storage-konton som krävs för**: Totalt antal standard storage-konton som krävs för att skydda de virtuella datorerna. Ett standardlagringskonto kan innehålla upp till 20 000 IOPS i alla virtuella datorer i standardlagring. Finns stöd för högst 500 IOPS per disk.
   * **Antal Blob-diskar som måste**: Antalet diskar som skapas på Azure storage.
   * **Antal premium-konton som krävs för**: Det totala antalet premium storage-konton som behövs för att skydda virtuella datorer. En källa för virtuell dator med hög IOPS (större än 20 000) måste ett premium storage-konto. Ett premium storage-konto kan innehålla upp till 80 000 IOPS.
   * **Totalt antal IOPS på Premium Storage**: Hur många beräknas baserat på 256 kB IOPS enhetens storlek på de totala premium storage-kontona. Hur många beräknas baserat på alla diskar för käll-VM och daglig dataändringshastighet för snabb Planner. För detaljerad Planner antalet beräknas baserat på det totala antalet virtuella datorer som är mappade till premium virtuella Azure-datorer (DS- och GS-serien) och dataändringshastighet på de virtuella datorerna.
   * **Antalet Konfigurationsservrar krävs**: Visar hur många konfigurationsservrar krävs för distributionen.
   * **Antal ytterligare Processervrar som krävs för**: Visar huruvida ytterligare processervrar är krävs, förutom den processerver som körs på konfigurationsservern som standard.
   * **100% ytterligare lagringsutrymme på källan**: Visar om ytterligare lagringsutrymme krävs på källplatsen.

      ![Resultat](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Kör detaljerad Planeringsverktyget

1. Ladda ned och öppna [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Du måste köra makron. När du uppmanas göra val för att aktivera redigering och innehåll.

2. I **Välj en typ av planner**väljer **detaljerad Planner** i listrutan.

   ![Guide för att komma igång](./media/site-recovery-capacity-planner/getting-started-2.png)

3. På den **arbetsbelastning kriteriet** kalkylblad, ange informationen som krävs. Du måste fylla i de markerade fälten.

   a. I **processorkärnor**, ange det totala antalet kärnor på en källserver.

   b. I **minnesallokering (i MB)**, ange RAM-storleken på en källserver.

   c. I **antalet nätverkskort**, ange antalet nätverkskort på en källserver.

   d. I **totalt lagringsutrymme (i GB)**, ange den totala storleken på VM-lagring. Om källservern har tre diskar med 500 GB, är totala storlek 1 500 GB.

   e. I **antalet diskar som är anslutna**, ange det totala antalet diskar på en källserver.

   f. I **Disk kapacitetsanvändning (%)**, ange den genomsnittliga användningen.

   g. I **daglig dataändringshastighet för (%)**, ange den dagliga datavolymen på ändra frekvensen för en källserver.

   h. I **mappning Azure VM-storlek**, ange den Virtuella Azure-storlek som du vill mappa. Om du inte vill göra detta manuellt, Välj **Compute virtuella IaaS-datorer**. Om du ange en manuell inställning och välj sedan **Compute virtuella IaaS-datorer**, manuell inställning kan skrivas över. Compute-processen identifierar automatiskt den bästa matchningen på Azure VM-storlek.

   ![Arbetsbelastningen kriteriet kalkylblad](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Om du väljer **Compute virtuella IaaS-datorer**, här är den gör:

   * Verifierar obligatorisk indata.
   * Beräknar IOPS och föreslår den bästa Azure VM storlek matchningen för varje virtuell dator som är berättigade för replikering till Azure. Om en lämplig storlek som virtuell Azure-dator inte kan identifieras, visas ett fel. Till exempel visas antalet diskar som är anslutna är 65, ett fel eftersom den högsta storleken för en Azure VM är 64.
   * Föreslår ett lagringskonto som kan användas för en Azure-dator.
   * Beräknar det totala antalet lagringskonton av standardtyp och premium storage-konton som krävs för arbetsbelastningen. Rulla ned för att visa Azure lagringstyp och storage-konto som kan användas för en källserver.
   * Slutför och sorterar resten av tabellen baserat på de nödvändiga lagringstyp (standard eller premium) tilldelat för en virtuell dator och antalet diskar som är anslutna. För alla virtuella datorer som uppfyller kraven för Azure, kolumnen **är VM kvalificerade?** visar **Ja**. Om en virtuell dator inte kan säkerhetskopieras till Azure, visas ett fel.

Kolumner AA-AE är resultatet och ange information för varje virtuell dator.

![Utdatakolumner AA-AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exempel
Till exempel för sex virtuella datorer med de värden som visas i tabellen, verktyget beräknar och tilldelar Azure storage-krav och bästa Azure VM-matchningen.

![Arbetsbelastningen kriteriet tilldelningar](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Tänk på följande i de exempel på utdata:

  * Den första kolumnen är en verifiering kolumn för virtuella datorer, diskar och omsättning.
  * Två lagringskonton av standardtyp och ett premium storage-konto krävs för fem virtuella datorer.
  * VM3 inte uppfyller kraven för skydd eftersom en eller flera diskar är mer än 1 TB.
  * VM1 och VM2 kan använda det första standard storage-kontot
  * VM4 kan använda andra standardlagringskontot.
  * VM5 och VM6 måste ett premium storage-konto och både kan använda ett enda konto.

    > [!NOTE]
    > IOPS på standard och premium-lagring beräknas på VM-nivå och inte på disknivå. En standard virtuell dator kan hantera upp till 500 IOPS per disk. Om IOPS för en disk som är större än 500, måste premiumlagring. Om IOPS för en disk som är mer än 500 men IOPS för totalt antal VM-diskarna som är inom de virtuella Azure-datorn gränserna som är support, hämtar planeringsverktyget en standard virtuell dator och inte DS eller GS-serien. (De Virtuella Azure-gränserna är VM-storlek, antalet diskar, antal nätverkskort, CPU och minne.) Du måste manuellt uppdatera cellen storlek på Azure-mappning med den lämpliga DS eller GS-serien VM.


När all information har angetts, Välj **skicka data till verktyget Distributionshanteraren** att öppna Capacity Planner. Arbetsbelastningar är markerade för att visa om de är berättigade till skydd.

### <a name="submit-data-in-capacity-planner"></a>Skicka data i Capacity Planner
1. När du öppnar den **Capacity Planner** kalkylbladet fylls baserat på de inställningar som du har angett. Ordet ”arbetsbelastning” visas i den **Infra indata källa** cell, så att indata är den **arbetsbelastning kriteriet** kalkylblad.

2. Om du vill göra ändringar, måste du ändra den **arbetsbelastning kriteriet** kalkylblad. Välj sedan **skicka data till verktyget Distributionshanteraren** igen.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du kör](site-recovery-capacity-planning-for-hyper-v-replication.md) verktyget för kapacitetsplanering.
