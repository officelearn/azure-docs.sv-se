---
title: Skapa en utvärdering med Azure Migrate Server utvärdering | Microsoft Docs
description: Beskriver hur du skapar en utvärdering med verktyget Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: cffde2a677650387dffd19733e082ff7002ccb55
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229107"
---
# <a name="create-an-assessment"></a>Skapa en utvärdering

Den här artikeln beskriver hur du skapar en utvärdering för lokala virtuella VMware-datorer eller virtuella Hyper-V-datorer med Azure Migrate: Server utvärdering.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben ger Azure-verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV). 

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](how-to-add-tool-first-time.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt ser du till att du har [lagt till](how-to-assess.md) Azure Migrate: Verktyget Server bedömning.
- Om du vill skapa en utvärdering måste du konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md) eller [Hyper-V](how-to-set-up-appliance-hyper-v.md). Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering. [Läs mer](migrate-appliance.md).


## <a name="assessment-overview"></a>Översikt över utvärdering
Det finns två typer av utvärderingar som du kan skapa med hjälp av Azure Migrate: Server utvärdering.

**Beskrivningar** | **Detaljer** | **Data**
--- | --- | ---
**Prestanda-baserade** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: Baserat på CPU-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)** : Baserat på IOPS och data flöde för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad VM-storlek**: Baserat på den lokala virtuella dator storleken<br/><br> **Rekommenderad disk typ**: Baserat på inställningen för lagrings typ som du väljer för utvärderingen.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.

## <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Gå igenom [metod tipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. På fliken **servrar** i **Azure Migrate: Panelen Server** bedömning klickar du på **utvärdera**.

    ![Utvärdera](./media/how-to-create-assessment/assess.png)

2. I **utvärdera servrar**anger du ett namn för utvärderingen.
3. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Bedömnings egenskaper](./media/how-to-create-assessment//view-all.png)

3. I **Välj eller skapa en grupp**väljer du **Skapa ny**och anger ett grupp namn. En grupp samlar in en eller flera virtuella datorer för utvärdering.
4. I **Lägg till datorer i gruppen**väljer du de virtuella datorer som ska läggas till i gruppen.
5. Klicka på **Skapa utvärdering** för att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/how-to-create-assessment//assessment-create.png)

6. När utvärderingen har skapats kan du Visa den på **servrar** > **Azure Migrate: Utvärderingar**av Server bedömning. > 
7. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-assessment"></a>Granska en utvärdering

En utvärdering beskriver:

- **Azure-beredskap**: Om de virtuella datorerna är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: Beräknad månatlig beräknings-och lagrings kostnad för att köra de virtuella datorerna i Azure.
- **Kostnads uppskattning för månatlig lagring**: Uppskattade kostnader för disk lagring efter migrering.

### <a name="view-an-assessment"></a>Visa en utvärdering

1. I **mål** >  **Server**för migrering klickar  du på utvärderingar i **Azure Migrate: Server utvärdering**.
2. I **bedömningar**klickar du på en utvärdering för att öppna den.

    ![Utvärderings Sammanfattning](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure readiness

1. I **Azure-beredskap**kontrollerar du om de virtuella datorerna är klara för migrering till Azure.
2. Granska VM-statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnads uppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: Visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: Visar problem och Rekommenderad reparation.
    - **Beredskap okänd**: Används när Azure Migrate inte kan utvärdera beredskap på grund av problem med data tillgänglighet.

2. Klicka på en status för **Azure-beredskap** . Du kan visa information om VM-beredskap och öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.



### <a name="review-cost-details"></a>Granska kostnads information

I den här vyn visas den beräknade beräknings-och lagrings kostnaden för virtuella datorer som körs i Azure.

1. Granska kostnader för beräkning och lagring per månad. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen.

    - Kostnads uppskattningar baseras på storleks rekommendationerna för en dator och dess diskar och egenskaper.
    - Uppskattade månatliga kostnader för beräkning och lagring visas.
    - Kostnads uppskattningen är att köra lokala virtuella datorer som virtuella IaaS-datorer. Azure Migrate Server-utvärderingen beaktar inte PaaS-eller SaaS-kostnader.

2. Du kan granska månads beräkningarna för lagrings kostnader. Den här vyn visar aggregerade lagrings kostnader för den utvärderade gruppen och delas upp över olika typer av lagrings diskar.
3. Du kan öka detalj nivån för att se information om vissa virtuella datorer.


### <a name="review-confidence-rating"></a>Granska säkerhetsomdöme

När du kör prestandabaserade utvärderingar tilldelas utvärderingen en förtroende nivå.

![Säkerhetsomdöme](./media/how-to-create-assessment/confidence-rating.png)

- En klassificering från 1 – stjärna (lägsta) till 5-stjärnor (högst) tilldelas.
- Förtroende omdömet hjälper dig att beräkna tillförlitligheten för de storleks rekommendationer som tillhandahålls av utvärderingen.
- Förtroende omdömet baseras på tillgängligheten för data punkter som behövs för att beräkna utvärderingen.

Tillförlitlighets klassificeringar för en utvärdering är följande.

**Tillgänglighet för data punkt** | **Säkerhetsomdöme**
--- | ---
0 %–20 % | 1 stjärna
21 %–40 % | 2 stjärnor
41 %–60 % | 3 stjärnor
61 %–80 % | 4 stjärnor
81 %–100 % | 5 stjärnor




## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder [beroende mappning](how-to-create-group-machine-dependencies.md) för att skapa grupper med hög exakthet.
- [Läs mer](concepts-assessment-calculation.md) om hur utvärderingar beräknas.
