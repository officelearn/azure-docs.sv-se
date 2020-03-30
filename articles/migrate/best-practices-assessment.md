---
title: Metodtips för utvärdering av utvärdering av utvärdering av utvärdering av Azure Migrate Server
description: Tips om hur du skapar utvärderingar med Utvärdering av Azure Migrate Server.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: de6953b6648613595bc9975b17941b3a453a6d60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185985"
---
# <a name="best-practices-for-creating-assessments"></a>Metodtips för att skapa utvärderingar

[Azure Migrate](migrate-overview.md) innehåller ett nav med verktyg som hjälper dig att identifiera, bedöma och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Navet innehåller Azure Migrate-verktyg och ISV-erbjudanden (Independent Software Vendor) från tredje part.

I den här artikeln sammanfattas metodtips när du skapar utvärderingar med verktyget Azure Migrate Server Assessment.

## <a name="about-assessments"></a>Om bedömningar

Utvärderingar som du skapar med Azure Migrate Server Assessment är en ögonblicksbild av data i tid. Det finns två typer av utvärderingar i Azure Migrate.

**Typ av bedömning** | **Detaljer** | **Data**
--- | --- | ---
**Prestationsbaserad** | Bedömningar som gör rekommendationer baserade på insamlade resultatdata | Rekommendationen vm-storlek baseras på CPU- och minnesanvändningsdata.<br/><br/> Disktypsrekommendationen (standard-HDD/SSD eller premiumhanterade diskar) baseras på IOPS och dataflöde för de lokala diskarna.
**Som är lokalt** | Utvärderingar som inte använder prestandadata för att ge rekommendationer. | Vm-storleksrekommendationen baseras på den lokala vm-storleken<br/><br> Den rekommenderade disktypen baseras på vad du väljer i lagringstypinställningen för utvärderingen.

### <a name="example"></a>Exempel
Om du till exempel har en lokal virtuell dator med fyra kärnor vid 20 % utnyttjande och minne på 8 GB med 10 % utnyttjande, blir bedömningarna följande:

- **Prestationsbaserad bedömning:**
    - Identifierar effektiva kärnor och minne baserat på kärnanvändning (4 x 0,20 = 0,8) och minne (8 GB x 0,10 = 0,8) användning.
    - Tillämpar den komfortfaktor som anges i bedömningsegenskaper (låt oss säga 1,3 x) för att få de värden som ska användas för storlek. 
    - Rekommenderar den närmaste vm-storleken i Azure som kan stödja ~1,04 kärnor (0,8 x 1,3) och ~1,04 GB (0,8 x 1,3) minne.

- **Bedömning i samma dag (som lokalt):**
    -  Rekommenderar en virtuell dator med fyra kärnor; 8 GB minne.

## <a name="best-practices-for-creating-assessments"></a>Metodtips för att skapa utvärderingar

Azure Migrate-enheten profilerar kontinuerligt din lokala miljö och skickar metadata och prestandadata till Azure. Följ dessa metodtips för utvärderingar av servrar som upptäckts med hjälp av en installation:

- **Skapa som-är-utvärderingar:** Du kan skapa bedömningar som är direkt när dina datorer visas i Azure Migrate-portalen.
- **Skapa en resultatbaserad bedömning**: När du har konfigurerat identifiering rekommenderar vi att du väntar minst en dag innan du kör en resultatbaserad utvärdering:
    - Det tar tid att samla in prestandadata. Om du väntar minst en dag ser du till att det finns tillräckligt med prestandadatapunkter innan du kör utvärderingen.
    - När du kör prestandabaserade utvärderingar ska du se till att du profilerar din miljö för utvärderingstiden. Om du till exempel skapar en utvärdering med en prestandavaraktighet inställd på en vecka måste du vänta i minst en vecka efter att du har börjat identifiera dig, för att alla datapunkter ska samlas in. Om du inte gör det får bedömningen inte femstjärniga betyg.
- **Beräkna om utvärderingar**: Eftersom utvärderingar är ögonblicksbilder i tid uppdateras de inte automatiskt med de senaste data. Om du vill uppdatera en utvärdering med de senaste uppgifterna måste du beräkna om den.

Följ dessa metodtips för utvärderingar av servrar som importeras till Azure Migrate via . CSV-fil:

- **Skapa som-är-utvärderingar:** Du kan skapa bedömningar som är direkt när dina datorer visas i Azure Migrate-portalen.
- **Skapa prestandabaserad bedömning:** Detta hjälper till att få en bättre kostnadsuppskattning, särskilt om du har överetablerad serverkapacitet lokalt. Den prestandabaserade bedömningens riktighet beror dock på vilka prestandadata som du har angett för servrarna. 
- **Beräkna om utvärderingar**: Eftersom utvärderingar är ögonblicksbilder i tid uppdateras de inte automatiskt med de senaste data. Om du vill uppdatera en utvärdering med de senaste importerade data måste du beräkna om den.

## <a name="best-practices-for-confidence-ratings"></a>Bästa praxis för förtroendeklassificeringar

När du kör prestationsbaserade bedömningar tilldelas en konfidensgrad från 1-stjärnigt (lägsta) till 5-stjärnigt (högst) till bedömningen. Så här använder du konfidensklassificeringar effektivt:
- Azure Migrate Server Assessment behöver användningsdata för VM CPU/minne.
- För varje disk som är kopplad till den lokala virtuella datorn behöver den läsa/skriva IOPS/dataflöde.
- För varje nätverkskort som är anslutet till den virtuella datorn behöver det nätverket in/ut-data.

Beroende på hur många procent av datapunkter som är tillgängliga för den valda varaktigheten anges konfidensklassificeringen för en bedömning som sammanfattas i följande tabell.

   **Tillgänglighet för datapunkt** | **Säkerhetsomdöme**
   --- | ---
   0 %–20 % | 1 stjärna
   21 %–40 % | 2 stjärnor
   41 %–60 % | 3 stjärnor
   61 %–80 % | 4 stjärnor
   81 %–100 % | 5 stjärnor


## <a name="common-assessment-issues"></a>Vanliga bedömningsfrågor

Så här åtgärdar du några vanliga miljöproblem som påverkar utvärderingar.

###  <a name="out-of-sync-assessments"></a>Bedömningar som inte är synkroniserade

Om du lägger till eller tar bort datorer från en grupp när du har skapat en utvärdering markeras den bedömning du skapade **inte synkroniseras**. Kör utvärderingen igen (**Beräkna om**) för att återspegla gruppändringarna.

### <a name="outdated-assessments"></a>Föråldrade bedömningar

Om det finns lokala ändringar av virtuella datorer som finns i en grupp som har bedömts markeras bedömningen **föråldrad**. Om du vill återspegla ändringarna kör du utvärderingen igen.

### <a name="low-confidence-rating"></a>Lågt konfidensbetyg

En bedömning kanske inte har alla datapunkter av flera skäl:

- Du profilerade inte din miljö för hela den varaktighet för vilken du skapar utvärderingen. Om du till exempel skapar en *resultatbaserad bedömning* med prestandavaraktighet inställd på en vecka måste du vänta i minst en vecka efter att du har påbörjat identifieringen för att alla datapunkter ska samlas in. Du kan alltid klicka på **Beräkna om** för att se den senaste tillämpliga konfidensklassificeringen. Konfidensklassificering är endast tillämplig när du skapar en *prestationsbaserad* bedömning.

- Några virtuella datorer stängdes av under perioden som utvärderingen utfördes. Om några virtuella datorer var avstängda under en viss period, kan Server Assessment inte samla in prestandadata för den perioden.

- Få virtuella datorer skapades efter att identifieringen startades i Server Assessment. Om du till exempel skapar en utvärdering för prestandahistoriken för den senaste månaden, men några virtuella datorer skapades i miljön för en vecka sedan. I detta fall kommer prestandadata för de nya virtuella datorerna inte att vara tillgängliga för hela tidsperioden och säkerhetsomdömet blir lågt.


## <a name="next-steps"></a>Nästa steg

- [Läs om](concepts-assessment-calculation.md) hur utvärderingar beräknas.
- [Läs om](how-to-modify-assessment.md) hur du anpassar en utvärdering.
