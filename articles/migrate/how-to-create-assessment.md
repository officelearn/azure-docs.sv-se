---
title: Skapa en Azure VM-utvärdering med Azure Migrate Server utvärdering | Microsoft Docs
description: Beskriver hur du skapar en Azure VM-utvärdering med verktyget Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 534619ace09b4e11934062a591adf8d9ab6f77ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500809"
---
# <a name="create-an-azure-vm-assessment"></a>Skapa en Azure VM-utvärdering

Den här artikeln beskriver hur du skapar en Azure VM-utvärdering för lokala virtuella VMware-datorer eller virtuella Hyper-V-datorer med Azure Migrate: Server utvärdering.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben ger Azure-verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV). 

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat](./create-manage-projects.md) ett Azure Migrate-projekt.
- Om du redan har skapat ett projekt kontrollerar du att du har [lagt till](how-to-assess.md) Azure Migrate: Server utvärderings verktyget.
- Om du vill skapa en utvärdering måste du konfigurera en Azure Migrate-enhet för [VMware](how-to-set-up-appliance-vmware.md) eller [Hyper-V](how-to-set-up-appliance-hyper-v.md). Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering. [Läs mer](migrate-appliance.md).


## <a name="azure-vm-assessment-overview"></a>Översikt över Azure VM-utvärdering
Det finns två typer av storleks kriterier som du kan använda för att skapa en Azure VM-utvärdering med Azure Migrate: Server bedömning.

**Utvärdering** | **Information** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar baserade på insamlade prestanda data | **Rekommenderad VM-storlek**: baserat på processor-och minnes användnings data.<br/><br/> **Rekommenderad disktyp (standard-eller Premium-hanterad disk)**: baserat på IOPS och data flödet för lokala diskar.
**Som lokalt** | Utvärderingar baserade på lokal storlek. | **Rekommenderad VM-storlek**: baserat på den lokala virtuella dator storleken<br/><br> **Rekommenderad disktyp**: baserat på den inställning för lagrings typ som du väljer för utvärderingen.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.

## <a name="run-an-assessment"></a>Köra en utvärdering

Kör en utvärdering på följande sätt:

1. Granska [metodtipsen](best-practices-assessment.md) för att skapa utvärderingar.
2. Klicka på **utvärdera** i panelen **Azure Migrate: Server bedömning** på fliken **servrar** .

    ![Skärm bild som visar Azure Migrate servrar med utvärdering valt under utvärderings verktyg.](./media/how-to-create-assessment/assess.png)

3. I **utvärdera servrar** väljer du bedömnings typ som "Azure VM", väljer identifierings källa och anger bedömnings namnet.

    ![Grunder i utvärderingar](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Klicka på **Visa alla** för att granska utvärderingsegenskaperna.

    ![Utvärderingsegenskaper](./media/how-to-create-assessment//view-all.png)

5. Klicka på **Nästa** och **Välj datorer att utvärdera**. I **Välj eller skapa en grupp** väljer du **Skapa ny** och anger ett gruppnamn. En grupp samlar en eller flera virtuella datorer för utvärdering.
6. I **Lägg till datorer i gruppen** väljer du de virtuella datorer som ska läggas till i gruppen.
7. Klicka på **Nästa** för att **Granska och skapa utvärdering** och granska utvärderingsinformationen.
8. Klicka på **Skapa utvärdering** för att skapa gruppen och kör utvärderingen.

    ![Skapa en utvärdering](./media/how-to-create-assessment//assessment-create.png)

9. När utvärderingen har skapats kan du se den i **Servrar** > **Azure Migrate: Serverutvärdering** > **Utvärderingar**.
10. Klicka på **Exportera utvärdering** för att ladda ned den som en Excel-fil.



## <a name="review-an-azure-vm-assessment"></a>Granska en Azure VM-utvärdering

En Azure VM-utvärdering beskriver:

- **Azure-beredskap**: om virtuella datorer är lämpliga för migrering till Azure.
- **Månads kostnads uppskattning**: beräknad kostnad för beräkning och lagring för de virtuella datorerna i Azure.
- **Uppskattad månatlig lagringskostnad**: Uppskattade kostnader för disklagring efter migreringen.

### <a name="view-an-azure-vm-assessment"></a>Visa en utvärdering av Azure VM

1. I **mål**  >   **Server** för migrering klickar du på **utvärderingar** i **Azure Migrate: Server bedömning**.
2. I **bedömningar** klickar du på en utvärdering för att öppna den.

    ![Sammanfattning av utvärdering](./media/how-to-create-assessment/assessment-summary.png)

### <a name="review-azure-readiness"></a>Granska Azure-beredskap

1. I **Azure-beredskap** kontrollerar du om de virtuella datorerna är klara för migrering till Azure.
2. Granska VM-statusen:
    - **Redo för Azure**: Azure Migrate rekommenderar en VM-storlek och kostnadsuppskattning för virtuella datorer i utvärderingen.
    - **Klar med villkor**: visar problem och Rekommenderad reparation.
    - **Inte redo för Azure**: visar problem och förslag på åtgärder.
    - **Beredskap okänd**: används när Azure Migrate inte kan utvärdera beredskap på grund av problem med data tillgänglighet.

3. Klicka på en status för **Azure-beredskap** . Du kan visa information om VM-beredskap och öka detalj nivån för att se information om virtuella datorer, inklusive beräknings-, lagrings-och nätverks inställningar.



### <a name="review-cost-details"></a>Granska kostnadsinformation

Vyn visar uppskattad beräknings- och lagringskostnad för att köra de virtuella datorerna i Azure.

1. Granska kostnader för beräkning och lagring per månad. Kostnaderna sammanställs för alla virtuella datorer i den utvärderade gruppen.

    - Kostnadsuppskattningarna baseras på storleksrekommendationerna för en dator, samt dess diskar och egenskaper.
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