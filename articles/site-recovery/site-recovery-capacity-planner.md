---
title: "Beräkna kapacitet för replikering i Azure | Microsoft Docs"
description: "Använd den här artikeln för att beräkna kapacitet vid replikering med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: nisoneji
ms.openlocfilehash: 840a559a82f3227a865d3c606b2fa321cb6144ab
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Planera kapacitet för att skydda Hyper-V virtuella datorer med Site Recovery

Verktyget Azure Site Recovery Capacity Planner hjälper dig att ta reda på dina kapacitetskrav vid replikering av Hyper-V virtuella datorer med Azure Site Recovery.

Använd Site Recovery Capacity Planner för att analysera din källmiljö och arbetsbelastningar, uppskattning bandbreddsbehov och serverresurser som du behöver för källplatsen och resurser (virtuella datorer och lagring osv), som du behöver på målplatsen.

Du kan köra verktyget på ett par olika lägen:

* **Snabb planera**: kör verktyget i det här läget för att hämta nätverks- och projektioner baserat på ett genomsnittligt antal virtuella datorer, diskar, lagring och förändringstakten.
* **Detaljerad planering**: köra verktyget i det här läget och ger information för varje arbetsbelastning på VM-nivå. Analysera VM-kompatibilitet och får nätverks- och projektioner.

## <a name="before-you-start"></a>Innan du börjar


1. Samla in information om din miljö, inklusive virtuella datorer, diskar per virtuell dator lagringsutrymme per disk.
2. Identifiera dina dagliga (omsättningen) förändringstakten för replikerade data. Gör detta genom att ladda ned den [Hyper-V kapacitetsplanering verktyget](https://www.microsoft.com/download/details.aspx?id=39057) få förändringstakten. [Lär dig mer](site-recovery-capacity-planning-for-hyper-v-replication.md) om det här verktyget. Vi rekommenderar att du kör det här verktyget över en vecka att avbilda medelvärden.
   

## <a name="run-the-quick-planner"></a>Kör snabba Planner
1. Ladda ned och öppna den [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) verktyget. Du behöver köra makron, så du väljer att aktivera redigering och aktivera innehållet när du tillfrågas.
2. I **ange planner** Välj **snabb Planner** i listrutan.

   ![Komma igång](./media/site-recovery-capacity-planner/getting-started.png)
3. I den **Capacity Planner** kalkylblad, ange nödvändig information. Du måste fylla i alla fält inringad i rött i skärmbilden nedan.

   * I **Välj ditt scenario**, Välj **Hyper-V till Azure** eller **VMware/fysiska till Azure**.
   * I **genomsnittliga dagliga data ändras hastighet (%)**, placeras i den information som du samlar in med hjälp av den [Hyper-V kapacitetsplanering verktyget](site-recovery-capacity-planning-for-hyper-v-replication.md) eller [Azure Site Recovery-distribution Planner](./site-recovery-deployment-planner.md).  
   * Den **komprimering** inställningen används inte när replikera virtuella Hyper-V-datorer till Azure. Använd en tredjeparts-installation, till exempel Riverbed för komprimering.
   * I **kvarhållning indata**, ange hur länge replikerna ska behållas, i timmar.
   * I **antalet timmar för inledande replikering för grupp med virtuella datorer bör slutföras** och **antalet virtuella datorer per batch för inledande replikering**, du kan ange inställningar som används för att beräkna krav för inledande replikering.  När Platsåterställningen har distribuerats, ska inledande hela datamängden överföras.

   ![Indata](./media/site-recovery-capacity-planner/inputs.png)
4. När du har publicerat i värden för källmiljön innehåller visas utdata:

   * **Bandbredd som krävs för deltareplikering** (MB per sekund). Nätverkets bandbredd för deltareplikering beräknas på genomsnittliga dagliga förändringstakten för data.
   * **Bandbredd som krävs för inledande replikering** (MB per sekund). Nätverkets bandbredd för inledande replikering beräknas på värden för inledande replikering som du lägger till i.
   * **Lagringsutrymme som krävs (i GB)** är det totala lagringsutrymmet för Azure som krävs.
   * **Totalt antal IOPS på standardlagring konton** beräknas baserat på om 8 K storlek på IOPS på de totala standard storage-kontona.  Antalet beräknas baserat på alla diskar för käll-virtuella datorer och dagliga data ändra frekvensen för snabb Planner. För detaljerad Planner numret beräknas baserat på totala antalet virtuella datorer som är mappade till standard virtuella Azure-datorer och data ändra på dessa virtuella datorer.
   * **Antalet standardlagring konton** innehåller det totala antalet standard storage-konton som krävs för att skydda de virtuella datorerna. Ett standardlagringskonto kan innehålla upp till 20 000 IOPS över alla virtuella datorer i en standardlagring och stöds högst 500 IOPS per disk.
   * **Antal blob-diskar som krävs för** ger antalet diskar som kommer att skapas på Azure-lagring.
   * **Antal premium storage-konton som krävs för** innehåller det totala antalet premium storage-konton som krävs för att skydda de virtuella datorerna. En källa för virtuell dator med hög IOPS (större än 20000) måste ett premiumlagringskonto. Premium-lagringskonto kan innehålla upp till 80000 IOPS.
   * **Totalt antal IOPS på premium-lagring** beräknas baserat på 256 kB IOPS storlek på totalt premium storage-konton.  Antalet beräknas baserat på alla diskar för käll-virtuella datorer och dagliga data ändra frekvensen för snabb Planner. För detaljerad Planner numret beräknas baserat på det totala antalet virtuella datorer som är mappade till premium Azure VM (DS och GS-serien) och data ändra på dessa virtuella datorer.
   * **Antal configuration-servrar krävs** visar hur många configuration-servrar som krävs för distributionen.
   * **Antal ytterligare servrar krävs** visar om ytterligare servrar krävs, förutom som körs på konfigurationsservern som standard.
   * **100% ytterligare lagringsutrymme på käll-** visar om ytterligare lagringsutrymme krävs på källplatsen.

   ![Resultat](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Kör detaljerad Planner

1. Ladda ned och öppna den [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) verktyget. Du behöver köra makron, så du väljer att aktivera redigering och aktivera innehållet när du tillfrågas.
2. I **ange planner**väljer **detaljerad Planner** i listrutan.

   ![Komma igång](./media/site-recovery-capacity-planner/getting-started-2.png)
3. I den **arbetsbelastning kriteriet** kalkylblad, ange nödvändig information. Du måste fylla i de markerade fälten.

   * I **processorkärnor**, ange det totala antalet kärnor på en källserver.
   * I **minnesallokering i MB**, ange storleken för RAM-minne på en källserver.
   * Den **antalet nätverkskort**, ange antalet nätverkskort på en källserver.
   * I **totalt lagringsutrymme (i GB)**, ange den totala storleken på VM-lagring. Om källservern har 3 diskar med 500 GB, är totalt lagringsutrymme 1 500 GB.
   * I **antalet diskar som är anslutna**, ange det totala antalet diskar på en källserver.
   * I **Disk kapacitetsutnyttjande**, ange genomsnittlig användning.
   * I **dagligen ändra hastighet (%)**, ange dagliga data ändra frekvensen för en källserver.
   * I **mappning Azure storlek**, ange den Virtuella Azure-storlek som du vill mappa. Om du inte vill göra detta manuellt, klickar du på **Compute IaaS-VM**. Om du ange en manuell inställning och klicka sedan på Beräkna IaaS-VM, kan skrivas över inställningen manuella eftersom beräkning processen identifierar automatiskt den bästa matchningen på Azure VM-storlek.

   ![Arbetsbelastningen kriteriet](./media/site-recovery-capacity-planner/workload-qualification.png)
4. Om du klickar på **Compute IaaS-VM** här är syfte:

   * Verifierar obligatoriska indata.
   * Beräknar IOPS och ger förslag på den bästa Azure VM aize matchning för varje virtuella datorer som är berättigade för replikering till Azure. Om en lämplig storlek på virtuella Azure-datorn inte går att identifiera ett fel utfärdas. Till exempel om antalet diskar som är ansluten i 65, utfärdas ett fel eftersom högsta storlek Azure VM är 64.
   * Tyder på ett lagringskonto som kan användas för en Azure VM.
   * Beräknar antalet lagringskonton som standard och premium storage-konton som krävs för arbetsbelastning. Rulla ned till vyn Azure storage-typ och lagringskontot som kan användas för en källserver.
   * Slutför och sorterar resten av tabellen baserat på lagring (standard eller premium) kopplade typen för en virtuell dator och antalet diskar som är anslutna. För alla virtuella datorer som uppfyller kraven för Azure, kolumnen **är VM kvalificerade?** visar **Ja**. Om en virtuell dator inte kan säkerhetskopieras till Azure, visas ett fel.

Kolumner AA-AE är resultatet och ange information för varje virtuell dator.

![Arbetsbelastningen kriteriet](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Exempel
Exempel för sex virtuella datorer med de värden som visas i tabellen, verktyget beräknar och tilldelar den bästa Azure VM-matchningen och Azure storage-krav.

![Arbetsbelastningen kriteriet](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Tänk på följande i exempel på utdata:

  * Den första kolumnen är en verifiering kolumn för virtuella datorer, diskar och omsättning.
  * Två standard storage-konton och en premium storage-konto krävs för fem virtuella datorer.
  * VM3 inte uppfyller kraven för skydd, eftersom en eller flera diskar är mer än 1 TB.
  * VM1 och VM2 kan använda det första standardlagringskontot
  * VM4 kan använda andra standardlagringskontot.
  * VM5 och VM6 måste ett premiumlagringskonto och kan båda använda ett konto.

    > [!NOTE]
    > IOPS på standard och premium-lagring beräknas på VM-nivå och inte på disk nivå. En virtuell dator som standard kan hantera upp till 500 IOPS per disk. Om IOPS för en disk som är större än 500, måste premium-lagring. Men om IOPS för en disk som är mer än 500, men IOPS för total VM-diskarna i standard Azure VM stödbegränsningarna (VM-storlek, antalet diskar, antalet nätverkskort, CPU, minne) hämtar sedan planner en standard virtuell dator och inte DS eller GS-serien. Du måste manuellt uppdatera mappning Azure storlek cellen med lämpliga DS eller GS-serien VM.


När allt är på plats klickar du på **skicka data till kapacitetsplaneringsverktyget för** att öppna den **Capacity Planner** arbetsbelastningar som är markerade för att visa om de är berättigade till skydd eller inte.

### <a name="submit-data-in-the-capacity-planner"></a>Skicka data i Capacity Planner
1. När du öppnar den **Capacity Planner** kalkylbladet fylls baserat på de inställningar som du har angett. Ordet ”arbetsbelastning' visas i den **Infra indata källa** cell, som visar att indata är den **arbetsbelastning kriteriet** kalkylblad.
2. Om du vill göra ändringar, måste du ändra den **arbetsbelastning kriteriet** kalkylblad och klickar på **skicka data till kapacitetsplaneringsverktyget för** igen.  

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du kör](site-recovery-capacity-planning-for-hyper-v-replication.md) Kapacitetsplaneringsverktyget för.
