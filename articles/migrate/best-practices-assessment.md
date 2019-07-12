---
title: Bästa praxis för att skapa utvärdering med Azure Migrate Server-utvärdering | Microsoft Docs
description: Innehåller tips för att skapa utvärderingar med Azure Migrate Server-utvärdering.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: d417efd4abf14247af171ea77b479f590e14fe76
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812969"
---
# <a name="best-practices-for-creating-assessments"></a>Metodtips för att skapa utvärderingar

[Azure Migrate](migrate-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV). 

Den här artikeln sammanfattas metodtips när du skapar utvärderingar med hjälp av verktyget Azure Migrate Server-utvärdering. 

## <a name="about-assessments"></a>Om utvärderingar

Utvärderingar som du skapar med Azure Migrate Server-utvärdering är en point-in-time-ögonblicksbild av data. Det finns två typer av utvärderingar i Azure Migrate.

**Utvärdering av typen** | **Detaljer** | **Data**
--- | --- | ---
**Prestandabaserad** | Utvärderingar som gör rekommendationer baserat på insamlade prestandadata | Rekommendation för virtuella datorer-storlek baserat på användningsdata för CPU och minne.<br/><br/> Rekommendation för typ av disk (standard eller premium managed disks) baseras på IOPS och dataflöden för lokala diskar.
**Som – lokalt** | Utvärderingar som inte använder prestandadata för att göra rekommendationer. | VM-storlek rekommendationen är baserad på lokala VM-storlek<br/><br> Rekommenderade disktypen baseras på vad du väljer i lagringstypen för utvärderingen.

### <a name="example"></a>Exempel
Till exempel om du har en lokal virtuell dator med fyra kärnor på 20% utnyttjande och 8 GB minne med 10% utnyttjande är utvärderingar följande:

- **Prestandabaserad utvärdering**:
    - Rekommenderar kärnor och minne baserat på core (0,8 kärnor) och minnesanvändning (0,8 GB).
    - Utvärderingen gäller en standard-komfortfaktor på 30%.
    - Rekommendation för virtuella datorer: ~1.4 kärnor (0,8 x1.3) och ~1.4 GB minne.
- **Som-är (lokalt) utvärdering**:
    -  Rekommenderar att en virtuell dator med fyra kärnor; 8 GB minne.

## <a name="best-practices-for-creating-assessments"></a>Metodtips för att skapa utvärderingar

Azure Migrate-installation kontinuerligt Profilerar din lokala miljö och skickar metadata-och prestandadata till Azure. Följ dessa bästa metoder för att skapa utvärderingar:

- **Skapa som – är utvärderingar**: Du kan skapa som-utvärderingar är omedelbart efter identifieringen.
- **Skapa prestandabaserad utvärdering**: När du har installerat identifiering, rekommenderar vi att du väntar minst en dag innan du kör en utvärdering av prestandabaserad:
    - Det tar tid för att samla in prestandadata. Att ha väntat minst en dag ser till att det inte finns tillräckligt många datapunkter för prestanda innan du kör utvärderingen.
    - Installationen för prestandadata, samlar in data i realtid punkter var 20: e sekund för varje prestandamått och samlar in dem upp till en enskild datapunkt på fem minuter. Enheten som skickar fem minuter datapunkternas till Azure varje timme för utvärdering av beräkning.  
- **Hämta senaste data**: Utvärderingar uppdateras inte automatiskt med den senaste informationen. Om du vill uppdatera en utvärdering med den senaste informationen, måste du köra den igen. 
- **Kontrollera varaktigheter matchar**: När du kör prestandabaserad utvärderingar, se till att din profil din miljö för utvärdering av varaktigheten. Om du skapar en utvärdering med en varaktigheten till en vecka, måste du vänta minst en vecka när du har startat identifieringen, för alla datapunkter ska samlas in. Om du inte kommer utvärderingen inte fem stjärnor. 
- **Undviker att missa datapunkter**: Följande problem kan resultera i saknar datapunkter i en prestandabaserad utvärdering:
    - Virtuella datorer är avstängda under utvärderingen och prestandadata samlas inte in. 
    - Om du skapar virtuella datorer under månaden som baseras prestandahistorik. data för de virtuella datorerna ska vara mindre än en månad. 
    - Utvärderingen har skapats direkt efter identifiering eller utvärdering tiden matchar inte data collection-tid för prestanda.

## <a name="best-practices-for-confidence-ratings"></a>Bästa praxis för säker betyg

När du kör prestandabaserad utvärderingar, tilldelas en konfidensnivå omdöme intervallet 1 stjärna (lägsta) (högst) för 5 stjärnor utvärderingen. Att använda säker klassificeringarna effektivt:
- Azure Migrate Server-utvärdering måste användningsdata för virtuell dator CPU/minne och disk-IOPS/dataflödet data.
- För varje nätverkskort som är anslutet till en virtuell dator, måste inkommande och utgående data i Azure Migrate.
- Om användningsdata inte är tillgängliga i vCenter Server, kanske storleksrekommendationen från Azure Migrate inte är tillförlitlig. 

Beroende på procentandelen datapunkter som är tillgängliga sammanfattas omdömen förtroende för en utvärdering i tabellen nedan.

   **Tillgänglighet för datapunkt** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor

- Om du får ett säkerhetsomdöme för en utvärdering är lägre än fem stjärnor, vänta minst en dag och sedan beräkna om utvärderingen.
- Ett låga omdömet innebär att Bedömningsrekommendationer inte kanske är tillförlitlig. I det här fallet rekommenderar vi att du ändrar egenskaperna för utvärdering ska användas som – är en lokal utvärdering.

## <a name="common-assessment-issues"></a>Vanliga utvärderingsproblem

Här är att lösa några vanliga problem i miljön som påverkar utvärderingar.

###  <a name="out-of-sync-assessments"></a>Out-för-sync utvärderingar

Om du lägger till eller ta bort datorer från en grupp när du har skapat en utvärdering utvärderingen som du har skapat kommer att markeras **ut av synkronisering**. Kör utvärderingen igen (**beräkna om**) så att ändringar grupp.

### <a name="outdated-assessments"></a>Inaktuella utvärderingar

Om det finns ändringar av lokala virtuella datorer som tillhör en grupp som har utvärderats, utvärderingen har markerats **inaktuella**. Kör utvärderingen igen för att återspegla ändringarna.

### <a name="missing-data-points"></a>Saknade datapunkter

En utvärdering kanske inte har alla datapunkter för en rad orsaker:

- Virtuella datorer kan stängas av under utvärderingen och prestandadata samlas inte in. 
- Virtuella datorer kan skapas under månaden historik baseras på vilken prestanda kan alltså sina prestandadata är mindre än en månad. 
- Utvärderingen har skapats direkt efter identifiering. Du behöver vänta den angivna mängden tid innan du kör en utvärdering för att samla in prestandadata för en angiven tidsperiod. Om du vill utvärdera prestandadata för en vecka, måste du vänta en vecka efter identifieringen. Om du inte utvärderingen kommer inte få fem stjärnor. 


## <a name="next-steps"></a>Nästa steg

- [Lär dig](concepts-assessment-calculation.md) hur utvärderingar beräknas.
- [Lär dig](how-to-modify-assessment.md) hur du anpassar en utvärdering.
