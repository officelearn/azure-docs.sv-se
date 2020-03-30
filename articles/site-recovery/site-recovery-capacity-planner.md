---
title: Planera kapacitet för Hyper-V-haveriberedskap med Azure Site Recovery
description: Använd den här artikeln för att uppskatta kapaciteten när du konfigurerar haveriberedskap med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 843d5da26d6791cea880e5dfb654fe27b74f5d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73936052"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planera kapacitet för haveriberedskap för virtuella datorer med flera virtuella datorer 

[Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) för Hyper-V till Azure-distribution innehåller följande:

* Bedömning av vm-berättigande, baserat på antalet diskar, diskstorlek, IOPS, omsättning och några VM-egenskaper
* Nätverkets bandbreddsbehov kontra utvärdering av återställningspunktmål
* Krav på infrastruktur för Azure
* Krav på lokal infrastruktur
* Inledande vägledning till batchreplikering
* Beräknad total kostnad för katastrofåterställning till Azure


Azure Site Recovery Capacity Planner hjälper dig att fastställa dina kapacitetskrav när du replikerar virtuella hyper-virtuella datorer med Azure Site Recovery.

Använd Site Recovery Capacity Planner för att analysera källmiljön och arbetsbelastningarna. Det hjälper dig att uppskatta bandbreddsbehov, de serverresurser du behöver för källplatsen och de resurser (till exempel virtuella datorer och lagring) som du behöver på målplatsen.

Du kan köra verktyget i två lägen:

* **Snabb planering**: Tillhandahåller nätverks- och serverprojektioner baserat på ett genomsnittligt antal virtuella datorer, diskar, lagring och ändringshastighet.
* **Detaljerad planering**: Innehåller information om varje arbetsbelastning på VM-nivå. Analysera VM-kompatibilitet och hämta nätverks- och serverprojektioner.

## <a name="before-you-start"></a>Innan du börjar

* Samla in information om din miljö, inklusive virtuella datorer, diskar per virtuell dator, lagring per disk.
* Identifiera din dagliga förändringsfrekvens (churn)för replikerade data. Hämta [hyper-V-kapacitetsplaneringsverktyget](https://www.microsoft.com/download/details.aspx?id=39057) för att få ändringshastigheten. [Läs mer](site-recovery-capacity-planning-for-hyper-v-replication.md) om det här verktyget. Vi rekommenderar att du kör det här verktyget under en vecka för att fånga genomsnitt.


## <a name="run-the-quick-planner"></a>Kör snabbplaneraren
1. Ladda ner och öppna [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Du måste köra makron. När du uppmanas att göra val för att aktivera redigering och innehåll.

2. Välj **Snabbplanerare**i listrutan **Välj en planerare.**

   ![Komma igång](./media/site-recovery-capacity-planner/getting-started.png)

3. Ange den information som krävs i kalkylbladet **Kapacitetsplanerare.** Fyll i alla fält inringade i rött i följande skärmdump:

   a. I **Välj scenario**väljer du **Hyper-V till Azure** eller **VMware/Physical till Azure**.

   b. I **Genomsnittlig daglig dataändringshastighet (%)** anger du den information du samlar in med hjälp av [hyper-V-kapacitetsplaneringsverktyget](site-recovery-capacity-planning-for-hyper-v-replication.md) eller [site recovery deployment planner](./site-recovery-deployment-planner.md).

   c. **Komprimeringsinställningen** används inte när du replikerar virtuella hyper-v-datorer till Azure. För komprimering, använd en apparat från tredje part, till exempel Riverbed.

   d. I **Kvarhållning i dagar**anger du i dagar hur länge replikerna ska behållas.

   e. I **Antal timmar då den första replikeringen för batchen virtuella datorer ska slutföras** och Antal virtuella datorer per inledande **replikeringsbatch**anger du inställningar som används för att beräkna initiala replikeringskrav. När Site Recovery distribueras överförs hela den ursprungliga datauppsättningen.

   ![Indata](./media/site-recovery-capacity-planner/inputs.png)

4. När du har angett värdena för källmiljön innehåller utdata:

   * **Bandbredd som krävs för deltareplikering (i Megabit/sek):** Nätverksbandbredd för deltareplikering beräknas på den genomsnittliga dagliga dataförändringshastigheten.
   * **Bandbredd som krävs för inledande replikering (i Megabit/sek):** Nätverksbandbredd för inledande replikering beräknas på de ursprungliga replikeringsvärdena som du anger.
   * **Lagring krävs (i GB):** Den totala Azure-lagring som krävs.
   * **Totalt IOPS på standardlagring:** Antalet beräknas baserat på storleken på 8K IOPS-enheten på de totala standardlagringskontona. För Snabbplaneraren beräknas numret baserat på alla vm-källdiskar och den dagliga dataändringshastigheten. För den detaljerade planeraren beräknas antalet baserat på det totala antalet virtuella datorer som mappas till vanliga virtuella Azure-datorer och dataändringshastigheten på dessa virtuella datorer.
   * **Antal standardlagringskonton som krävs:** Det totala antalet standardlagringskonton som behövs för att skydda de virtuella datorerna. Ett standardlagringskonto kan innehålla upp till 20 000 IOPS i alla virtuella datorer i standardlagring. Maximalt 500 IOPS stöds per disk.
   * **Antal Blob-diskar som krävs:** Antalet diskar som skapas på Azure-lagring.
   * **Antal premiumkonton som krävs:** Det totala antalet premiumlagringskonton som behövs för att skydda de virtuella datorerna. En käll-VM med hög IOPS (större än 20 000) behöver ett premiumlagringskonto. Ett premiumlagringskonto kan innehålla upp till 80 000 IOPS.
   * **Totalt IOPS på Premium Storage:** Antalet beräknas baserat på 256K IOPS-enhetsstorleken på de totala premiumlagringskontona. För Snabbplaneraren beräknas numret baserat på alla vm-källdiskar och den dagliga dataändringshastigheten. För den detaljerade planeraren beräknas antalet baserat på det totala antalet virtuella datorer som mappas till premium-virtuella Azure-datorer (DS- och GS-serie) och dataändringshastigheten på dessa virtuella datorer.
   * **Antal konfigurationsservrar som krävs:** Visar hur många konfigurationsservrar som krävs för distributionen.
   * **Antal ytterligare processservrar som krävs:** Visar om ytterligare processservrar krävs, utöver den processserver som körs på konfigurationsservern som standard.
   * **100 % extra lagringsutrymme på källan**: Visar om ytterligare lagringsutrymme krävs på källplatsen.

      ![Resultat](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Kör den detaljerade planeraren

1. Ladda ner och öppna [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Du måste köra makron. När du uppmanas att göra val för att aktivera redigering och innehåll.

2. Välj Detaljerad planerare i listrutan i **Välj en planeraretyp.** **Detailed Planner**

   ![Guide för att komma igång](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Ange den information som krävs i kalkylbladet **Arbetsbelastningsbehörighet.** Du måste fylla i alla markerade fält.

   a. I **Processorkärnor**anger du det totala antalet kärnor på en källserver.

   b. I **Minnesallokering (i MBs)** anger du RAM-storleken på en källserver.

   c. I **Antal nätverkskort**anger du antalet nätverkskort på en källserver.

   d. I **Total lagring (i GB)** anger du den totala storleken på vm-lagringen. Om källservern till exempel har tre diskar med 500 GB vardera, är den totala lagringsstorleken 1 500 GB.

   e. Ange det totala antalet diskar på en källserver i **Antal anslutna**diskar.

   f. Ange genomsnittligt utnyttjande av **diskkapacitet (%)**

   g. I **Daglig dataändringshastighet (%)** anger du den dagliga dataändringshastigheten för en källserver.

   h. I **Mappa Azure VM-storlek**anger du den Azure VM-storlek som du vill mappa. Om du inte vill göra detta manuellt väljer du **Virtuella IaaS-datorer**för Beräkning . Om du anger en manuell inställning och sedan väljer **Virtuella IaaS-datorer**kan den manuella inställningen skrivas över. Beräkningsprocessen identifierar automatiskt den bästa matchningen på Azure VM-storlek.

   ![Kalkylblad för arbetsbelastningsbehörighet](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Om du väljer **Virtuella IAAS-datorer för Compute IaaS**gör du så här:

   * Validerar obligatoriska indata.
   * Beräknar IOPS och föreslår den bästa Azure VM-storleksmatchningen för varje virtuell dator som är berättigad till replikering till Azure. Om en lämplig storlek Azure VM inte kan identifieras, visas ett fel. Om till exempel antalet anslutna diskar är 65 visas ett fel eftersom den högsta storleken för en Azure VM är 64.
   * Föreslår ett lagringskonto som kan användas för en Virtuell Azure.Suggests a storage account that can be used for an Azure VM.
   * Beräknar det totala antalet standardlagringskonton och premiumlagringskonton som krävs för arbetsbelastningen. Bläddra nedåt för att visa Azure-lagringstypen och lagringskontot som kan användas för en källserver.
   * Slutför och sorterar resten av tabellen baserat på den lagringstyp som krävs (standard eller premium) som tilldelats för en virtuell dator och antalet anslutna diskar. För alla virtuella datorer som uppfyller kraven för Azure visas **virtuell**dator kvalificerad för virtuella **datorer?** Om en virtuell dator inte kan säkerhetskopieras till Azure visas ett fel.

Kolumner AA till AE är utdata och ger information för varje virtuell dator.

![Utdatakolumner AA till AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exempel
Som ett exempel, för sex virtuella datorer med de värden som visas i tabellen, beräknar och tilldelar verktyget den bästa Azure VM-matchningen och Azure-lagringskraven.

![Tilldelningar för arbetsbelastningskvalificering](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* I exempelutdata bör du tänka på följande:

  * Den första kolumnen är en valideringskolumn för virtuella datorer, diskar och omsättning.
  * Två standardlagringskonton och ett premiumlagringskonto behövs för fem virtuella datorer.
  * VM3 kvalificerar sig inte för skydd eftersom en eller flera diskar är mer än 1 TB.
  * VM1 och VM2 kan använda det första standardlagringskontot
  * VM4 kan använda det andra standardlagringskontot.
  * VM5 och VM6 behöver ett premiumlagringskonto och båda kan använda ett enda konto.

    > [!NOTE]
    > IOPS på standard- och premiumlagring beräknas på VM-nivå och inte på disknivå. En virtuell standard kan hantera upp till 500 IOPS per disk. Om IOPS för en disk är större än 500 behöver du premiumlagring. Om IOPS för en disk är mer än 500 men IOPS för den totala VM-diskarna ligger inom stödstandarden Azure VM gränser, väljer planeraren en vanlig virtuell dator och inte DS eller GS-serien. (Azure VM-gränserna är VM-storlek, antal diskar, antal kort, CPU och minne.) Du måste uppdatera mappningscellen för mappning av Azure med lämplig virtuell dator för DS- eller GS-serien.


När all information har angetts väljer du **Skicka data till planerareverktyget** för att öppna Kapacitetsplaneraren. Arbetsbelastningar markeras för att visa om de är berättigade till skydd.

### <a name="submit-data-in-capacity-planner"></a>Skicka data i kapacitetsplaneraren
1. När du öppnar kalkylbladet **Kapacitetsplanerare** fylls det i baserat på de inställningar du har angett. Ordet "Arbetsbelastning" visas i **källcellen Infra-indata** för att visa att indata är kalkylbladet **Arbetsbelastningsbehörighet.**

2. Om du vill göra ändringar måste du ändra kalkylbladet **Arbetsbelastningsbekvalificering.** Välj sedan **Skicka data till planeringsverktyget** igen.

   ![Kapacitetsplanerare](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Nästa steg
[Läs om hur du kör](site-recovery-capacity-planning-for-hyper-v-replication.md) kapacitetsplaneringsverktyget.
