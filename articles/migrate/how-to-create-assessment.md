---
title: Skapa en utvärdering med Utvärdering av Azure Migrate Server | Microsoft-dokument
description: Beskriver hur du skapar en utvärdering med verktyget Utvärdering av Azure Migrate Server
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68229107"
---
# <a name="create-an-assessment"></a>Skapa en utvärdering

I den här artikeln beskrivs hur du skapar en utvärdering för lokala virtuella datorer med VMware eller virtuella datorer med Hyper-V med Azure Migrate: Server Assessment.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en centraliserad hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben tillhandahåller Azure-verktyg för utvärdering och migrering samt ISV-erbjudanden (Independent Software Vendor) från tredje part. 

## <a name="before-you-start"></a>Innan du börjar

- Kontrollera att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt kontrollerar du att du har [lagt till](how-to-assess.md) verktyget Azure Migrate: Server Assessment.
- Om du vill skapa en utvärdering måste du konfigurera en Azure Migrate-installation för [VMware](how-to-set-up-appliance-vmware.md) eller [Hyper-V](how-to-set-up-appliance-hyper-v.md). Installationen identifierar lokala datorer och skickar metadata och prestandadata till Azure Migrate: Server Assessment. [Läs mer](migrate-appliance.md).


## <a name="assessment-overview"></a>Utvärderingsöversikt
Det finns två typer av utvärderingar som du kan skapa med Azure Migrate: Server Assessment.

**Utvärdering** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar baserade på insamlade resultatdata | **Rekommenderad VM-storlek:** Baserat på CPU- och minnesanvändningsdata.<br/><br/> **Rekommenderad disktyp (standard eller premiumhanterad disk)**: Baserat på IOPS och dataflöde för de lokala diskarna.
**Som lokalt** | Bedömningar baserade på lokal storlek. | **Rekommenderad VM-storlek:** Baserat på den lokala vm-storleken<br/><br> **Rekommenderad disktyp**: Baserat på den lagringstyp som du väljer för utvärderingen.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.

## <a name="run-an-assessment"></a>Köra en utvärdering

Gör en bedömning på följande sätt:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. Klicka på **Utvärdera**i panelen **Serverutvärdering** på fliken **Servrar.**

    ![Utvärdera](./media/how-to-create-assessment/assess.png)

2. Ange ett namn för utvärderingen i **Bedöma servrar.**
3. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömningsegenskaper](./media/how-to-create-assessment//view-all.png)

3. Välj **Skapa ny**i Markera eller skapa **en grupp**och ange ett gruppnamn. En grupp samlar ihop en eller flera virtuella datorer för bedömning.
4. I **Lägg till datorer i gruppen**väljer du virtuella datorer som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** om du vill skapa gruppen och köra utvärderingen.

    ![Skapa en utvärdering](./media/how-to-create-assessment//assessment-create.png)

6. När utvärderingen har skapats visar du den i **Servrar** > **Azure Migrera: Serverutvärderingsutvärderingar** > **Assessments**.
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-assessment"></a>Granska en bedömning

En bedömning beskriver:

- **Azure-beredskap**: Om virtuella datorer är lämpliga för migrering till Azure.
- **Uppskattning av månadskostnad**: Uppskattade månatliga beräknings- och lagringskostnader för att köra de virtuella datorerna i Azure.
- **Uppskattning av månatliga lagringskostnader**: Uppskattade kostnader för disklagring efter migreringen.

### <a name="view-an-assessment"></a>Visa en bedömning

1. Klicka på **Utvärderingar** i **Azure Migrera: Serverutvärdering**i servrar för **migreringsmål.** >  **Servers**
2. I **Bedömningar**klickar du på en bedömning för att öppna den.

    ![Sammanfattning av bedömning](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap**kontrollerar du om virtuella datorer är redo för migrering till Azure.
2. Granska vm-statusen:
    - **Klar för Azure:** Azure Migrate rekommenderar en vm-storlek och kostnadsuppskattningar för virtuella datorer i utvärderingen.
    - **Redo med villkor**: Visar problem och föreslagen reparation.
    - **Inte redo för Azure**: Visar problem och föreslagen reparation.
    - **Okänd beredskap**: Används när Azure Migrate inte kan bedöma beredskapen på grund av problem med datatillgänglighet.

2. Klicka på en **Azure-beredskapsstatus.** Du kan visa information om vm-beredskap och öka detaljnivån för att se vm-information, inklusive beräknings-, lagrings- och nätverksinställningar.



### <a name="review-cost-details"></a>Granska kostnadsinformation

Den här vyn visar den uppskattade beräknings- och lagringskostnaden för att köra virtuella datorer i Azure.

1. Granska de månatliga beräknings- och lagringskostnaderna. Kostnaderna aggregeras för alla virtuella datorer i den bedömda gruppen.

    - Kostnadsuppskattningar baseras på storleksrekommendationerna för en dator och dess diskar och egenskaper.
    - Beräknade månadskostnader för beräkning och lagring visas.
    - Kostnadsuppskattningen är för att köra de lokala virtuella datorerna som virtuella IaaS-datorer. Azure Migrate Server Assessment tar inte hänsyn till PaaS eller SaaS kostnader.

2. Du kan granska uppskattningar av månatliga lagringskostnader. Den här vyn visar aggregerade lagringskostnader för den bedömda gruppen, uppdelade över olika typer av lagringsdiskar.
3. Du kan öka detaljnivån för att se information om specifika virtuella datorer.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör resultatbaserade utvärderingar tilldelas en konfidensklassificering till utvärderingen.

![Säkerhetsomdöme](./media/how-to-create-assessment/confidence-rating.png)

- Betyg från 1-stjärniga (lägsta) till 5-stjärniga (högsta) delas ut.
- Konfidensklassificeringen hjälper dig att uppskatta tillförlitligheten hos de storleksrekommendationer som tillhandahålls av bedömningen.
- Konfidensklassificeringen baseras på tillgången på datapunkter som behövs för att beräkna bedömningen.

Förtroendebetyg för en bedömning är följande.

**Tillgänglighet för datapunkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor




## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [beroendemappning](how-to-create-group-machine-dependencies.md) för att skapa grupper med högt förtroende.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
